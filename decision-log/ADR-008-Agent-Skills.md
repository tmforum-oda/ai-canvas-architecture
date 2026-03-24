ADR-008-Agent-Skills

Date: 2026-03-24

# Status

Proposed

# Context

Agents can be prompted to behave as intended.  However, in deterministic use cases, Agent behaviours must be predictable and repeatable.  
Anthropic is proposing to centralise and manage the data to enforce predictability and repeatability as "Skills"  (https://agentskills.io/home). 
Other model providers have recognised this construct as valuable, and the idea is a rapidly emerging de facto standard.

This is a separately managed file that is intended to be bound to all agents that share the same capabilities.  Typically, Skills are expected to 
be bound to Agents at implementation, and an upgraded Agent is implemented if there are any changes to the Skill set.

In the AI-native Canvas architecture, the platform must support declarative configuration of Skills used by agentic components.

While Skills can be unique to a context, there is the potential for a standards organisation such as the TM Forum to define reference Skills for Agents that 
are viewed as universally applicable across the industry.