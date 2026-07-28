# claude-tools

A collection of Claude Code agents and skills built for real engineering work.

Each tool is a single file. Drop it into your project and it works. No dependencies, no configuration, no setup overhead.

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

## Installation

Both tools are single files. Copy them into your project's `.claude` directory.

**Agent:**
```
.claude/agents/adr-generate.md
```

**Skill:**
```
.claude/skills/diagram-generator/SKILL.md
```

If the `.claude/agents` or `.claude/skills` directories do not exist, create them. Claude Code picks them up automatically on the next session.

---

## Sample Output

The examples below were produced by `adr-generate`: 

- [dotnet-stack-for-greenfield-cloud.md](./examples/dotnet-stack-for-greenfield-cloud.md): Implement .NET stack for greenfield projects
- [use-postgresql-as-primary-data-store.md](./examples/use-postgresql-as-primary-data-store.md): MongoDB vs PostgreSQL as a primary data store

The example below was produced by `diagram-generator`:

- [diagrams-sample.md](./examples/diagrams-sample.md): flowchart, pie, and bar diagrams, each as a simple ASCII version and a bigger Mermaid version

---

## More Tools Coming

This repo grows with each engagement. If you find a tool useful, star the repo to follow updates.

If you work with scaling tech companies and want to talk architecture, [connect on LinkedIn](https://go.alexandrefernandes.co.uk/linkedin).
