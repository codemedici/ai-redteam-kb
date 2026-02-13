---
id: data-from-AI-services-agent-tools
title: "AML.T0085.001: AI Agent Tools"
sidebar_label: "AI Agent Tools"
sidebar_position: 85002
---

# AML.T0085.001: AI Agent Tools

Adversaries may prompt the AI service to invoke various tools the agent has access to. Tools may retrieve data from different APIs or services in an organization.

## Metadata

- **Technique ID:** AML.T0085.001
- **Created:** 2025-09-30
- **Last Modified:** 2025-09-30
- **Maturity:** demonstrated

## Parent Technique

**Parent Technique:** AML.T0085 — Data from AI Services

## Tactics (1)

- [[frameworks/atlas/tactics/collection|Collection]]

## Mitigations (5)

- [[frameworks/atlas/mitigations/ai-telemetry-logging|AML.M0024: AI Telemetry Logging]] — Log requests to AI services to detect malicious queries for data.
- [[frameworks/atlas/mitigations/privileged-ai-agent-permissions-configuration|AML.M0026: Privileged AI Agent Permissions Configuration]] — Configuring privileged AI agents with proper access controls can limit an adversary's ability to collect data from agent tool invocation if the agent is compromised.
- [[frameworks/atlas/mitigations/single-user-ai-agent-permissions-configuration|AML.M0027: Single-User AI Agent Permissions Configuration]] — Configuring AI agents with permissions that are inherited from the user can limit an adversary's ability to collect data from agent tool invocation if the agent is compromised.
- [[frameworks/atlas/mitigations/ai-agent-tools-permissions-configuration|AML.M0028: AI Agent Tools Permissions Configuration]] — Configuring AI Agent tools with access controls that are inherited from the user or the AI Agent invoking the tool can limit adversary's access to sensitive data.
- [[frameworks/atlas/mitigations/segmentation-of-ai-agent-components|AML.M0032: Segmentation of AI Agent Components]] — Segmentation can prevent adversaries from utilizing tools in an agentic workflow to collect sensitive data.

## Case Studies (3)

- [[frameworks/atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|Data Exfiltration via Agent Tools in Copilot Studio]]
- [[frameworks/atlas/case-studies/planting-instructions-for-delayed-automatic-ai-agent-tool-invocation|Planting Instructions for Delayed Automatic AI Agent Tool Invocation]]
- [[frameworks/atlas/case-studies/living-off-ai-prompt-injection-via-jira-service-management|Living Off AI: Prompt Injection via Jira Service Management]]
