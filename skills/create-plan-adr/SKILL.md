---
name: create-plan-adr
description: Create an Architecture Decision Record at .claude/plans/adr-NNNN-<title>.md capturing a single decision with context, the chosen path, and consequences. Use when the user wants to record an architectural decision, document what was chosen and why, or capture rationale for a design choice.
---

# Create Plan: ADR

Generate an Architecture Decision Record at `.claude/plans/adr-NNNN-<kebab-title>.md` following the ADR pattern.

An ADR is a short reference document that captures **one** decision — its context, what was chosen, and the consequences. It is durable, immutable (superseded rather than edited), and scoped to a single page when possible.

## Inputs Required

- **ADR number**: 4-digit sequential (e.g., `0001`, `0042`). Scan `.claude/plans/adr-*.md` for the highest existing number, increment by 1. If none, start at `0001`.
- **Decision title**: short noun phrase (e.g., "Use JWT for session tokens")
- **Context**: forces at play that led to the decision
- **Decision**: declarative statement ("We will...")
- **Consequences**: positive, negative, neutral effects

## Template

See [adr-template.md](adr-template.md) for the full scaffold — Status, Context, Decision, Consequences.

Copy the template, fill in `{...}` placeholders. Keep total length under one page.

## ADR Pattern: Core Principles

1. **One decision per ADR.** Multi-decision documents are TDDs or RFCs; ADRs capture a single, atomic choice.
2. **Short — reference document, not essay.** Industry rule of thumb: ≤1 page. If it grows, you're probably documenting an implementation plan (TDD) or weighing options (RFC).
3. **Immutable once accepted.** Don't edit accepted ADRs. New circumstances → new ADR that supersedes the old one (mark the old one `Superseded by ADR-NNNN`).
4. **Declarative voice.** "We will use JWT with 15-minute expiry" — not "We are considering" or "We might."
5. **Consequences must include negatives.** Every decision has costs. An ADR that only lists positives is incomplete.

## When to Use an ADR

| Trigger | ADR? |
|---------|------|
| "Record this decision" | Yes |
| "Architecture decision", "design choice", "capture rationale" | Yes |
| "We chose X because Y" | Yes |
| "Plan how we'll build this" | No — use TDD |
| "Should we A or B?" | No — use RFC |
| "Long-form design doc" | No — use TDD |

## Filename Convention

ADRs use a numbered prefix for chronological ordering:

```
.claude/plans/adr-0001-use-jwt-for-sessions.md
.claude/plans/adr-0002-postgres-as-primary-store.md
.claude/plans/adr-0003-deprecate-v1-api.md
```

Scan existing `adr-NNNN-*.md` to determine the next number. If none exist, start at `0001`.

## Status Values

- **Proposed** — ADR is drafted but not yet accepted
- **Accepted** — decision is in force
- **Superseded by ADR-MMMM** — newer ADR overrules this one (link to the new ADR)
- **Deprecated** — no longer applicable but no replacement (rare)

## Anti-Patterns

| Anti-Pattern | Why it bites | Fix |
|--------------|--------------|-----|
| Multi-decision ADR | Hard to supersede, hard to link to specific decision | Split into multiple ADRs |
| ADR > 1 page | Not a reference doc anymore | Move detail to a TDD; keep ADR to decision + consequences |
| No Consequences section | Decision rot — future readers don't know costs | Always include positive + negative + neutral |
| Editing an accepted ADR | History rewrite; downstream readers can't trust ADRs | Write a new ADR that supersedes |
| Tentative language in Decision | "We might…" undermines the record | Declarative: "We will…" |
| Missing Status | Reader can't tell if it's in force | Always set Status |
| ADR conflated with implementation plan | TDD masquerading as ADR | Split: ADR records the decision; TDD documents the implementation |

## Validation Checklist

- [ ] Filename matches `adr-NNNN-<kebab-title>.md` and lives in `.claude/plans/`
- [ ] NNNN is sequential after the last existing ADR
- [ ] H1 title matches `ADR-NNNN: {title}`
- [ ] Status section present (Proposed / Accepted / Superseded / Deprecated)
- [ ] Context is 1-3 paragraphs
- [ ] Decision uses declarative voice ("We will…")
- [ ] Consequences include Positive, Negative, and Neutral
- [ ] Total length under one printed page
- [ ] No implementation phases (those belong in a TDD)
- [ ] Forward slashes throughout

## Generation Process

1. **Gather inputs** — decision title, context, decision text, consequences.
2. **Determine ADR number** — scan `.claude/plans/adr-*.md` for the highest existing number, increment by 1. If none, start at `0001`.
3. **Read** [adr-template.md](adr-template.md).
4. **Copy template**, fill placeholders.
5. **Validate** against the checklist (especially length).
6. **Confirm save** — use `AskUserQuestion`: "Save ADR to `.claude/plans/adr-NNNN-<kebab-title>.md`?" with options **Save** and **Cancel**. If cancelled, stop.
7. **Save** to `.claude/plans/adr-NNNN-<kebab-title>.md`.
8. **Ask to execute** — use `AskUserQuestion`: "ADR saved. Ready to proceed?" with options **Execute now** and **Not yet**.

## Output

- Write the ADR to `.claude/plans/adr-NNNN-<kebab-title>.md`.
- If user selects **Execute now**, begin implementation immediately.
- Otherwise, final line of response: `DONE: {path}`
