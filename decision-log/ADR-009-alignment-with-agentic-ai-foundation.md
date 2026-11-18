# ADR-009 — Alignment with the Agentic AI Foundation (AAIF)

Date: 2026-11-18

# Status

Proposed

# Context

On 9 December 2025 the Linux Foundation announced the **Agentic AI Foundation (AAIF)**, a neutral, open foundation that hosts open protocols and open source projects for agentic AI. It was anchored by three contributions: the **Model Context Protocol (MCP)** from Anthropic, **goose** from Block, and **AGENTS.md** from OpenAI. Its founding platinum members are AWS, Anthropic, Block, Bloomberg, Cloudflare, Google, Microsoft and OpenAI. Its gold members include Cisco, Ericsson, IBM, Okta, Oracle, Salesforce, SAP and Tetrate, and its silver members include Mirantis, Solo.io and SUSE.

In November 2026, AAIF lists these projects (https://aaif.io/projects):

| AAIF project | What it is |
|---|---|
| Model Context Protocol (MCP) | Protocol connecting AI applications to tools, data and applications |
| Agent2Agent (A2A) | Protocol for agents to communicate, collaborate and exchange tasks across platforms |
| AGENTS.md | Open format for guiding coding agents with project-specific instructions |
| Agent Skills | Open format for reusable agent expertise |
| agentgateway | Gateway for connections between agents, models, tools and APIs |
| Agent Router | Envoy-based AI gateway connecting applications to models and MCP tools |
| Open Responses | Shared LLM API format for agents across providers |
| OpenSandbox | General-purpose sandbox platform for AI applications |
| OpenEnv | Open environments for training AI agents |
| goose | Open source, extensible AI agent |

AAIF also runs working groups on Identity & Trust; Observability & Traceability; Security & Privacy; Governance, Risk & Regulatory Alignment; Workflows & Process Integration; Accuracy & Reliability; Agentic Commerce; and Taxonomy & Landscape.

The AI-Native ODA Canvas has already made several decisions that rely on the same protocols and formats: ADR-002 (Interaction Interface Model: OpenAPI, A2A, MCP), ADR-004 (Gateway Mediation Architecture) and ADR-008 (Agent Skills). The Canvas Reference Implementation is Kubernetes-native and follows the Kubernetes Operator Pattern. It already aligns with CNCF projects (Kubernetes, Istio/Envoy, OpenTelemetry), and the decision that ODA Components are independent of the Canvas implementation depends on open, widely adopted standards.

Until now, the Canvas has aligned with these agentic standards case by case, without saying so as a deliberate position. The standards now share a neutral governance home, so the Canvas can align with that home as a whole and say so clearly.

# Decision

1. **The ODA Canvas aligns with the Agentic AI Foundation as its primary source of agentic AI standards.** Where the Canvas needs a protocol, format or interface for agentic AI, it adopts the AAIF-hosted specification by default. Any TM Forum-specific alternative needs a recorded justification in an ADR.
2. **The Canvas signposts this alignment clearly.** It does this through an alignment statement and matrix in the Canvas documentation (see *Signposting* below), maintained as AAIF projects and Canvas capabilities change.
3. **The Canvas states which specification versions it supports.** For each AAIF specification the Canvas implements (MCP and A2A first), the alignment matrix records the supported version(s). Specification changes are tracked the way the Canvas tracks Kubernetes versions.
4. **TM Forum extends AAIF standards rather than forking them.** Telecoms-specific needs, such as TM Forum Open API semantics over MCP, ODA Component identity in A2A Agent Cards, and the Component-level declaration of agent dependencies, are layered on top of the AAIF specifications. Where an extension would be useful beyond telecoms, it is offered upstream through the AAIF contribution process.
5. **Candidate AAIF implementations are evaluated as reference implementations** for the Canvas gateway and runtime roles described below. Each adoption is recorded in its own ADR, or as an update to an existing one (for example ADR-004).

# Where the Canvas already aligns

| AAIF project | Current Canvas alignment | Evidence |
|---|---|---|
| **MCP** | `mcp` is a first-class `apiType` for exposed and dependent APIs in the Component CRD, so Components declare MCP servers and dependencies, and the Canvas operators expose, secure and discover them. | ADR-002; `oda-ca-docs` ADR-0016 (MCP Server for TM Forum Open APIs, *In progress*); `oda-canvas` `charts/oda-crds` Component CRD; `source/tmf-services/MCP_Resource_Inventory`; MCP integration in the PDB Management Operator (TMFOP010); MCP access types in `DataResourceConfig` (TMFOP012) |
| **A2A** | `a2a` is a first-class `apiType` alongside `openapi` and `mcp`, so agentic Components can expose and depend on A2A endpoints. | ADR-002; Component CRD `apiType` enum (`a2a` re-added under oda-canvas#612); delegated agent identity PoC for A2A-to-MCP calls (reference-example-components#78) |
| **AGENTS.md** | The Canvas repository uses AGENTS.md as its single source of AI agent instructions: one at the root and six nested files (`charts/`, `source/`, `source/operators/`, `feature-definition-and-test-kit/`, `usecase-library/`, `canvas-portal/`). `CLAUDE.md` defers to it. | `oda-canvas/AGENTS.md` and nested files |
| **Agent Skills** | ADR-008 adopts Skills as an expectation in agent configuration and proposes TM Forum reference Skills. The Canvas repository ships seven `SKILL.md` skills (for example `create-oda-operator`, `helm-chart-development`, `write-bdd-feature`). | ADR-008; `oda-canvas/skills/`; `oda-agent-skills-marketplace` |
| **Gateways (agentgateway, Agent Router)** | ADR-004 defines an AI Gateway (OpenAI-compatible API; LiteLLM reference implementation) and a Secure Interactions Gateway mediating MCP and A2A traffic. The Canvas already runs Envoy, through Istio, as its service mesh data plane. | ADR-004; ADR-005 (`AIGatewayConfig`); `charts/api-operator-istio` |
| **Observability & Traceability (WG)** | The Canvas observability stack uses OpenTelemetry, and agentic demos trace agent and tool calls (OpenTelemetry with Langfuse export). | `oda-canvas/charts/observability-stack`; TMFOP012 demo agent; TMFOP006 TMFC043 tracing |
| **Identity & Trust (WG)** | The Canvas is working on call-time authorisation and delegation for agentic components, using short-lived, audience-scoped credentials and Keycloak token-exchange delegation. | oda-canvas#606 (moved to this repository); reference-example-components#78 |

# Where the Canvas could also align

| AAIF project or WG | Opportunity | Suggested next step |
|---|---|---|
| **agentgateway / Agent Router** | Candidate reference implementations for the ADR-004 Secure Interactions Gateway (MCP and A2A mediation) and, for Agent Router, the AI Gateway. Both are Envoy-based, which fits the Canvas's existing Envoy/Istio data plane. | Evaluate both against the ADR-004 responsibilities and record the outcome as an update to ADR-004. |
| **Open Responses** | A provider-neutral LLM API format for agents. ADR-004 standardises on an *OpenAI-compatible* interface, which is a de facto vendor API. Open Responses offers a neutrally governed alternative. | Track the specification. When it's stable, make it the AI Gateway's consumer-facing contract, alongside or instead of the OpenAI-compatible interface, and update ADR-004 and ADR-006. |
| **OpenSandbox** | Isolated execution for agentic Components that run generated code or tools (ADR-001). | Evaluate as an optional Canvas capability, installed by its own operator chart and not by default, in line with keeping the base Canvas lean. |
| **OpenEnv** | Standard environments for training and evaluating agents. These could underpin conformance and evaluation of agentic Components. | Explore with the CTK and evaluation work, for example ODA Component scenarios packaged as OpenEnv environments. |
| **MCP and A2A specification versions** | Today the Canvas declares `apiType: mcp` / `a2a` but doesn't state which protocol versions it supports or tests. | Add supported versions to the alignment matrix, and BDD coverage that exercises them. |
| **AGENTS.md and Agent Skills in Canvas-related repositories** | Apply the oda-canvas pattern across `reference-example-components`, `oda-helm-charts` and the `TMFOPnnn` operator repositories, and publish TM Forum reference Skills (ADR-008) in the AAIF Agent Skills format. | Add AGENTS.md to each repository. Publish reference Skills from `oda-agent-skills-marketplace`. |
| **Working groups** | AAIF has no visible telecoms voice beyond vendor members such as Ericsson. TM Forum's work on agent identity (Identity & Trust), tracing across ODA Components (Observability & Traceability), AI governance (Governance, Risk & Regulatory Alignment) and process integration with eTOM and ODA (Workflows & Process Integration) is directly relevant. | Recommend that TM Forum considers AAIF membership or a formal liaison, so Canvas architects can take part in these working groups. This is a TM Forum decision, outside the scope of this ADR. |
| **goose** | No direct architectural role. It could serve as an open reference agent client in demos and tutorials. | No action. Revisit if a reference client is needed. |

# Signposting

Alignment is only useful to adopters if they can see it. The Canvas will:

- add an **"Open standards alignment"** section to the Canvas documentation, with the matrices above and their supported versions;
- reference the relevant AAIF specification in Canvas documentation and in CRD field descriptions wherever an `apiType` or interface maps to one (for example "`mcp`: Model Context Protocol, an AAIF project, version …");
- mention AAIF alignment in the `oda-canvas` README alongside its existing Kubernetes and CNCF positioning;
- review the matrix when AAIF adds or retires projects, and record material changes in this decision log.

# Benefits

- **Interoperability for CSPs and vendors.** Communications service providers and ODA Component vendors can build agentic Components with mainstream, multi-vendor tooling, such as any MCP client or A2A-capable agent framework, without TM Forum-specific adapters.
- **A neutral, credible position.** Aligning with a Linux Foundation project avoids the appearance of favouring one model provider or hyperscaler. AAIF's members span all of them, which matches the vendor neutrality the Canvas already holds for Kubernetes and the service mesh.
- **Less bespoke specification work.** TM Forum can focus on telecoms-specific semantics (Open APIs, ODA Component metadata, eTOM processes) and avoid re-specifying generic agent plumbing.
- **Clear procurement and conformance messages.** "ODA Canvas: aligned with Kubernetes, CNCF and AAIF open standards" is a simple statement buyers and partners can check against their own architectures.
- **An upstream influence route.** Recorded extensions, together with membership or a liaison, give TM Forum a route to bring telecoms requirements (identity, delegation, traceability across operator domains) into the standards the whole industry uses.
- **Easier onboarding for contributors.** AGENTS.md and Agent Skills are already recognised by the AI coding tools contributors use. Signposting them shortens the path to productive contributions to the Canvas.

# Trade-offs

- **Specification churn.** MCP and A2A are evolving quickly. Committing to them means tracking versions and handling breaking changes, as the Canvas already does for the Component specification (N-2) and Kubernetes.
- **Breadth versus maturity.** AAIF projects vary in maturity. Defaulting to AAIF must not mean adopting immature projects in core paths. Each adoption still needs its own ADR.
- **Governance outside TM Forum.** TM Forum doesn't control AAIF's roadmap. Without membership or a liaison, the Canvas can align with these standards but can't influence them.
- **Signposting has a maintenance cost.** The alignment matrix needs an owner and periodic review, or it will drift out of date.

# Future Considerations

- Whether TM Forum should formally join AAIF, or set up a liaison, and how Canvas architects would represent it in working groups.
- An ODA profile of A2A Agent Cards and MCP server metadata that carries ODA Component identity and TM Forum Open API references, offered upstream where generally useful.
- Conformance testing of agentic Components against stated MCP and A2A versions as part of the CTK.
- How ADR-0016 (`oda-ca-docs`) and this log should cross-reference each other, now that both cover MCP.

# References

- Linux Foundation press release, 9 December 2025: https://www.linuxfoundation.org/press/linux-foundation-announces-the-formation-of-the-agentic-ai-foundation
- Agentic AI Foundation: https://aaif.io/ (projects: https://aaif.io/projects)
- ADR-001 Agentic Components; ADR-002 Interaction Interface Model; ADR-004 Gateway Mediation Architecture; ADR-005 AI-Native Configuration Resources; ADR-006 Model Onboarding; ADR-008 Agent Skills (this log)
- `oda-ca-docs` Decision-Log ADR-0016 (MCP Server for TM Forum Open APIs) and ADR-0022 (Modular and Independent Operators)
