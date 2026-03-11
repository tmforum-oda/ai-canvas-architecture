# ADR-005 — AI-Native Configuration Resources for Canvas Control Plane

Date: 2026-03-11

# Status

Proposed

# Context

The AI-native extension of ODA Canvas requires declarative resources that allow the Canvas control plane to configure external AI and data capabilities in a Kubernetes-native way.

The platform must support declarative configuration for:
- model access through the AI Gateway
- data resource access through external data platforms
- policy, authentication, and observability metadata required for platform integration

At the same time, the architecture has already established the following principles:
- ODA Component remains the primary deployable lifecycle unit (ADR-001)
- interaction interfaces are represented using ExposedAPI / DependentAPI resources (ADR-002)
- Resource Inventory provides the unified discovery interface (ADR-003)
- AI Gateway and Interaction Gateway provide runtime mediation (ADR-004)

This creates a need for AI-native configuration resources that are distinct from the deployable component itself, but can still be reconciled by Canvas operators as part of the platform control plane.


# Decision

The architecture introduces the following AI-native configuration resources as Canvas CRDs:
`AIGatewayConfig`
`DataResourceConfig`

These resources are part of the Canvas control-plane declarative model and are reconciled by operators to configure the runtime integration layer.

No separate CRDs are introduced in this phase for:
- Agent
- Tool
- Model


# Resource Model Overview

| Resource | Purpose | 
|----------|---------|
| Component | Primary deployable lifecycle unit |
| ExposedAPI | Exposed interaction interface |
| DependentAPI | Dependent interaction interface |
| AIGatewayConfig | Declarative configuration for AI Gateway model access |
| DataResourceConfig | Declarative configuration for external data resources |

These resources together form the minimum declarative model needed to support AI-native workloads in Canvas.


# AIGatewayConfig

# Purpose

AIGatewayConfig defines the set of AI models and associated gateway policies available to a component or workload within the Canvas environment.

It acts as the canonical declarative configuration resource for model onboarding into Canvas.

It provides the metadata required by Canvas operators to configure:
- AI Gateway integration
- model provider access
- authentication
- guardrails
- traffic strategy
- observability

It also provides the source metadata used later by Resource Inventory to expose discoverable models available in the Canvas environment.


# Architectural Interpretation

Under this architecture:
- AIGatewayConfig defines the subset of models onboarded into the Canvas environment
- these models may be a subset of a broader enterprise model landscape
- models defined here are later exposed through Resource Inventory
- the AI Gateway uses this resource as the declarative configuration source for runtime mediation


# Model Representation Decision

A separate Model CRD is not introduced in this phase.
Instead: Models are declared inside AIGatewayConfig

This avoids introducing duplicate lifecycle resources while still allowing models to be:
- configured declaratively
- governed through the control plane
- discovered via Resource Inventory


# DataResourceConfig

# Purpose

DataResourceConfig defines external data resources that may be used by agentic components.

It provides declarative metadata required by the Canvas control plane to configure integration with external data platforms such as Databricks.

It includes:
- data product identity
- storage metadata
- catalog location
- access management metadata


# Architectural Interpretation

Under this architecture:
- DataResourceConfig provides the declarative data dependency configuration
- it is reconciled by Canvas operators to prepare external data integration
- it does not yet freeze the runtime data access mechanism


# Control Plane Role

These configuration resources are first-class CRD objects in the Canvas control plane.

Examples:
`kubectl get aigatewayconfigs -n ai-gateway`
`kubectl get dataresourceconfigs -n <namespace>`

This ensures that AI-native integrations are:
- declarative
- observable in Kubernetes
- operator-managed
- aligned with the ODA Canvas control-plane model


# Benefits

This decision provides the following benefits:

Introduces the minimum required AI-native configuration resources without over-expanding the CRD model.

Keeps the deployable model simple by retaining Component as the primary workload unit.

Supports declarative configuration of AI and data dependencies in a Kubernetes-native way.

Allows future discovery of models through Resource Inventory without introducing a dedicated model resource immediately.


# Trade-offs

AIGatewayConfig carries both model onboarding and gateway configuration semantics, which may need refinement later.

DataResourceConfig provides declarative dependency modeling, but runtime access behavior remains open.

Future architecture revisions may still decide to introduce richer resource types if lifecycle complexity increases.


# Future Considerations

Future revisions may evaluate:
- separation of model resource and gateway configuration semantics
- richer policy configuration resources
- explicit data product discovery resources
- additional AI-native CRDs as maturity increases
