# Use PostgreSQL as Primary Data Store

**Status:** Accepted  
**Date:** 2026-05-06  
**Deciders:** CTO, Lead Engineer  

---

## Context

The product has grown from a simple CRUD application to a system with complex relational queries across orders, customers, and invoices. The current MongoDB setup was chosen early for schema flexibility, but join-heavy reporting queries are becoming difficult to maintain and slow to execute. A decision was needed on whether to continue with MongoDB or migrate to a relational store before the reporting module ships in Q3.

## Decision Drivers

- Reporting queries require multi-table joins that are unnatural in a document store
- The engineering team has stronger SQL expertise than MongoDB aggregation pipeline experience
- Data relationships are now well-understood and stable; schema flexibility is no longer a priority
- PostgreSQL is already in use for the authentication service, so operational familiarity exists

## Options Considered

- Continue with MongoDB, optimise with aggregation pipelines
- Migrate to PostgreSQL
- Introduce a separate read model (PostgreSQL) alongside MongoDB for reporting only

## Decision

Chosen option: **Migrate to PostgreSQL**

The data model has stabilised and the reporting requirements are inherently relational. Maintaining two stores (Option 3) would add operational complexity without removing the underlying mismatch. The team's existing SQL fluency and the presence of PostgreSQL in the auth service make this the lowest-risk path to a maintainable long-term architecture.

## Trade-offs

### Upsides
- Reporting queries become straightforward SQL
- Unified data store reduces operational surface area
- Team can work fluently without upskilling on aggregation pipelines

### Downsides / Risks
- Migration effort is non-trivial; existing data must be transformed and validated
- Any schema changes post-migration require explicit migrations rather than ad-hoc document updates
- Short-term delivery slowdown during migration window

## Options Analysis

### Continue with MongoDB
- Pro: No migration cost
- Pro: Schema flexibility preserved
- Con: Reporting queries remain complex and slow
- Con: Team fluency gap widens over time

### Migrate to PostgreSQL
- Pro: Natural fit for relational reporting requirements
- Pro: Leverages existing team expertise
- Con: Upfront migration cost
- Con: Stricter schema discipline required going forward

### Read model alongside MongoDB
- Pro: No disruption to existing write path
- Con: Two stores to operate and keep in sync
- Con: Does not resolve the underlying architectural mismatch

## Follow-on Decisions

- Migration strategy: big bang vs phased by entity type
- ORM selection for the .NET service layer

## Links

None.
