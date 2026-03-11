# ADR-002 — Interaction Interface Model for AI-Native ODA Canvas

Date: 2026-03-11

⸻
# Status

Proposed
⸻

# Context

ODA Canvas currently defines component interaction contracts using the following resource definitions:
	•	ExposedAPI
	•	DependentAPI

These resources represent APIs that components either expose or depend upon.

The AI-native extension of ODA Canvas introduces additional interaction patterns required for agentic systems, including:
	•	agent-to-agent communication
	•	tool invocation
	•	model interactions
	•	traditional service APIs
	•	telemetry and metrics interfaces

Introducing separate resource types for each interaction pattern would significantly increase architectural complexity and diverge from the current ODA Canvas resource model.

Therefore a unified abstraction is required that allows the platform to represent all interaction mechanisms consistently while preserving compatibility with the existing Canvas schema.

⸻

# Decision

The architecture introduces the concept of an Interaction Interface as a generalized abstraction representing any interaction endpoint exposed or consumed by a component.

Interaction Interfaces are implemented using the existing Canvas resource types:
    ExposedAPI
    DependentAPI

The interface type is distinguished using the apiType attribute.

This approach allows new interaction mechanisms to be introduced without adding new resource types.


⸻

# Supported Interface Types

The following apiType values are defined for the current architecture phase:
    - openapi
    - prometheus
    - openmetrics
    - a2a
    - mcp


⸻

# Interface Visibility in the Canvas Control Plane

Interaction Interfaces are represented as first-class CRD objects within the Canvas control plane.

These objects can be referenced and inspected using Kubernetes APIs.

Example:
    kubectl get exposedapis -n components

Similarly, dependent interfaces can be inspected through:
    kubectl get dependentapis -n components

This ensures that interaction interfaces are:
	•	declaratively defined
	•	discoverable in the Canvas control plane
	•	manageable through the Kubernetes operator lifecycle

⸻

# Discovery Through Resource Inventory

Interaction Interfaces are exposed through Resource Inventory APIs, providing a standardized TM Forum interface for discovering available resources.

The detailed discovery model and registry architecture are defined in ADR-003.

⸻

# Implications

# Benefits

Provides a unified abstraction for representing multiple interaction types.

Avoids introducing new CRDs for agents, tools, and APIs.

Maintains compatibility with the existing ODA Canvas resource model.

Allows consistent discovery of interaction interfaces across the platform.

⸻

# Trade-offs

Interaction semantics must be interpreted through metadata rather than specialized resource types.

Future interface types may require additional schema extensions.

⸻

# Future Considerations

Future architecture revisions may evaluate introducing specialized resources if additional lifecycle behavior becomes necessary.

Examples include:
	•	tool lifecycle management
	•	agent orchestration
	•	advanced protocol governance

⸻
