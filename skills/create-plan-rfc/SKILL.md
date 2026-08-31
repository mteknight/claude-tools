---
name: create-plan-rfc
description: Create a Request for Comments document at .claude/plans/<name>.md that enumerates multiple options with tradeoffs and a recommendation. Use when the user wants to weigh approaches, explore alternatives, evaluate tradeoffs, or decide between competing designs before committing.
---

# Create Plan: RFC

Generate a Request for Comments document at `.claude/plans/<kebab-name>.md` following the RFC pattern.

An RFC is an option-exploration document — it lists at least two viable approaches with tradeoffs, recommends one, and ends with open questions. Use it when the architecture is not yet settled and the team needs to weigh choices before committing.

## Inputs Required

- **Plan name**: kebab-case (becomes filename — e.g., `auth-token-strategy-rfc.md`)
- **Subject**: one-line summary of the decision being explored
- **Options**: at least two viable approaches with tradeoffs

## Template

See [rfc-template.md](rfc-template.md) for the full scaffold — Context, Goals & Non-goals, Options Considered, Tradeoffs table, Recommendation, Open Questions.

Copy the template, fill in `{...}` placeholders, expand Options to at least two.

## RFC Pattern: Core Principles

1. **At least two real options.** An RFC with one option is a TDD in disguise. If you only have one viable approach, write that as a TDD.
2. **Tradeoffs across consistent dimensions.** The tradeoff table compares options on the same axes (cost, risk, reversibility, maintenance, performance) so reviewers can see the contrast at a glance.
3. **Lead with the recommendation in Context, then justify in the Recommendation section.** Reviewers should know what you're proposing before they reach the detailed comparison.
4. **Recommendation is a forecast, not a decision.** The RFC closes when reviewers agree or the team picks a path. Until then, the recommendation is provisional.
5. **Open Questions matter.** Reviewers engage with explicit uncertainty more than they engage with confident prose. Surface what you're unsure about.

## When to Use an RFC

| Trigger | RFC? |
|---------|------|
| "Should we A or B?" | Yes |
| "Weigh tradeoffs", "evaluate approaches", "compare options" | Yes |
| "Plan an implementation" | No — use TDD |
| "Record what we decided" | No — use ADR |
| "Design doc" | Usually TDD; RFC if multiple architectures are still on the table |

## Async Review Flow

The canonical RFC review process:

1. **Write RFC** — 1-2 days
2. **Async review** — reviewers leave inline comments (2-3 days)
3. **Decision meeting** — only if comments don't resolve (30-60 min)
4. **Convert outcome to ADR(s)** — same day; record what was decided

If your RFC review needs a meeting before comments are exhausted, the RFC needs more options or clearer tradeoffs.

## Length and Structure

- RFCs scale with the decision's complexity. Simple choice between two libraries: short. Multi-quarter architecture: long, with appendix-level detail in sibling files.
- TOC required for documents >100 lines.

## Anti-Patterns

| Anti-Pattern | Why it bites | Fix |
|--------------|--------------|-----|
| One option presented | Looks like a TDD pretending to be open | Add at least one real alternative, even if obviously inferior |
| Tradeoff table with empty cells | Reviewer can't compare | Fill every cell or remove the dimension |
| No recommendation | Reviewers don't know which way to push | Always recommend; "no preference" is rare and must be justified |
| Recommendation buried at the end with no preview | Reviewers waste time on options they wouldn't pick | Mention the recommendation in Context |
| Time estimates added preemptively | Invites argument about estimates, not approach | Add only after approach is agreed |
| RFC never closed (no resulting decision) | Decision rots, work blocked | Convert to ADR or amend with rejection rationale |

## Validation Checklist

- [ ] Filename is kebab-case, ends in `.md`, lives in `.claude/plans/`
- [ ] H1 title starts with `RFC:` (visually distinct from TDDs and ADRs)
- [ ] At least two options in Options Considered
- [ ] Tradeoff table fully filled across consistent dimensions
- [ ] Recommendation states which option and why
- [ ] Open Questions section at the end (concise — sacrifice grammar per project rule)
- [ ] No time estimates unless user-confirmed
- [ ] Forward slashes throughout

## Generation Process

1. **Gather inputs** — plan name, subject, at least two options with tradeoffs.
2. **Read** [rfc-template.md](rfc-template.md).
3. **Copy template**, fill placeholders, expand Options to at least two.
4. **Validate** against the checklist.
5. **Confirm save** — use `AskUserQuestion`: "Save RFC to `.claude/plans/<kebab-name>.md`?" with options **Save** and **Cancel**. If cancelled, stop.
6. **Save** to `.claude/plans/<kebab-name>.md`.
7. **Ask to execute** — use `AskUserQuestion`: "RFC saved. Ready to proceed?" with options **Execute now** and **Not yet**.

## Output

- Write the RFC to `.claude/plans/<kebab-name>.md`.
- If user selects **Execute now**, begin implementation immediately.
- Otherwise, final line of response: `DONE: {path}`
