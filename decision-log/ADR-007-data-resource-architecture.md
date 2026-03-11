ADR-007 — Data Resource Architecture and Access Patterns

Date: 2026-03-11

⸻
# Status

Proposed
⸻

# Context

AI-native workloads rely on data resources as a core capability alongside:
	•	agentic components
	•	AI models
	•	tools and services

In the AI-native Canvas architecture, the platform must support declarative configuration of external data dependencies used by agentic components.

Unlike models and APIs, however, enterprise data platforms typically introduce additional complexities including:
	•	data governance policies
	•	catalog and lineage systems
	•	data platform access controls
	•	runtime access patterns

As a result, the architecture must define how Canvas integrates with external data systems while preserving flexibility for different deployment environments.

⸻

# Current Architectural Understanding

Based on current architecture discussions, the following principles have emerged.

# External Data Platform

Canvas is not responsible for hosting or managing the data platform itself.

Instead, Canvas integrates with external enterprise data platforms.

Example architecture:
Canvas
   ↓
Data Integration Layer
   ↓
External Data Platform

For the current reference implementation, the external data platform is:
Databricks

Databricks provides capabilities including:
	•	data lakehouse storage
	•	catalog management
	•	data product governance
	•	analytics and AI pipelines

⸻

# Declarative Data Dependency Resource

ADR-005 introduced a new Canvas configuration resource:
DataResourceConfig

This resource allows agentic components to declare data resource dependencies in a declarative way.

The resource includes metadata such as:
data product identity
storage location
catalog reference
data format
access management metadata

Example structure:
apiVersion: oda.tmforum.org/v1alpha1
kind: DataResourceConfig
spec:
  dependentDataResources:
    - name: network-events-product
      id: DP-NET-001
      dataProductType: table
      physicalStorage:
        location: s3://bucket/path
        format: iceberg
      catalogRef:
        catalog: network_ops
        schema: alarms
        object: events_silver

This resource allows the Canvas control plane to understand the data dependencies of a component.

⸻

# Data Governance Responsibility

In this architecture, data governance remains the responsibility of the external data platform.

For example:
Databricks Unity Catalog

provides:
	•	access control
	•	role management
	•	data lineage
	•	governance policies

Canvas may reference governance metadata such as:
service accounts
roles
access level

but does not manage the governance lifecycle of the data platform itself.

⸻

# Runtime Data Access Patterns

Unlike model access and service interactions, the runtime access pattern for data resources is not yet fully standardized.

Two possible runtime interaction patterns have been identified.

# Pattern A — MCP Tool-based Access

In this model, the data platform exposes tools via MCP servers.

Example:
Agent
   ↓
MCP Tool
   ↓
Data Platform


⸻

# Pattern B — SDK-based Access

In this model, agents access the data platform directly using SDKs or client libraries.

Example:
Agent
   ↓
SDK / client library
   ↓
Data Platform


⸻

# Data Discovery Considerations

At present, DataResourceConfig is primarily used as a configuration resource.

Unlike models or APIs, data resources are not yet represented as Resource Inventory resources.

Possible future approaches include:
	•	exposing data products through Resource Inventory
	•	integrating with external catalog systems
	•	federating discovery with data platform metadata services

This topic remains open for further architecture discussion.

⸻

# Open Architectural Questions

The following questions require further discussion.

# Runtime Access Standardization

Should data access be standardized through:
MCP tool interfaces
SDK access
hybrid approaches

# Gateway Mediation

Should data access be mediated through:
Interaction Gateway
data platform access layer
direct SDK access

# Data Discovery Model

Should data resources be discoverable through:
Resource Inventory
external data catalogs
federated discovery mechanisms

# Data Product Abstraction

Should the architecture introduce a standardized Data Product abstraction within Canvas?
