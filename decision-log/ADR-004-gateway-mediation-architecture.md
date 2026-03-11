# ADR-004 — Gateway Architecture for AI-Native Canvas

Date: 2026-03-11

⸻
# Status

In progress
⸻

# Context

AI-native workloads introduce multiple runtime interaction patterns within the platform, including:
	•	Agent-to-Agent communication
	•	Tool invocation through MCP
	•	Traditional API interactions
	•	Model inference requests

These interactions require a runtime mediation layer that provides:
	•	secure access control
	•	traffic routing
	•	policy enforcement
	•	rate limiting
	•	protocol mediation

Without a unified mediation layer, direct service-to-service communication would lead to inconsistent governance, fragmented security models, and reduced observability.

Therefore, AI-Native Canvas requires a structured gateway architecture to manage runtime interactions across the platform as well as align it with AI-Native-Blueprint Secure Interactions Framework (GB1087)

⸻

# Decision

The architecture introduces two logical gateway classes:
AI Gateway
Interaction Gateway

These gateways mediate all runtime interactions between platform components and external services.

⸻

# Gateway Classes

# AI Gateway

The AI Gateway mediates all interactions between platform workloads and Large Language Models or AI model providers.

Responsibilities include:
	•	model routing
	•	provider abstraction
	•	credential mediation
	•	model access governance
	•	usage control and quota enforcement
	•	request logging and observability

The AI Gateway provides a standardized interface for model consumption, allowing components to interact with multiple model providers through a unified interface.

Current implementation mapping:
LiteLLM

LiteLLM acts as the AI Gateway responsible for mediating access to models hosted on platforms such as AWS.

# Model Source Considerations

Models accessible through the AI Gateway may originate from:
• external model providers (e.g., AWS Bedrock, OpenAI)
• internally managed ML models governed by ML Ops pipelines.

Approved models from ML Ops pipelines may be exposed through the AI Gateway as standardized APIs accessible to components.

⸻

# Interaction Gateway

The Interaction Gateway mediates all non-model interactions between components and services.

This includes:
	•	OpenAPI service calls
	•	Agent-to-Agent interactions (A2A)
	•	MCP tool invocation

Responsibilities include:
	•	authentication and identity validation
	•	policy enforcement
	•	request routing
	•	rate limiting
	•	spike control
	•	protocol mediation
	•	observability and logging

The Interaction Gateway ensures that all service interactions follow consistent governance and security policies.

Current implementation mappings include:
MuleSoft Flex Gateway
Istio

These technologies provide the runtime infrastructure required for enforcing interaction policies and routing traffic between components.

⸻

# Runtime Interaction Model

Runtime interactions follow the mediation model defined below.
Component / Agent
       ↓
Interaction Gateway
       ↓
Target Service

For model interactions:
Component / Agent
       ↓
AI Gateway
       ↓
Model Provider

This model ensures that runtime communication is governed by the platform mediation layer.

⸻

# Separation of Discovery and Runtime

Resource discovery and runtime interaction mediation are intentionally separated.

Resource registries provide discovery metadata only and are not involved in runtime request flows.

Architecture principle:
Registries → discovery
Gateways → runtime mediation

Runtime traffic always flows through the appropriate gateway.

⸻

# Identity and Security Integration

Identity management within the architecture integrates with the gateway mediation layer.

Current architecture mapping:
Keycloak

Keycloak provides identity and authentication capabilities used by the gateway layer for enforcing access policies.

Gateways leverage the identity infrastructure to:
	•	validate caller identity
	•	apply access control policies
	•	enforce authorization rules

⸻
# Benefits

The gateway mediation architecture provides several advantages:

Centralized governance for all runtime interactions.

Consistent security and policy enforcement across interaction types.

Unified control over model access and service interactions.

Clear separation between discovery mechanisms and runtime traffic mediation.

⸻

# Trade-offs

Introducing gateway mediation adds an additional layer in runtime request paths.

However, the benefits of centralized governance and observability outweigh the additional infrastructure complexity.

⸻

# Future Considerations

Future architecture revisions may evaluate:
	•	deeper integration between AI Gateway and policy engines
	•	dynamic traffic routing based on model characteristics
	•	integration of gateway telemetry into platform observability systems
	•	additional interaction protocols