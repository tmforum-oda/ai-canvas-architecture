ADR-007 — Data Resource Architecture and Access Patterns

Date: 2026-03-30

# Status

Proposed

# Context

AI-native workloads rely on data resources as a core capability alongside:
- agentic components
- AI models
- tools and services

In the AI-native Canvas architecture, the platform must support declarative configuration of external data dependencies used by agentic components.

Unlike models and APIs, however, enterprise data platforms typically introduce additional complexities including:
- data governance policies
- catalog and lineage systems
- data platform access controls
- runtime access patterns

As a result, the architecture must define how Canvas integrates with external data systems while preserving flexibility for different deployment environments.


# Current Architectural Understanding

Based on current architecture discussions, the following principles have emerged.

# External Data Platform

Canvas is not responsible for hosting or managing the data platform itself.

Instead, Canvas integrates with external enterprise data platforms.

Example architecture:
Canvas → Data Integration Layer → External Data Platform

For the current reference implementation, the external data platform is:
`Databricks`

Databricks provides capabilities including:
- data lakehouse storage
- catalog management
- data product governance
- analytics and AI pipelines

# Declarative Data Dependency Resource

ADR-005 introduced a new Canvas configuration resource:
`DataResourceConfig`

This resource allows agentic components to declare data resource dependencies in a declarative way.

The resource includes metadata such as:
- data product identity
- storage location
- catalog reference
- data format
- access management metadata

Example structure:
```yaml
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
```
This resource allows the Canvas control plane to understand the data dependencies of a component.

Discussion topic: If agents do not create data products, and if agents only reference and interact with data products via higher level interfaces (catalog / mcp / data platform SDK), are physicalStorage characteristics still relevant? Or should they be abstracted and left to the data provider to manage?

# Data Governance Responsibility

In this architecture, data governance remains the responsibility of the external data platform.

For example:
- Databricks Unity Catalog

provides:
- access control
- role management
- data lineage
- governance policies

Canvas may reference governance metadata such as:
- service accounts
- roles
- access level

but does not manage the governance lifecycle of the data platform itself.


# Runtime Data Access Patterns

Unlike model access and service interactions, the runtime access pattern for data resources is not yet fully standardized.

Two possible runtime interaction patterns have been identified.

# Pattern A — MCP Tool-based Access

In this model, the data platform exposes tools via MCP servers.

Example:
Agent → MCP Tool → Data Platform



# Pattern B — SDK-based Access

In this model, agents access the data platform directly using SDKs or client libraries.

Example:
Agent  → SDK / client library  → Data Platform


# Data Discovery Considerations

At present, DataResourceConfig is primarily used as a configuration resource.

Unlike models or APIs, data resources are not yet represented as Resource Inventory resources.

Possible future approaches include:
- exposing data products through Resource Inventory
- integrating with external catalog systems
- federating discovery with data platform metadata services

This topic remains open for further architecture discussion.


# Open Architectural Questions

The following questions require further discussion. Initial proposals added below.

# Runtime Access Standardization

Proposal:
- Data access should be standardized through hybrid support, primarily through MCP tool interfaces where possible with an alternate option for SDK access. MCP status as industry standard may change and/or platforms may choose not to natively advertise all data assets via MCP for various reasons.
- Any backend data retrieval and/or processing required prior to returning requested data via MCP/SDK is the responsibility of the data platform. Prescribing specific mechanisms for this is out of scope and approaches may differ by data platform. For example, Databricks' implementation may respond to MCP requests via a Genie space which queries a Databricks SQL Warehouse to return relevant data.


# Gateway Mediation

Proposal:
Data access should be mediated through native data platform access layer via service principals. Where this is directly or downstream of interaction gateway layer requires additional discussion.

# Data Discovery Model

Proposal:
Data resources should be discoverable through direct interaction with data provider's catalog.

# Data Product Abstraction

Should the architecture introduce a standardized Data Product abstraction within Canvas?
