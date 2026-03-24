# ADR-004 — Gateway Architecture for AI-Native Canvas

Date: 2026-03-11

# Status

Proposed

# Context

AI-native workloads introduce multiple runtime interaction patterns within the platform, including:
- Agent-to-Agent communication
- Tool invocation through MCP
- Traditional API interactions
- Model inference requests

These interactions require a runtime mediation layer that provides:
- secure access control
- traffic routing
- policy enforcement
- rate limiting
- protocol mediation

Without a unified mediation layer, direct service-to-service communication would lead to inconsistent governance, fragmented security models, and reduced observability.

Therefore, AI-Native Canvas requires a structured gateway architecture to manage runtime interactions across the platform as well as align it with AI-Native-Blueprint Secure Interactions Framework (GB1087)


# Decision

The architecture introduces two logical gateway classes:
`AI Gateway`
`Interaction Gateway`

These gateways mediate all runtime interactions between platform components and external services.


# Gateway Classes

## AI Gateway

The AI Gateway mediates all interactions between platform workloads and Large Language Models or AI model providers.

The AI Gateway exposes a standardized **OpenAI-compatible API interface** for model consumption.

Responsibilities include:
- model routing
- provider abstraction
- credential mediation
- model access governance
- usage control and quota enforcement
- request logging and observability

The AI Gateway provides a standardized interface for model consumption, allowing components to interact with multiple model providers through a unified interface.

### Agent-to-Model Interaction Interface

All agent-to-model interactions are standardized using the OpenAI API specification.

This interface defines:
- prompt structure
- completion requests
- streaming responses
- tool-calling semantics (where applicable)

The AI Gateway is responsible for translating this standardized interface into provider-specific API calls.

This ensures portability of agents across different model providers and environments.

### LiteLLM Reference Implementation

LiteLLM acts as a reference implementation of the AI Gateway, responsible for mediating access to models hosted across multiple providers, including:

- AWS Bedrock  
- Google Vertex AI  
- OpenAI  
- Self-hosted models  

LiteLLM provides:
- OpenAI-compatible API interface  
- multi-provider routing  
- credential abstraction  
- usage tracking and observability  

This enables components and agents to interact with different model providers through a unified interface.


### AgentCore Reference Implementation

AWS AgentCore is an alternative reference implementation of the AI Gateway, providing a managed, cloud-native capability for model access and agent execution.

AgentCore enables:
- managed access to foundation models within the AWS ecosystem  
- integration with AWS-native services (e.g., Bedrock, IAM, CloudWatch)  
- scalable and secure execution of agent workloads  
- built-in governance, monitoring, and operational controls  

In this model, AgentCore can act as the AI Gateway abstraction layer, exposing standardized model interfaces while leveraging AWS-native capabilities for execution and management.

### Model Source Considerations

Models accessible through the AI Gateway may originate from:
- external model providers (e.g., AWS Bedrock, OpenAI)
- internally managed ML models governed by ML Ops pipelines.

Approved models from ML Ops pipelines may be exposed through the AI Gateway as standardized APIs accessible to components.


## Secure Interactions Gateway

The Interaction Gateway mediates all non-model interactions between components and services.

This includes:
- OpenAPI service calls
- Agent-to-Agent interactions (A2A)
- MCP tool invocation

These interactions may involve different protocols but are governed uniformly through the gateway layer.

Responsibilities include:
- authentication and identity validation
- policy enforcement
- request routing
- rate limiting
- spike control
- protocol mediation
- observability and logging

The Interaction Gateway ensures that all service interactions follow consistent governance and security policies.

Current implementation mappings include:
`MuleSoft Flex Gateway`
`Istio`

These technologies provide the runtime infrastructure required for enforcing interaction policies and routing traffic between components.


### Runtime Interaction Model

Runtime interactions follow the mediation model defined below.
`Component / Agent → Interaction Gateway → Target Service`

For model interactions:
`Component / Agent → AI Gateway → Model Provider`

This model ensures that runtime communication is governed by the platform mediation layer.


### Separation of Discovery and Runtime

Resource discovery and runtime interaction mediation are intentionally separated.

Resource registries provide discovery metadata only and are not involved in runtime request flows.

Architecture principle:
Registries → discovery
Gateways → runtime mediation

Runtime traffic always flows through the appropriate gateway.


## Identity and Security Integration

Identity and access management are enforced through integration between the gateway layer and the identity provider.

Current architecture mapping:
`Keycloak`

Integration pattern:

- Clients (agents, components, external systems) authenticate via Keycloak and obtain access tokens (e.g., JWT).
- Requests to the Interaction Gateway or AI Gateway include these tokens.
- Gateways validate tokens using Keycloak-issued public keys or introspection endpoints.
- Authorization policies are enforced at the gateway level based on:
  - identity
  - roles
  - scopes
  - policy rules

The Secure Interaction Gateway (e.g., MuleSoft Flex Gateway) integrates directly with Keycloak for authentication and authorization enforcement.

This ensures:
- centralized identity management
- consistent policy enforcement across all interaction types
- secure access to APIs, agents, tools, and models

## Gateway Responsibility Boundaries

Gateways are responsible for mediating runtime interactions only.

They do not manage:
- agent lifecycle
- component deployment
- resource registration

These responsibilities remain within the Canvas control plane.

Gateways operate purely in the runtime (data plane), enforcing:
- security
- routing
- policy
- observability

# Benefits

The gateway mediation architecture provides several advantages:

Centralized governance for all runtime interactions.

Consistent security and policy enforcement across interaction types.

Unified control over model access and service interactions.

Clear separation between discovery mechanisms and runtime traffic mediation.


# Trade-offs

Introducing gateway mediation adds an additional layer in runtime request paths.

However, the benefits of centralized governance and observability outweigh the additional infrastructure complexity.


# Future Considerations

Future architecture revisions may evaluate:
- deeper integration between AI Gateway and policy engines
- dynamic traffic routing based on model characteristics
- integration of gateway telemetry into platform observability systems
- additional interaction protocols