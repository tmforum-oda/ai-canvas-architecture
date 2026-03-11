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

TM Forum has already adopted the A2A Protocol as the open standard for exposing agent skills and capabilities (see ADR-019).

Therefore, the architecture must support agents while maintaining alignment with the ODA Component deployment model.


# Decision

The architecture retains the ODA Component as the primary deployable lifecycle unit.

Agent functionality is expressed through interaction interfaces exposed or consumed by components.

No standalone Agent CRD is introduced in this phase.


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
`    Agent = Component exposing an A2A interaction interface`

Agent identity therefore derives from the component’s exposed interaction interfaces, rather than from a dedicated resource type.


# Agent Capability Definition

Agent capabilities and dependencies are expressed through:

- component metadata extensions
- exposed interaction interfaces
- dependent interaction interfaces

Agent capabilities are declared through the Component Specification and its interaction interface definitions.


# Alignment with A2A Protocol Adoption ([Decision-Log/0019-adopt-a2a-protocol-for-agent-skill-exposure.md](https://github.com/tmforum-oda/oda-ca-docs/blob/9ef52c078d8f0d72a2d419ba702fdcd8efd6d2d6/Decision-Log/0019-adopt-a2a-protocol-for-agent-skill-exposure.md#19-adopt-a2a-protocol-as-open-standard-for-agent-skill-exposure))

This architecture aligns with the previously documented decision:

019 — Adopt A2A Protocol as Open Standard for Agent Skill Exposure

According to ADR-019:
- Agents must expose skills through the A2A Protocol
- Agents must publish an Agent Card at:
`       /.well-known/agent-card.json`

- Agent capabilities must be discoverable and interoperable

Under ADR-001, this requirement is satisfied by Agentic Components exposing A2A interaction interfaces that comply with the A2A protocol.

This ensures that:
- TM Forum agents remain interoperable
- the Canvas architecture remains aligned with open standards
- the component deployment model remains unchanged.


# Implications

# Benefits

Maintains alignment with the ODA Canvas architecture.

Avoids introducing additional lifecycle models.

Allows agents to be implemented using the existing component deployment framework.

Ensures compatibility with A2A protocol interoperability requirements.

Simplifies operator implementation and lifecycle reconciliation.


# Trade-offs

Agents are not independently deployable resources.

Agent identity must be derived from component metadata and exposed interfaces.

Agent discovery depends on interaction interface metadata rather than a dedicated resource type.


# Future Considerations

The working group may evaluate introducing a dedicated Agent CRD in future architecture revisions if:
- agents require independent lifecycle management
- agent orchestration becomes a platform responsibility
- richer agent metadata must be managed by the control plane

The concept of ODA Solution Components may also evolve to formalize AI-native workload types within the ODA architecture.

# Out of Scope

This ADR does not define or decide the following topics:

- tool lifecycle
- model onboarding architecture
- registry architecture
- gateway architecture

These topics are addressed in separate ADRs within the architecture.