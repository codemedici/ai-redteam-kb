---
id: ai-agent-tools-permissions-configuration
title: "AML.M0028: AI Agent Tools Permissions Configuration"
sidebar_label: "AI Agent Tools Permissions Configuration"
sidebar_position: 29
type: mitigation
---

# AML.M0028: AI Agent Tools Permissions Configuration

When deploying tools that will be shared across multiple AI agents, it is important to implement robust policies and controls on permissions for the tools. These controls include applying the principle of least privilege along with delegated access, where the tools receive the permissions, identities, and restrictions of the AI agent calling them. These configurations may be implemented either in MCP servers which connect the agents to the tools calling them or, in more complex cases, directly in the configuration files of the tool.

## Metadata

- **Mitigation ID:** AML.M0028
- **Created:** 2025-10-29
- **Last Modified:** 2025-12-23
- **Category:** Technical - Cyber
- **ML Lifecycle:** Deployment

## Techniques (5)

- [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]] — Configuring AI Agent tools with access controls inherited from the user or the AI Agent invoking the tool can limit an adversary's capabilities within a system, including their ability to abuse tool invocations and access sensitive data.
- [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-AI-services-RAG-databases|AML.T0085: Data from AI Services]] — Configuring AI Agent tools with access controls inherited from the user or the AI Agent invoking the tool can limit adversary's access to sensitive data.
- [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-AI-services-agent-tools|AML.T0085.001: AI Agent Tools]] — Configuring AI Agent tools with access controls that are inherited from the user or the AI Agent invoking the tool can limit adversary's access to sensitive data.
- [[frameworks/atlas/techniques/impact/data-destruction-via-ai-agent-tool-invocation|AML.T0101: Data Destruction via AI Agent Tool Invocation]] — Configuring AI Agent tools with access controls inherited from the user or the AI Agent invoking the tool can limit an adversary's capabilities within a system, including their ability to abuse tool invocations to destroy data.
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-agent-tool-invocation|AML.T0086: Exfiltration via AI Agent Tool Invocation]] — Configuring AI Agent tools with access controls inherited from the user or the AI Agent invoking the tool can limit an adversary's capabilities within a system, including their ability to abuse tool invocations and exfiltrate sensitive data.
