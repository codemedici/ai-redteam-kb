---
id: exfiltration-via-ai-agent-tool-invocation
title: "AML.T0086: Exfiltration via AI Agent Tool Invocation"
sidebar_label: "Exfiltration via AI Agent Tool Invocation"
sidebar_position: 87
---

# AML.T0086: Exfiltration via AI Agent Tool Invocation

Adversaries may use prompts to invoke an agent's tool capable of performing write operations to exfiltrate data. Sensitive information can be encoded into the tool's input parameters and transmitted as part of a seemingly legitimate action. Variants include sending emails, creating or modifying documents, updating CRM records, or even generating media such as images or videos.

## Metadata

- **Technique ID:** AML.T0086
- **Created:** 2025-09-30
- **Last Modified:** 2025-09-30
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/exfiltration|Exfiltration]]

## Mitigations (8)

- [[frameworks/atlas/mitigations/ai-telemetry-logging|AML.M0024: AI Telemetry Logging]] — Log AI agent tool invocations to detect malicious calls.
- [[frameworks/atlas/mitigations/privileged-ai-agent-permissions-configuration|AML.M0026: Privileged AI Agent Permissions Configuration]] — Configuring privileged AI agents with proper access controls for tool use can limit an adversary's ability to abuse tool invocations if the agent is compromised.
- [[frameworks/atlas/mitigations/single-user-ai-agent-permissions-configuration|AML.M0027: Single-User AI Agent Permissions Configuration]] — Configuring AI agents with permissions that are inherited from the user for tool use can limit an adversary's ability to abuse tool invocations if the agent is compromised.
- [[frameworks/atlas/mitigations/ai-agent-tools-permissions-configuration|AML.M0028: AI Agent Tools Permissions Configuration]] — Configuring AI Agent tools with access controls inherited from the user or the AI Agent invoking the tool can limit an adversary's capabilities within a system, including their ability to abuse tool invocations and exfiltrate sensitive data.
- [[frameworks/atlas/mitigations/human-in-the-loop-for-ai-agent-actions|AML.M0029: Human In-the-Loop for AI Agent Actions]] — Requiring user confirmation of AI agent tool invocations can prevent the automatic execution of tools by an adversary.
- [[frameworks/atlas/mitigations/restrict-ai-agent-tool-invocation-on-untrusted-data|AML.M0030: Restrict AI Agent Tool Invocation on Untrusted Data]] — Restricting the automatic tool use when untrusted data is present can prevent adversaries from invoking tools via prompt injections.
- [[frameworks/atlas/mitigations/segmentation-of-ai-agent-components|AML.M0032: Segmentation of AI Agent Components]] — Segmentation can prevent adversaries from utilizing tools in an agentic workflow to compromise sensitive data sources.
- [[frameworks/atlas/mitigations/input-and-output-validation-for-ai-agent-components|AML.M0033: Input and Output Validation for AI Agent Components]] — Validation can prevent adversaries from utilizing tools in an agentic workflow to compromise sensitive data sources.

## Case Studies (3)

- [[frameworks/atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|Data Exfiltration via Agent Tools in Copilot Studio]]
- [[frameworks/atlas/case-studies/living-off-ai-prompt-injection-via-jira-service-management|Living Off AI: Prompt Injection via Jira Service Management]]
- [[frameworks/atlas/case-studies/data-exfiltration-via-an-mcp-server-used-by-cursor|Data Exfiltration via an MCP Server used by Cursor]]
