# Adopt .NET as the Default Stack for Greenfield Cloud and Microservices Work

## Table of Contents

- [Status](#status)
- [Context](#context)
- [Decision Drivers](#decision-drivers)
- [Options Considered](#options-considered)
- [Decision](#decision)
  - [Scope and Carve-Outs](#scope-and-carve-outs)
- [Trade-offs](#trade-offs)
  - [Upsides](#upsides)
  - [Downsides and Risks](#downsides-and-risks)
- [Options Analysis](#options-analysis)
  - [.NET on Azure (Chosen)](#net-on-azure-chosen)
  - [Node.js / TypeScript Backend](#nodejs--typescript-backend)
  - [Python Backend](#python-backend)
  - [Java / JVM Stack](#java--jvm-stack)
- [Follow-on Decisions](#follow-on-decisions)
- [Links](#links)

## Status

Accepted  
Date: 2026-05-06  
Deciders: CTO, Lead Engineer

## Context

The engineering organisation already uses .NET in existing production systems and is committing to Azure as its primary cloud platform.  
Greenfield cloud and microservices work has so far lacked a formally documented default stack, leading to ambiguity when new services are scoped.  
This decision formalises the existing direction and extends it as the standard for all new cloud-native and microservices development, while explicitly carving out scope where other technologies remain appropriate.

## Decision Drivers

- Existing team expertise in .NET and the broader Microsoft toolchain
- Strategic alignment with the Microsoft ecosystem, including Azure as the primary cloud target
- Performance characteristics of modern .NET for cloud and microservices workloads
- Continuity with existing .NET systems already in production
- Reduced cognitive load from a single, well-understood backend platform

## Options Considered

- .NET on Azure
- Node.js / TypeScript backend on Azure
- Python backend on Azure
- Java / JVM stack on Azure

## Decision

Adopt .NET as the default backend stack for all greenfield cloud and microservices work, deployed primarily to Azure.  
This option was chosen because it directly satisfies all three primary decision drivers: it leverages existing team expertise, aligns with the Microsoft and Azure ecosystem strategy, and offers strong performance for cloud-native workloads.  
It also preserves continuity with the .NET systems already in use, avoiding fragmentation of the engineering platform.

### Scope and Carve-Outs

This decision applies to greenfield backend services for cloud and microservices workloads. The following carve-outs apply:

- **Frontend**: Angular with TypeScript remains the standard, given its stronger tooling and ecosystem for UI work.
- **Python**: Permitted for edge cases and for agentic / AI tooling, where the Python ecosystem has clear advantages.
- **All other greenfield cloud and microservices backend work**: .NET is the default.

## Trade-offs

### Upsides

- Consolidates backend work on a single, well-supported platform that the team already knows.
- Maximises first-party integration with Azure services, including identity, messaging, observability, and managed compute.
- Modern .NET delivers strong runtime performance and low memory overhead for microservice workloads.
- Reduces hiring and onboarding ambiguity by providing a clear default stack.
- Aligns long-term licensing, tooling, and support relationships with a single vendor ecosystem.

### Downsides and Risks

- Tighter coupling to the Microsoft and Azure ecosystem increases switching costs if strategy changes later.
- Engineers whose primary expertise is in other ecosystems (Node.js, Java, Python) may have a steeper ramp-up.
- Risk of defaulting to .NET in cases where another stack would be a better fit if the carve-outs are not enforced.
- Potential gaps in the .NET ecosystem for niche AI / ML scenarios, mitigated by the Python carve-out.
- Reliance on Microsoft's roadmap and pricing for both the runtime and the cloud platform.

## Options Analysis

### .NET on Azure (Chosen)

- Pros:
  - Strong existing team expertise.
  - First-class integration with Azure services.
  - High performance for cloud and microservices workloads.
  - Continuity with existing production systems.
- Cons:
  - Increased ecosystem lock-in.
  - Less idiomatic for AI / agentic tooling.

### Node.js / TypeScript Backend

- Pros:
  - Shared language with the Angular / TypeScript frontend.
  - Large ecosystem and fast iteration for I/O-bound services.
- Cons:
  - Weaker fit with existing .NET systems and team expertise.
  - Generally lower CPU-bound performance than modern .NET.
  - Less natural alignment with the Microsoft / Azure strategy.
- Reason rejected: Did not satisfy the team expertise and ecosystem alignment drivers as strongly as .NET.

### Python Backend

- Pros:
  - Strongest ecosystem for AI, ML, and agentic tooling.
  - Familiar to many engineers.
- Cons:
  - Performance and concurrency characteristics weaker than .NET for general microservices.
  - Diverges from existing .NET production systems.
- Reason rejected as default: Better suited to the carved-out AI / agentic use cases than as a general backend default.

### Java / JVM Stack

- Pros:
  - Mature ecosystem for large-scale microservices.
  - Strong performance and tooling.
- Cons:
  - No existing footprint in the current platform.
  - Weaker alignment with the Microsoft / Azure strategic direction.
  - Would introduce a parallel platform to maintain alongside existing .NET.
- Reason rejected: No strategic or expertise-based justification to introduce a second backend platform.

## Follow-on Decisions

- Define the baseline .NET version, project template, and reference architecture for new microservices.
- Select standard Azure compute targets (for example App Service, Container Apps, AKS, or Functions) for greenfield services.
- Establish standards for observability, configuration, and secrets management on Azure for .NET services.
- Define the policy and approval path for invoking the Python carve-out for AI / agentic workloads.
- Confirm frontend standards (Angular / TypeScript) in a separate ADR if not already recorded.

## Links

None.
