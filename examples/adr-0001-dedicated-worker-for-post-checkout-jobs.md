# ADR-0001: Run post-checkout work in a dedicated worker consuming Azure Service Bus

## Status

Accepted

## Context

The Orders service runs invoice generation, warehouse notification, and the confirmation email inline on the checkout request. This adds 400 to 900 ms to checkout under normal load, and a slow email provider times out the whole call. The work has to leave the request path.

Three approaches were weighed in the RFC [Background Job Processing Approach for the Orders Service](./background-job-processing-approach.md): an in-process channel, a dedicated worker on Azure Service Bus, and Hangfire on PostgreSQL. The deciding forces were durability, since a lost invoice is a finance problem, and scaling independence, since email latency must not force the web tier to scale. Azure Service Bus is already provisioned for the inventory feed, and there is no existing background worker, so this choice sets the pattern other teams will copy.

## Decision

We will publish post-checkout work as messages to an Azure Service Bus queue and process them in a separate .NET Worker Service container, scaled on queue depth, with idempotent handlers and Service Bus dead-lettering for poison messages.

## Consequences

### Positive
- Jobs survive restarts and deploys, because messages are held by the broker rather than in process memory.
- Retry, backoff, and dead-lettering come from Service Bus instead of hand-written code.
- Web and worker tiers scale on their own signals: HTTP concurrency and queue depth.
- Asynchronous work follows the same pattern as the existing inventory feed.

### Negative
- One more deployable to build, release, and monitor.
- Local development needs the Service Bus emulator or a shared dev namespace.
- Saving the order and publishing the message are two writes that can disagree. This needs its own decision (outbox or not) before go-live.
- Handlers must be idempotent, because delivery is at least once.

### Neutral
- Checkout returns before the invoice and email exist, so the confirmation page can no longer show the invoice number.
- Hangfire and in-process channels stay out of this service. Teams that already use them elsewhere are not required to migrate.
