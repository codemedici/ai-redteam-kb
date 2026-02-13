---
id: data-destruction-via-ai-agent-tool-invocation
title: "AML.T0101: Data Destruction via AI Agent Tool Invocation"
sidebar_label: "Data Destruction via AI Agent Tool Invocation"
sidebar_position: 102
---

# AML.T0101: Data Destruction via AI Agent Tool Invocation

Adversaries may invoke an AI agent's tool capable of performing mutative operations to perform Data Destruction. Adversaries may destroy data and files on specific systems or in large numbers on a network to interrupt availability to systems, services, and network resources.

## Metadata

- **Technique ID:** AML.T0101
- **Created:** 2025-11-25
- **Last Modified:** 2025-11-25
- **Maturity:** realized

## Tactics (1)

- [[frameworks/atlas/tactics/impact|Impact]]

## Mitigations (6)

- [[frameworks/atlas/mitigations/ai-telemetry-logging|AML.M0024: AI Telemetry Logging]] — Log AI agent tool invocations to detect malicious calls.
- [[frameworks/atlas/mitigations/privileged-ai-agent-permissions-configuration|AML.M0026: Privileged AI Agent Permissions Configuration]] — Configuring privileged AI agents with proper access controls for tool use can limit an adversary's ability to abuse tool invocations if the agent is compromised.
- [[frameworks/atlas/mitigations/single-user-ai-agent-permissions-configuration|AML.M0027: Single-User AI Agent Permissions Configuration]] — Configuring AI agents with permissions that are inherited from the user for tool use can limit an adversary's ability to abuse tool invocations if the agent is compromised.
- [[frameworks/atlas/mitigations/ai-agent-tools-permissions-configuration|AML.M0028: AI Agent Tools Permissions Configuration]] — Configuring AI Agent tools with access controls inherited from the user or the AI Agent invoking the tool can limit an adversary's capabilities within a system, including their ability to abuse tool invocations to destroy data.
- [[frameworks/atlas/mitigations/human-in-the-loop-for-ai-agent-actions|AML.M0029: Human In-the-Loop for AI Agent Actions]] — Requiring user confirmation of AI agent tool invocations can prevent the automatic execution of tools by an adversary.
- [[frameworks/atlas/mitigations/restrict-ai-agent-tool-invocation-on-untrusted-data|AML.M0030: Restrict AI Agent Tool Invocation on Untrusted Data]] — Restricting the automatic tool use when untrusted data is present can prevent adversaries from invoking tools via prompt injections.

## Case Studies (2)

- [[frameworks/atlas/case-studies/data-destruction-via-indirect-prompt-injection-targeting-claude-computer-use|Data Destruction via Indirect Prompt Injection Targeting Claude Computer-Use]]
- [[frameworks/atlas/case-studies/code-to-deploy-destructive-ai-agent-discovered-in-amazon-q-vs-code-extension|Code to Deploy Destructive AI Agent Discovered in Amazon Q VS Code Extension]]
