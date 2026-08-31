# RFC: Background Job Processing Approach for the Orders Service

## Table of Contents
- [Context](#context)
- [Goals & Non-goals](#goals--non-goals)
- [Options Considered](#options-considered)
- [Tradeoffs](#tradeoffs)
- [Recommendation](#recommendation)
- [Open Questions](#open-questions)

---

## Context

The Orders service currently runs all post-checkout work inline on the HTTP request: invoice generation, warehouse notification, and the confirmation email. Under normal load this adds 400 to 900 ms to checkout, and when the email provider is slow the whole checkout call times out. We need to move this work off the request path.

The service is ASP.NET Core on Azure Container Apps, backed by PostgreSQL, with Azure Service Bus already provisioned for a separate inventory feed. There is no existing background worker, so this decision sets the pattern every other team will copy.

This RFC recommends Option B, a dedicated worker process consuming Azure Service Bus, as the best balance of operational fit and long-term flexibility. The detailed comparison follows.

---

## Goals & Non-goals

### Goals

- Remove invoice, warehouse, and email work from the checkout request path
- At-least-once processing with idempotent handlers and a dead-letter path for poison messages
- Retry with backoff for transient downstream failures
- Job status and failure counts visible in the existing Grafana dashboard
- A pattern other services can reuse without adopting a new datastore

### Non-goals

- Scheduled or cron-style jobs (no current need; revisit if reporting exports land)
- Exactly-once semantics (handlers are being made idempotent instead)
- A general workflow engine with multi-step orchestration and compensation
- Replacing the existing Service Bus inventory feed

---

## Options Considered

### Option A: In-process background queue (`System.Threading.Channels` + `IHostedService`)

**Summary**: Enqueue jobs into an in-memory channel inside the same process that serves HTTP. A hosted service drains the channel on background threads. No infrastructure changes.

**Pros**:
- Smallest change; least code to write
- No new infrastructure, no message broker cost
- Trivially simple local development story

**Cons**:
- Jobs are lost on pod restart or crash, because the queue lives in memory
- Background work competes with request handling for the same CPU and memory
- Scaling the web tier for traffic also scales the workers, and vice versa, with no independent control
- No built-in retry, dead-lettering, or visibility; all of it is hand-rolled

### Option B: Dedicated worker process consuming Azure Service Bus

**Summary**: Checkout publishes a message to an Azure Service Bus queue and returns. A separate worker container (a .NET Worker Service) consumes the queue, runs handlers, and relies on Service Bus for retry, backoff, and dead-lettering. Web and worker tiers scale independently.

**Pros**:
- Durable: messages survive restarts and deploys
- Retry, exponential backoff, and dead-letter queues are native to Service Bus
- Web and worker tiers scale on their own signals (HTTP concurrency vs queue depth)
- Reuses infrastructure and team familiarity from the existing inventory feed
- KEDA scale-to-zero on queue depth is supported on Container Apps

**Cons**:
- New deployable unit to build, release, and monitor
- Local development needs the Service Bus emulator or a shared dev namespace
- Slightly more moving parts than a single process

### Option C: Hangfire with a PostgreSQL storage backend

**Summary**: Add Hangfire to the existing service. Jobs are persisted to PostgreSQL, processed by an in-process Hangfire server, and observed through the bundled dashboard. Optionally split the Hangfire server into its own process later.

**Pros**:
- Durable job storage without a message broker
- Built-in retry, scheduling, and a ready-made dashboard
- Familiar `BackgroundJob.Enqueue` developer ergonomics
- Reuses the PostgreSQL instance already in place

**Cons**:
- Job processing load lands on the primary transactional database (polling and locks)
- Default in-process server couples worker scaling to the web tier unless separated
- Another library with its own schema, migrations, and upgrade cadence
- Diverges from the Service Bus pattern the inventory feed already established

---

## Tradeoffs

| Dimension | Option A: In-process channel | Option B: Worker + Service Bus | Option C: Hangfire + PostgreSQL |
|-----------|------------------------------|--------------------------------|---------------------------------|
| Cost / effort | Lowest: no new infra | Medium: new container, IaC, pipeline | Low to medium: library plus DB schema |
| Durability | None: in-memory, lost on restart | High: broker-backed persistence | High: rows in PostgreSQL |
| Retry / dead-letter | Hand-rolled | Native to Service Bus | Built into Hangfire |
| Scaling independence | None: shares the web tier | Full: separate tier, queue-depth scaling | Partial: only if server is split out |
| Operational load on datastore | None | None (broker is separate) | Adds polling and lock load to primary DB |
| Long-term maintenance | Grows as custom retry/visibility code accretes | Low: managed broker, standard worker pattern | Medium: extra schema and library upgrades |
| Consistency with existing stack | New pattern | Matches existing inventory feed | New pattern, second job system concept |
| Local dev friction | Lowest | Medium: emulator or dev namespace | Low: just needs PostgreSQL |

---

## Recommendation

**Proposed**: Option B, a dedicated worker process consuming Azure Service Bus.

**Rationale**: The two dimensions that matter most here are durability (a lost invoice or warehouse notification is a real customer and finance problem) and scaling independence (email latency spikes must not force the web tier to scale). Option B is strong on both and reuses infrastructure the team already runs. Option A fails the durability bar outright. Option C meets the durability bar but pushes job-processing load onto the primary transactional database and introduces a second, divergent pattern for asynchronous work when Service Bus is already in the stack.

The cost of Option B is one new deployable and a slightly heavier local setup, both one-time and well understood. Neither outweighs losing durability or overloading the primary database.

**Fallback if rejected**: If the team judges a second deployable too costly right now, adopt Option C but split the Hangfire server into its own process from the start, so the only later migration is the storage layer, not the deployment topology.

---

## Open Questions

- Publish to Service Bus inside the checkout DB transaction, or outbox table plus relay? Dual-write risk if former.
- One queue with a type header, or one queue per job kind (invoice, warehouse, email)?
- Dead-letter handling: manual replay tool, or automated redrive after fix?
- Worker in the same repo and pipeline as the API, or its own?
- Message payload: full order snapshot, or just order ID and re-read in the handler?
