---
id: LLM-indirect-prompt-injection
title: "AML.T0051.001: Indirect"
sidebar_label: "Indirect"
sidebar_position: 51002
---

# AML.T0051.001: Indirect

An adversary may inject prompts indirectly via separate data channel ingested by the LLM such as include text or multimedia pulled from databases or websites.
These malicious prompts may be hidden or obfuscated from the user. This type of injection may be used by the adversary to gain a foothold in the system or to target an unwitting user of the system.

## Metadata

- **Technique ID:** AML.T0051.001
- **Created:** 2023-10-25
- **Last Modified:** 2023-10-25
- **Maturity:** demonstrated

## Parent Technique

**Parent Technique:** AML.T0051 — LLM Prompt Injection

## Tactics (1)

- [[frameworks/atlas/tactics/execution|Execution]]

## Mitigations (2)

- [[frameworks/atlas/mitigations/ai-telemetry-logging|AML.M0024: AI Telemetry Logging]] — Telemetry logging can help identify if unsafe prompts have been submitted to the LLM.
- [[frameworks/atlas/mitigations/input-and-output-validation-for-ai-agent-components|AML.M0033: Input and Output Validation for AI Agent Components]] — Validation can prevent adversaries from executing prompt injections that could affect agentic workflows.

## Case Studies (12)

- [[frameworks/atlas/case-studies/indirect-prompt-injection-threats-bing-chat-data-pirate|Indirect Prompt Injection Threats: Bing Chat Data Pirate]]
- [[frameworks/atlas/case-studies/chatgpt-conversation-exfiltration|ChatGPT Conversation Exfiltration]]
- [[frameworks/atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|Financial Transaction Hijacking with M365 Copilot as an Insider]]
- [[frameworks/atlas/case-studies/google-bard-conversation-exfiltration|Google Bard Conversation Exfiltration]]
- [[frameworks/atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|Data Exfiltration from Slack AI via Indirect Prompt Injection]]
- [[frameworks/atlas/case-studies/planting-instructions-for-delayed-automatic-ai-agent-tool-invocation|Planting Instructions for Delayed Automatic AI Agent Tool Invocation]]
- [[frameworks/atlas/case-studies/living-off-ai-prompt-injection-via-jira-service-management|Living Off AI: Prompt Injection via Jira Service Management]]
- [[frameworks/atlas/case-studies/hacking-chatgpt-s-memories-with-prompt-injection|Hacking ChatGPT’s Memories with Prompt Injection]]
- [[frameworks/atlas/case-studies/data-exfiltration-via-an-mcp-server-used-by-cursor|Data Exfiltration via an MCP Server used by Cursor]]
- [[frameworks/atlas/case-studies/data-destruction-via-indirect-prompt-injection-targeting-claude-computer-use|Data Destruction via Indirect Prompt Injection Targeting Claude Computer-Use]]
- [[frameworks/atlas/case-studies/exposed-clawdbot-control-interfaces-leads-to-credential-access-and-execution|Exposed ClawdBot Control Interfaces Leads to Credential Access and Execution]]
- [[frameworks/atlas/case-studies/openclaw-command-control-via-prompt-injection|OpenClaw Command & Control via Prompt Injection]]
