# ADR-006 — Model Onboarding and AI Gateway Integration

Date: 2026-03-11

⸻
# Status

In progress
⸻

# Context

AI-native ODA Canvas must support the use of custom and provider-hosted machine learning models by Agentic Components.

Models may originate from:
	•	external provider platforms (for example AWS Bedrock, Google, OpenAI, Groq, Mistral, Cohere)
	•	internally managed ML models produced through enterprise ML Ops pipelines
	•	vendor-managed models onboarded for telco-specific workloads

The architecture must define:
	•	how models are made available inside the Canvas environment
	•	how components reference models
	•	how the AI Gateway is configured
	•	how model discovery works through Resource Inventory
	•	how this aligns with the earlier architecture decision Decision 20 — ML Model Integration via ML Ops Pipeline

At the same time, the architecture must avoid turning Canvas into a full ML training platform.

⸻

# Decision

The architecture adopts the following model:
	1.	Model lifecycle governance is owned by an ML Ops pipeline or equivalent external governance process
	2.	Canvas governs model accessibility inside the Canvas environment
	3.	AIGatewayConfig is the declarative configuration resource used to onboard models into Canvas
	4.	The AI Gateway exposes approved models to components through a standardized runtime interface
	5.	Resource Inventory provides the discovery interface for models available inside Canvas

Thus, the architecture separates:
ML Ops pipeline = model lifecycle governance
Canvas = model accessibility and runtime mediation


⸻

# Model Onboarding Responsibility

Canvas does not perform:
	•	model training
	•	model testing
	•	model evaluation
	•	model approval

These activities belong to the ML Ops pipeline or equivalent external governance process.

Canvas assumes that models referenced for onboarding are already:
validated
approved
governed

Canvas is responsible for making those approved models accessible inside the Canvas environment.

⸻

AIGatewayConfig as the Model Onboarding Resource

AIGatewayConfig is the canonical Canvas configuration resource for onboarding approved models into the Canvas environment.

It defines:
	•	available models
	•	provider metadata
	•	authentication requirements
	•	gateway routing metadata
	•	traffic strategy
	•	observability integration
	•	runtime guardrails

This resource is reconciled by Canvas operators to configure the AI Gateway.

⸻

# Component Model Dependency Declaration

Components declare model dependencies directly.

Example conceptually:
spec:
  dependentAIModels:
    - name: gemini-2.5-pro


The Canvas control plane resolves these component dependencies against models made available through AIGatewayConfig.

Thus:
Component declares dependency
      ↓
Canvas resolves against AIGatewayConfig
      ↓
AI Gateway provides access

This keeps model consumption simple for component developers while allowing the platform to control which models are actually available.


⸻

# Internal and External Model Support

The architecture supports both:
	•	externally hosted models
	•	internally managed models

Examples:
	•	AWS Bedrock models
	•	Google-hosted models
	•	enterprise-hosted custom models
	•	ML Ops-managed internal model APIs

All of these are made accessible through a common pattern:
Agentic Component
      ↓
AI Gateway
      ↓
External provider OR internal model endpoint

This preserves portability and multi-vendor flexibility.

⸻

# Runtime Mediation

Runtime model access is always mediated through the AI Gateway.

Example:
Agentic Component
      ↓
AI Gateway
      ↓
Model provider / model endpoint


⸻

# Benefits

This decision provides several architectural benefits:

Separates ML lifecycle governance from Canvas runtime accessibility.

Keeps Canvas focused on integration and governance rather than training and evaluation.

Supports both internal and external model providers.

Allows Resource Inventory to function as the model discovery interface.

Enables secure provider integration through operator-managed configuration.

⸻

# Trade-offs

Canvas depends on external ML Ops governance for model approval.

Model lifecycle responsibility is distributed across two domains:
- ML Ops pipeline
- Canvas control plane

This introduces coordination requirements between enterprise ML governance and Canvas platform operations.

⸻

# Future Considerations

Future architecture revisions may evaluate:
- richer synchronization between ML Ops pipelines and Canvas onboarding
- more explicit model lifecycle visibility in Canvas
- standardized model approval metadata in Resource Inventory
- dedicated model governance APIs

⸻
