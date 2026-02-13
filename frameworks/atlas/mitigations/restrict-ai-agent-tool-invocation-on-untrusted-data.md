---
id: restrict-ai-agent-tool-invocation-on-untrusted-data
title: "AML.M0030: Restrict AI Agent Tool Invocation on Untrusted Data"
sidebar_label: "Restrict AI Agent Tool Invocation on Untrusted Data"
sidebar_position: 31
type: mitigation
---

# AML.M0030: Restrict AI Agent Tool Invocation on Untrusted Data

Untrusted data can contain prompt injections that invoke an AI agent's tools, potentially causing confidentiality, integrity or availability violations. It is recommended that tool invocation be restricted or limited when untrusted data enters the LLM's context.

The degree to which tool invocation is restricted may depend on the potential consequences of the action. Consider blocking the automatic invocation of tools or requiring user confirmation once untrusted data enters the LLM's context. For high consequence actions, consider always requiring user confirmation.

## Metadata

- **Mitigation ID:** AML.M0030
- **Created:** 2025-10-29
- **Last Modified:** 2025-12-23
- **Category:** Technical - ML
- **ML Lifecycle:** Deployment

## Techniques (3)

- [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]] — Restricting the automatic tool use when untrusted data is present can prevent adversaries from invoking tools via prompt injections.
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-agent-tool-invocation|AML.T0086: Exfiltration via AI Agent Tool Invocation]] — Restricting the automatic tool use when untrusted data is present can prevent adversaries from invoking tools via prompt injections.
- [[frameworks/atlas/techniques/impact/data-destruction-via-ai-agent-tool-invocation|AML.T0101: Data Destruction via AI Agent Tool Invocation]] — Restricting the automatic tool use when untrusted data is present can prevent adversaries from invoking tools via prompt injections.
