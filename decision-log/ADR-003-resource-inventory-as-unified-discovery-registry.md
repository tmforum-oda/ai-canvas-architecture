# ADR-003 — Resource Inventory as the Unified Discovery Registry for AI-Native Canvas

Date: 2026-03-11

⸻
# Status

In progress
⸻

# Context

AI-native systems require dynamic discovery of runtime capabilities across the platform, including:
	•	deployed components
	•	exposed interaction interfaces
	•	agents and their capabilities
	•	tools available through MCP
	•	available models accessible through the AI Gateway

In ODA Canvas, platform resources are defined declaratively using Kubernetes Custom Resource Definitions (CRDs) such as:
	•	Components
	•	ExposedAPI
	•	DependentAPI
	•	AI gateway configuration resources

These resources represent the source of truth for the Canvas control plane.

However, telco systems and external platforms interact with TM Forum ecosystems through standardized Open APIs, not directly through Kubernetes APIs.

Therefore a mechanism is required to expose Canvas resources through a standardized TM Forum discovery interface while preserving Kubernetes as the authoritative configuration layer.

⸻

# Decision

The TMF Resource Inventory API is adopted as the unified discovery registry for AI-native Canvas resources.

Resource Inventory exposes discoverable platform resources including:
    ODA Components
    Interaction Interfaces
    Agents
    Tools
    Models

Resource Inventory does not become the source of truth for these resources.

Instead, Resource Inventory acts as a discovery projection layer, deriving its resource data from Canvas CRDs in the Kubernetes control plane.

⸻

# Source of Truth Architecture

The authoritative configuration of platform resources remains within Canvas CRDs.

Resource Inventory retrieves resource information by querying Canvas control plane objects.

Architecture model:
Canvas CRDs (Source of Truth)
        ↓
Canvas Operators / Control Plane
        ↓
Resource Inventory API
        ↓
Platform Discovery Clients

This approach ensures:
	•	declarative infrastructure management via Kubernetes
	•	standard TM Forum API discovery for external systems
	•	alignment with ODA architecture principles

⸻

# Registry Views

Several specialized registries may appear in architecture diagrams, including:
	•	Agent Registry
	•	Tool Registry
	•	Model Registry

These registries are logical views derived from Resource Inventory, not separate systems.

Architecture:
Resource Inventory
      ↓
Filtered Views
      ↓
Agent Registry
Tool Registry
Model Registry

This approach avoids introducing separate registry services and maintains a single discovery interface.

⸻

# Agent Registry Representation

Agents are represented as components exposing A2A interaction interfaces.

Agents appear in Resource Inventory when:
    apiType = a2a

Resources meeting this criteria are exposed through the Agent Registry view.


⸻

# Tool Registry Representation

Tools are represented as interaction interfaces using the MCP protocol.

Example:
    apiType = mcp

nterfaces meeting this criteria are exposed through the Tool Registry view.

No dedicated Tool CRD is introduced in this architecture phase.

⸻

# Model Registry Representation

Models available within the Canvas environment are defined through the AI Gateway configuration resources.

Specifically:
    AIGatewayConfig CRD

These resources define the models accessible through the platform’s AI Gateway (for example LiteLLM).

Architecture:
AIGatewayConfig CRD
        ↓
Canvas Control Plane
        ↓
Resource Inventory
        ↓
Model Registry View

Resource Inventory therefore acts as the model registry interface, while the configuration of models remains within Canvas declarative resources.


⸻

# Metadata Representation

Resource Inventory stores normalized metadata and references for resources.

It does not store full protocol-native definitions.

Examples:
| Resource | Stored Metadata | External Reference |
|----------|-----------------|--------------------|
| Agent | name, endpoint, interface type | agent card URL |
| Tool | name, endpoint | MCP manifest endpoint |
| Model | name, provider, gateway reference | backend model provider |
| API | endpoint, interface type | OpenAPI definition |

⸻
# Resource Categories

The following resource categories are supported in this architecture phase:
ODAComponent
API
Agent
Tool
Model

⸻
# Relationship Types

The following relationships are supported for resource discovery:
exposes
exposedBy
dependsOn

These relationships allow discovery clients to determine how resources interact within the platform.

Examples include:
- component exposes interaction interface
- component depends on interaction interface
- agent depends on tool
- component depends on model


⸻

# Benefits

This approach provides several advantages:

Unified discovery interface for all AI-native platform resources.

Avoids introducing separate registries for agents, tools, or models.

Maintains Kubernetes CRDs as the declarative source of truth.

Allows external platforms to discover resources through standard TM Forum APIs.

Supports future expansion of the resource taxonomy.

⸻

# Trade-offs

Resource Inventory does not store complete protocol-specific metadata.

Some resource details must be retrieved from external protocol endpoints.

Example:
	•	Agent Card for agents
	•	MCP manifest for tools

⸻

# Future Considerations

Future architecture revisions may evaluate:
	•	deeper integration between Resource Inventory and agent capability metadata
	•	representation of data resources within the inventory model
	•	policy-based filtering of discovery results
	•	extended relationship modeling for complex dependency graphs

⸻