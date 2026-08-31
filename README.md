# claude-tools

A collection of Claude Code agents and skills built for real engineering work.

An agent is a single file; a skill is a folder with its SKILL.md and any template files. Drop it into your project and it works. No dependencies, no configuration, no setup overhead.

Built and maintained by [Alexandre Fernandes](https://go.alexandrefernandes.co.uk/linkedin), Fractional Solutions Architect at [Lumenworks Digital](https://www.alexandrefernandes.co.uk).

---

## Table of Contents

- [Tools](#tools)
  - [Agents](#agents)
  - [Skills](#skills)
- [Installation](#installation)
- [Sample Output](#sample-output)
- [More Tools Coming](#more-tools-coming)

---

## Tools

### Agents

#### `adr-generate`

Generates an Architecture Decision Record through a structured discovery conversation.

Most teams skip ADRs because writing them from scratch is slow and the result is inconsistent. This agent runs a guided discovery loop, asks the right questions about context, constraints, and trade-offs, then produces a complete, committable ADR document.

It will not generate output until it has enough context. That is intentional.

**Invoke with:**
```
Generate an ADR for the following decision: [describe your decision]
output_path: ./docs/decisions/
```

**Produces:** a structured markdown ADR file following the [MADR](https://adr.github.io/madr/) convention, written to your specified output path.

---

### Skills

#### `diagram-generator`

Automatically selects the right diagram type and generates it when explaining non-trivial concepts, architecture, or processes.

Prefers Mermaid when a renderer is available. Falls back to ASCII for console-only contexts. Covers flowcharts, sequence diagrams, ER diagrams, state machines, and architecture diagrams. Triggered proactively when a diagram would add more clarity than prose alone.

No invocation needed. Once installed, it runs automatically when relevant.

---

#### `create-plan-tdd`

Generates a Technical Design Document (TDD) at `.claude/plans/<name>.md` for implementation work: architecture, phased milestones with verification criteria, risks, testing strategy, and rollout.

Use it once the approach is settled and the question is "how do we build this," not "which approach do we pick" (that's an RFC) or "why did we choose X" (that's an ADR). Each phase ends at a verifiable milestone, not a vague task like "refactor X."

**Invoke with:**
```
/create-plan-tdd
```
or ask Claude to plan an implementation, write a design doc, or draft a migration plan.

**Produces:** a structured markdown TDD, saved only after you confirm, with an offer to start implementation immediately.

---

#### `create-plan-rfc`

Generates a Request for Comments document at `.claude/plans/<name>.md` for decisions that are not settled yet: at least two viable options, a tradeoff table across consistent dimensions, a recommendation, and open questions.

Use it when the question is "which approach do we pick," not "how do we build it" (that's a TDD) or "why did we choose X" (that's an ADR). The recommendation is a forecast, not a verdict; the RFC closes when reviewers agree or the team picks a path.

**Invoke with:**
```
/create-plan-rfc
```
or ask Claude to weigh approaches, compare options, or evaluate tradeoffs before committing.

**Produces:** a structured markdown RFC written to `.claude/plans/`, validated against a checklist (two or more options, a fully filled tradeoff table, an explicit recommendation).

---

## Installation

Copy the files below into your project's `.claude` directory. The agent and the diagram skill are single files; the plan skills ship a `SKILL.md` plus a template file in the same folder.

**Agent:**
```
.claude/agents/adr-generate.md
```

**Skills:**
```
.claude/skills/diagram-generator/SKILL.md
.claude/skills/create-plan-tdd/SKILL.md
.claude/skills/create-plan-tdd/tdd-template.md
.claude/skills/create-plan-rfc/SKILL.md
.claude/skills/create-plan-rfc/rfc-template.md
```

If the `.claude/agents` or `.claude/skills` directories do not exist, create them. Claude Code picks them up automatically on the next session.

---

## Sample Output

The examples below were produced by `adr-generate`: 

- [dotnet-stack-for-greenfield-cloud.md](./examples/dotnet-stack-for-greenfield-cloud.md): Implement .NET stack for greenfield projects
- [use-postgresql-as-primary-data-store.md](./examples/use-postgresql-as-primary-data-store.md): MongoDB vs PostgreSQL as a primary data store

The example below was produced by `diagram-generator`:

- [diagrams-sample.md](./examples/diagrams-sample.md): flowchart, pie, and bar diagrams, each as a simple ASCII version and a bigger Mermaid version

The example below was produced by `create-plan-tdd`:

- [add-rate-limiting-to-public-api.md](./examples/add-rate-limiting-to-public-api.md): phased TDD for adding per-API-key rate limiting to a public API gateway

The example below was produced by `create-plan-rfc`:

- [background-job-processing-approach.md](./examples/background-job-processing-approach.md): RFC weighing three ways to move post-checkout work off the request path, with a tradeoff table and a recommendation

---

## More Tools Coming

This repo grows with each engagement. If you find a tool useful, star the repo to follow updates.

If you work with scaling tech companies and want to talk architecture, [connect on LinkedIn](https://go.alexandrefernandes.co.uk/linkedin).
