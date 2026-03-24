# ADR-001 — Agentic Components (ODA Solution Components) as the Primary Deployable Unit

Date: 2026-03-11

# Status

Proposed

# Context

The AI-native extension of the ODA Canvas introduces new architectural capabilities for deploying agentic workloads that interact with:
- other agents (a2a)
- tools (mcp)
- APIs (openapi, prometheus, openmetrics)
- models
- enterprise data systems

These workloads must integrate with the existing ODA Canvas control-plane model, where:
- components are deployed declaratively
- lifecycle is managed through Kubernetes operators
- resource requirements are expressed in the Component Specification

A key architectural decision is whether Agents should be introduced as independent deployable resources, or whether the existing ODA Component lifecycle model should remain the deployment anchor.

Introducing a dedicated Agent resource type would require:
- a new lifecycle model
- additional operator reconciliation logic
- divergence from the current ODA Canvas architecture

At the same time, the architecture must support standardized agent interoperability, including skill discovery and execution using open protocols.

TM Forum has already adopted the A2A Protocol as the open standard for exposing agent skills and capabilities (see [Canvas ADR-019](https://github.com/tmforum-oda/oda-ca-docs/blob/master/Decision-Log/0019-adopt-a2a-protocol-for-agent-skill-exposure.md)).

Therefore, the architecture must support agents while maintaining alignment with the ODA Component deployment model.


# Decision

The architecture retains the ODA Component as the primary deployable lifecycle unit.

Agent functionality is expressed through interaction interfaces exposed or consumed by components.

No standalone Agent CRD is introduced in this phase.

Agents are treated as a logical construct derived from component capabilities and interaction interfaces, rather than as an independently managed control-plane resource.

This ensures that:
- lifecycle management remains unified under the ODA Component model  
- duplication of control-plane abstractions is avoided  
- the architecture remains consistent with Kubernetes-native design principles  

# Definition — Agentic Component (ODA Solution Component)

An Agentic Component (ODA Solution Component) is defined as:

An ODA Component that exposes or consumes AI-native interaction interfaces enabling autonomous or semi-autonomous behavior.

Agentic Components interact with other systems through interaction interfaces such as:
- A2A interfaces
- MCP interfaces
- APIs
- model dependencies

These interfaces define how the component participates in an agentic ecosystem.


# Agent Representation in the Architecture

Agents are represented in the architecture as components exposing A2A interaction interfaces.

Conceptually:
`Agent = Component exposing an A2A interaction interface`

Agent identity is derived from:
- the component identity  
- the exposed A2A interaction interface  
- the published Agent Card (as per A2A protocol)  

There is no separate agent identity resource in the control plane.

## Agent Runtime Realization Patterns

Agentic Components may be realized using different runtime deployment patterns, depending on architectural and operational requirements.

### 1. Native Runtime Pattern

The agent logic is deployed and executed within the Canvas platform as part of the component workload.

- agent runtime is containerized and managed within Kubernetes  
- interaction interfaces (A2A, MCP, APIs) are exposed directly by the component  
- full lifecycle is managed within Canvas  

### 2. Proxy Runtime Pattern

A thin component-proxy is deployed within Canvas to expose standardized interaction interfaces, while the core agent logic executes on an external agent platform.

- component acts as a proxy or façade  
- interaction gateway mediates communication  
- external runtime may include:
  - agent development platforms  
  - reasoning engines  
  - enterprise AI services  

Both patterns conform to this ADR because:

- the control-plane representation remains an ODA Component  
- interaction interfaces remain standardized and discoverable  
- lifecycle management remains anchored in the component model  

The proxy-based runtime exposure pattern is further detailed in:

**ADR-008 — Agent Runtime Realization Patterns**

# Agent Capability Definition

Agent capabilities and dependencies are expressed through:

- component metadata extensions
- exposed interaction interfaces
- dependent interaction interfaces

Agent capabilities are declared through the Component Specification and its interaction interface definitions.


# Alignment with A2A Protocol Adoption in ODA Canvas

This architecture aligns with the previously documented decision in ODA-CA Documentation: [019 — Adopt A2A Protocol as Open Standard for Agent Skill Exposure](https://github.com/tmforum-oda/oda-ca-docs/blob/master/Decision-Log/0019-adopt-a2a-protocol-for-agent-skill-exposure.md)

According to Canvas ADR-019:
- Agents must expose skills through the A2A Protocol
- Agents must publish an Agent Card at:
`       /.well-known/agent-card.json`

- Agent capabilities must be discoverable and interoperable

Under ADR-001 (this ADR), this requirement is satisfied by Agentic Components exposing A2A interaction interfaces that comply with the A2A protocol.

This ensures that:
- TM Forum agents remain interoperable
- the Canvas architecture remains aligned with open standards
- the component deployment model remains unchanged.


# Implications

# Benefits

- maintains alignment with the ODA Canvas architecture  
- avoids introducing additional lifecycle models  
- allows agents to be implemented using the existing component deployment framework  
- ensures compatibility with A2A protocol interoperability requirements  
- simplifies operator implementation and reconciliation  


# Trade-offs

- agents are not independently deployable resources  
- agent identity must be derived from component metadata and exposed interfaces  
- agent discovery depends on interaction interface metadata rather than a dedicated resource type  


# Future Considerations

The working group may evaluate introducing a dedicated Agent CRD in future architecture revisions if:
- agents require independent lifecycle management
- agent orchestration becomes a platform responsibility
- richer agent metadata must be managed by the control plane

The concept of ODA Solution Components may also evolve to formalize AI-native workload types within the ODA architecture.

The proxy-based runtime exposure model is defined separately to allow independent evolution of runtime interaction patterns.

# Out of Scope

This ADR does not define or decide the following topics:

- tool lifecycle
- model onboarding architecture
- registry architecture
- gateway architecture

These topics are addressed in separate ADRs within the architecture.
