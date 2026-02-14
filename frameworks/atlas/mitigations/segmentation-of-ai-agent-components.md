---
id: segmentation-of-ai-agent-components
title: "AML.M0032: Segmentation of AI Agent Components"
sidebar_label: "Segmentation of AI Agent Components"
sidebar_position: 33
type: mitigation
---

# AML.M0032: Segmentation of AI Agent Components

Define security boundaries around agentic tools and data sources with methods such as API access, container isolation, code execution sandboxing, and rate limiting of tool invocation. This restricts untrusted processes or potential compromises from spreading throughout the system.

## Metadata

- **Mitigation ID:** AML.M0032
- **Created:** 2025-11-25
- **Last Modified:** 2025-12-18
- **Category:** Technical - Cyber
- **ML Lifecycle:** Deployment, Business and Data Understanding

## Techniques (6)

- [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]] — Segmentation can prevent adversaries from utilizing tools in an agentic workflow to perform unsafe actions that affect other components.
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-agent-tool-invocation|AML.T0086: Exfiltration via AI Agent Tool Invocation]] — Segmentation can prevent adversaries from utilizing tools in an agentic workflow to compromise sensitive data sources.
- [[frameworks/atlas/techniques/credential-access/ai-agent-tool-credential-harvesting|AML.T0098: AI Agent Tool Credential Harvesting]] — Segmentation can prevent adversaries from utilizing tools in an agentic workflow to harvest credentials.
- [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-AI-services-RAG-databases|AML.T0085: Data from AI Services]] — Segmentation can prevent adversaries from utilizing tools in an agentic workflow to collect sensitive data from AI services.
- [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-AI-services-RAG-databases|AML.T0085.000: RAG Databases]] — Segmentation can prevent adversaries from utilizing tools in an agentic workflow to collect sensitive data from RAG databases.
- [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-AI-services-agent-tools|AML.T0085.001: AI Agent Tools]] — Segmentation can prevent adversaries from utilizing tools in an agentic workflow to collect sensitive data.
