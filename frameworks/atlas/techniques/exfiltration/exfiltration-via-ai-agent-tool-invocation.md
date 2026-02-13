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

## Case Studies (3)

- [[frameworks/atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|Data Exfiltration via Agent Tools in Copilot Studio]]
- [[frameworks/atlas/case-studies/living-off-ai-prompt-injection-via-jira-service-management|Living Off AI: Prompt Injection via Jira Service Management]]
- [[frameworks/atlas/case-studies/data-exfiltration-via-an-mcp-server-used-by-cursor|Data Exfiltration via an MCP Server used by Cursor]]
