# Add Rate Limiting to Public API

## Table of Contents
- [Overview](#overview)
- [Architecture](#architecture)
- [Implementation Phases](#implementation-phases)
- [Risks & Mitigations](#risks--mitigations)
- [Testing Strategy](#testing-strategy)
- [Rollout](#rollout)
- [Open Questions](#open-questions)

---

## Overview

### Context
The public API has no rate limiting. A single misbehaving client recently issued ~40k requests/minute against `/v1/search`, degrading response times for every other tenant. The API sits behind an ASP.NET Core gateway, backed by Redis for session state, so Redis is already available as shared, low-latency storage for counters.

### Goals
- Enforce a per-API-key request limit on all public endpoints
- Return `429 Too Many Requests` with a `Retry-After` header when a client exceeds its limit
- Make limits configurable per API key tier (free, pro, enterprise) without a redeploy

### Non-goals
- Per-IP limiting (API keys are mandatory for all public routes already)
- Global/service-wide throttling (separate concern, not in scope here)

---

## Architecture

Rate limiting is implemented as gateway middleware using the sliding-window-counter algorithm, with Redis as the shared counter store so limits are enforced consistently across all gateway instances.

Follow the existing middleware registration pattern in `src/Gateway/Program.cs` (`app.UseMiddleware<AuthMiddleware>()`), and the Redis connection pattern already used by `src/Gateway/Session/RedisSessionStore.cs`.

### Component overview

| Component | Role | Notes |
|-----------|------|-------|
| `RateLimitMiddleware` | Intercepts requests, checks/increments counter, short-circuits with 429 | New, `src/Gateway/RateLimit/` |
| Redis (existing cluster) | Stores sliding-window counters keyed by `ratelimit:{apiKey}:{window}` | Reuses `RedisSessionStore` connection multiplexer |
| `RateLimitConfig` | Per-tier limits, loaded from config | New, hot-reloadable via existing `IOptionsMonitor<T>` pattern |

---

## Implementation Phases

<!-- Each phase ends at a verifiable milestone -->

### Phase 1: Counter and middleware skeleton

**Goal**: Requests are counted per API key in Redis; no enforcement yet.

**Tasks**:
1. Add `RateLimitMiddleware` in `src/Gateway/RateLimit/RateLimitMiddleware.cs`, registered after `AuthMiddleware` in `src/Gateway/Program.cs`
2. Implement sliding-window counter increment against Redis, keyed by API key and current window
3. Log counter values without blocking any requests (shadow mode)

**Verification**: Integration test asserts a counter increments in Redis for each authenticated request; existing API tests still pass unchanged.

### Phase 2: Enforcement and tiered limits

**Goal**: Requests over the configured limit receive `429` with `Retry-After`.

**Tasks**:
1. Add `RateLimitConfig` with per-tier thresholds, bound via `IOptionsMonitor<RateLimitConfig>`
2. Short-circuit the pipeline with `429` and `Retry-After` when the counter exceeds the tier limit
3. Add `X-RateLimit-Remaining` and `X-RateLimit-Reset` response headers on all requests

**Verification**: Integration test drives a free-tier key past its limit and asserts `429` + correct headers; a pro-tier key with the same request volume is not throttled.

### Phase 3: Config hot-reload and observability

**Goal**: Ops can change limits without a redeploy, and throttling is visible in monitoring.

**Tasks**:
1. Confirm `RateLimitConfig` reloads on config change (reuse existing `IOptionsMonitor` reload wiring)
2. Emit a metric (`gateway.ratelimit.throttled`) tagged by API key tier
3. Add a Grafana panel to the existing gateway dashboard

**Verification**: Changing the config file updates enforced limits within one polling interval, observed via test; metric appears in the local monitoring stack under load test.

---

## Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Redis latency spikes add overhead to every request | M | M | Reuse existing pooled connection multiplexer; add timeout with fail-open fallback |
| Shadow mode (Phase 1) masks a bug that only surfaces under enforcement | L | M | Compare shadow-mode counts against expected traffic before enabling Phase 2 |
| Fail-open on Redis outage means limits silently stop applying | M | L | Emit a metric on fallback so an outage is visible, not silent |

---

## Testing Strategy

- **Unit tests**: sliding-window counter math, config binding, header generation
- **Integration tests**: end-to-end request flow against a real Redis instance (test containers), covering shadow mode, enforcement, and tier differentiation
- **Acceptance criteria**: a scripted load test exceeding the free-tier limit receives `429` responses once the threshold is crossed, and pro-tier traffic at the same volume does not

---

## Rollout

- Ship Phase 1 (shadow mode) to production first; monitor counter accuracy for one week before enabling enforcement
- Enable enforcement (Phase 2) tier by tier, starting with free tier
- Rollback plan: feature flag disables `RateLimitMiddleware` entirely, falling back to current unthrottled behavior

---

## Open Questions

- Shared limit across API key's multiple gateway regions, or per-region?
- Enterprise tier: hard cap or soft cap with alerting only?
