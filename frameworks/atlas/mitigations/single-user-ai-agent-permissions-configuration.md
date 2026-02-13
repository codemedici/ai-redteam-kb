---
id: single-user-ai-agent-permissions-configuration
title: "AML.M0027: Single-User AI Agent Permissions Configuration"
sidebar_label: "Single-User AI Agent Permissions Configuration"
sidebar_position: 28
type: mitigation
---

# AML.M0027: Single-User AI Agent Permissions Configuration

When deploying an AI agent that acts as a representative of a user and performs actions on their behalf, it is important to implement robust policies and controls on permissions and lifecycle management of the agent. Lifecycle management involves establishing identity, protocols for access management, and decommissioning of the agent when its role is no longer needed. Controls should also include the principle of least privilege and delegated access from the user account. When acting as a representative of a user, the AI agent should not be granted permissions that the user would not be granted within the system or organization.

## Metadata

- **Mitigation ID:** AML.M0027
- **Created:** 2025-10-29
- **Last Modified:** 2025-12-23
- **Category:** Technical - Cyber
- **ML Lifecycle:** Deployment

## Techniques (7)

- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-agent-tool-invocation|AML.T0086: Exfiltration via AI Agent Tool Invocation]] — Configuring AI agents with permissions that are inherited from the user for tool use can limit an adversary's ability to abuse tool invocations if the agent is compromised.
- [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]] — Configuring AI agents with permissions that are inherited from the user for tool use can limit an adversary's ability to abuse tool invocations if the agent is compromised.
- [[frameworks/atlas/techniques/collection/data-from-ai-services|AML.T0085: Data from AI Services]] — Configuring AI agents with permissions that are inherited from the user can limit an adversary's ability to collect data from AI services if the agent is compromised.
- [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-AI-services-RAG-databases|AML.T0085.000: RAG Databases]] — Configuring AI agents with permissions that are inherited from the user can limit an adversary's ability to collect data from RAG Databases if the agent is compromised.
- [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-AI-services-agent-tools|AML.T0085.001: AI Agent Tools]] — Configuring AI agents with permissions that are inherited from the user can limit an adversary's ability to collect data from agent tool invocation if the agent is compromised.
- [[frameworks/atlas/techniques/credential-access/rag-credential-harvesting|AML.T0082: RAG Credential Harvesting]] — Configuring AI agents with permissions that are inherited from the user can limit an adversary's ability to harvest credentials from RAG Databases if the agent is compromised.
- [[frameworks/atlas/techniques/impact/data-destruction-via-ai-agent-tool-invocation|AML.T0101: Data Destruction via AI Agent Tool Invocation]] — Configuring AI agents with permissions that are inherited from the user for tool use can limit an adversary's ability to abuse tool invocations if the agent is compromised.
