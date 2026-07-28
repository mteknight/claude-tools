---
name: create-plan-tdd
description: Create a Technical Design Document at .claude/plans/<name>.md with architecture, phased implementation milestones, risks, testing strategy, and rollout. Use when the user wants to plan an implementation, write a design doc, draft a migration plan, or document a build approach with phases.
---

# Create Plan: TDD

Generate a Technical Design Document at `.claude/plans/<kebab-name>.md` following the TDD pattern.

A TDD is an implementation specification — it documents the chosen approach (not options), breaks work into phased milestones with verification criteria, and captures risks and rollout. Use it when the architecture is broadly settled and the work is "how do we build this."

## Inputs Required

- **Plan name**: kebab-case (becomes filename — e.g., `oauth-migration-plan.md`)
- **Subject**: one-line summary of what is being built
- **Phases**: milestone-bearing chunks of work
- **Time estimations**: only include if user-confirmed (project convention)

## Template

See [tdd-template.md](tdd-template.md) for the full scaffold — TOC, Overview, Architecture, Implementation Phases, Risks, Testing, Rollout, Open Questions.

Copy the template, fill in `{...}` placeholders, drop sections that don't apply (e.g., Rollout for an internal-only tool).

## TDD Pattern: Core Principles

1. **Phases must be verifiable.** Each phase ends at a test passing, a command succeeding, or an observable behavior change. "Refactor module X" is not a phase; "module X passes its new contract tests" is.
2. **Reference existing patterns by file path.** "Follow the pattern in `src/foo/Bar.cs`" beats abstract descriptions. The implementer (human or Claude) mirrors concrete examples.
3. **Embed verification criteria inline.** Test cases and acceptance criteria belong in the phase that delivers them — not in a far-off "Testing Strategy" sidebar.
4. **Lead with the decision, not the journey.** The Overview's first paragraph should state what is being built. Don't make the reader scroll for the headline.
5. **Architecture as constraints, not options.** A TDD documents the chosen approach. If you're weighing options, write an RFC first.

## When to Use a TDD

| Trigger | TDD? |
|---------|------|
| "Plan an implementation" | Yes |
| "Implementation plan", "design doc", "migration plan", "phased plan" | Yes |
| "Record a decision that's already made" | No — use ADR |
| "Should we A or B?" | No — use RFC |
| "Capture rationale for choosing X" | No — use ADR |

## Length and Structure

- TDDs scale with complexity. Small feature: a few sections, one phase. Large migration: many phases, risk table, rollout sequence.
- TOC required for documents >100 lines (so partial reads still see the structure).
- "Old patterns" `<details>` block for deprecated approaches — keeps history without polluting main content.

## Anti-Patterns

| Anti-Pattern | Why it bites | Fix |
|--------------|--------------|-----|
| Phases without verification criteria | No way to know "done" | Each phase ends at a verifiable milestone |
| "Refactor X" as a phase | Vague — refactor what to what? | Restate as observable outcome |
| Abstract architecture descriptions with no file paths | Hard to map to actual code | Cite files by name |
| Multiple options listed | Should have been an RFC | Pick one and document why; or write RFC instead |
| Time estimates added preemptively | Invites pushback on estimates, not approach | Add only after approach agreed |
| Plan in Confluence/Notion only | Claude can't read without fetch + auth | Keep in `.claude/plans/` |
| Plan never updated as scope shifts | Reader follows stale plan | Amend at the top with "What changed" entry |
| Time-sensitive language ("after Aug 2025") | Rots silently | `<details>`-wrapped "Old patterns" section |

## Validation Checklist

- [ ] Filename is kebab-case, ends in `.md`, lives in `.claude/plans/`
- [ ] H1 title matches the filename's subject
- [ ] Table of Contents present (TOC after title, per project convention)
- [ ] Overview leads with the headline decision
- [ ] Each phase has a verification step
- [ ] File paths cited for any "follow this pattern" instruction
- [ ] Open Questions section at the end (concise — sacrifice grammar for concision per project rule)
- [ ] No time estimates unless user-confirmed
- [ ] No "Old patterns" content outside a `<details>` block
- [ ] Forward slashes throughout

## Generation Process

1. **Gather inputs** — plan name, subject, phases, architectural constraints.
2. **Read** [tdd-template.md](tdd-template.md).
3. **Copy template content**, fill placeholders, drop inapplicable sections.
4. **Validate** against the checklist.
5. **Confirm save** — use `AskUserQuestion`: "Save plan to `.claude/plans/<kebab-name>.md`?" with options **Save** and **Cancel**. If cancelled, stop.
6. **Save** to `.claude/plans/<kebab-name>.md`.
7. **Ask to execute** — use `AskUserQuestion`: "Plan saved. Ready to implement?" with options **Execute now** and **Not yet**.

## Output

- Write the plan to `.claude/plans/<kebab-name>.md`.
- If user selects **Execute now**, begin implementation immediately.
- Otherwise, final line of response: `DONE: {path}`
