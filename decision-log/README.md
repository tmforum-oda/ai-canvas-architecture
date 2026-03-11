# Architecture Decision Log

This folder contains a log of architectural decisions made on this project, including their status, context, the decision and the known consequences. We will add to this log through the development of ODA-CA and version control all the decisions.

There is a useful guide at  
https://microsoft.github.io/code-with-engineering-playbook/design/design-reviews/decision-log/

and the original article is at  
https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions

The purpose of maintaining this Architecture Decision Log is to:

- Provide a transparent record of architectural decisions made during the development of **AI-Native ODA Canvas**
- Capture the context and rationale behind design choices
- Allow working group members to review, comment, and evolve the architecture
- Maintain architectural continuity as the platform evolves

Each Architecture Decision Record (ADR) captures:

- **Context** – The architectural problem or decision point
- **Decision** – The architectural choice made
- **Consequences** – Implications of the decision
- **Status** – Current lifecycle state of the decision

Typical ADR status values include:

- **Proposed** – Decision is under discussion
- **Proposed** – Decision is agreed and adopted
- **Superseded** – Decision has been replaced by another ADR
- **Deprecated** – Decision is no longer applicable

---

# Architecture Decision Index

| Decision | Date | Status | Doc reference | Made By |
|----------|-------|-------|---------------|---------|
| ADR-001 – Agentic Components as Primary Deployable Unit | 2026-03-10 | Proposed | ADR-001-agentic-components.md | Project Foundation |
| ADR-002 – Interaction Interface Model (OpenAPI, A2A, MCP) | 2026-03-10 | Proposed | ADR-002-interaction-interface-model.md | Project Foundation |
| ADR-003 – Resource Inventory as Unified Discovery Registry | 2026-03-10 | Proposed | ADR-003-resource-inventory-registry.md | Project Foundation |
| ADR-004 – Gateway Mediation Architecture (AI Gateway + Interaction Gateway) | 2026-03-10 | Proposed | ADR-004-gateway-architecture.md | Project Foundation |
| ADR-005 – AI-Native Configuration Resources (AIGatewayConfig, DataResourceConfig) | 2026-03-10 | Proposed | ADR-005-ai-native-config-resources.md | Project Foundation |
| ADR-006 – Model Onboarding and AI Gateway Integration | 2026-03-10 | Proposed | ADR-006-model-onboarding.md | Project Foundation |
| ADR-007 – Data Resource Architecture and Access Patterns | 2026-03-10 | Proposed | ADR-007-data-resource-architecture.md | Project Foundation |

---

# Architecture Scope

These ADRs collectively define the architectural foundation of **AI-Native ODA Canvas**, including:

- Agentic component architecture
- Interaction interface model
- Gateway architecture
- Model onboarding and AI gateway integration
- Data resource configuration
- Discovery and registry model
- Control plane resource model

The architecture supports the following **core interaction patterns**:

- **Agent → Model**
- **Agent → Tool**
- **Agent → Agent**

and integrates with ecosystem platforms including:

- AI model providers (AWS Bedrock, OpenAI, Google, etc.)
- Data platforms (Databricks)
- Gateway technologies (MuleSoft / Istio)
- AI gateways (LiteLLM)

---

# Contributing

Architecture decisions are expected to evolve as the platform matures.

Working group members are encouraged to:

- Propose new ADRs when introducing architectural changes
- Discuss and review proposed ADRs through pull requests
- Update ADR status when decisions are Proposed or superseded

When creating a new ADR:

1. Copy the ADR template
2. Assign the next sequential ADR number
3. Document context, decision, and consequences
4. Submit a pull request for discussion

---

# Related Architecture Artifacts

The ADRs in this repository are complemented by the following architecture artifacts:

- **Layered Architecture Diagram**
- **Control Plane Architecture**
- **Runtime Interaction Architecture**
- **Agent Ecosystem Architecture**

Together these artifacts provide a complete architectural view of the **AI-Native Canvas platform**.