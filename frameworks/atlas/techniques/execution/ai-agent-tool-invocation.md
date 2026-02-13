---
id: ai-agent-tool-invocation
title: "AML.T0053: AI Agent Tool Invocation"
sidebar_label: "AI Agent Tool Invocation"
sidebar_position: 54
---

# AML.T0053: AI Agent Tool Invocation

Adversaries may use their access to an AI agent to invoke tools the agent has access to. LLMs are often connected to other services or resources via tools to increase their capabilities. Tools may include integrations with other applications, access to public or private data sources, and the ability to execute code.

This may allow adversaries to execute API calls to integrated applications or services, providing the adversary with increased privileges on the system. Adversaries may take advantage of connected data sources to retrieve sensitive information. They may also use an LLM integrated with a command or script interpreter to execute arbitrary instructions.

AI agents may be configured to have access to tools that are not directly accessible by users. Adversaries may abuse this to gain access to tools they otherwise wouldn't be able to use.

## Metadata

- **Technique ID:** AML.T0053
- **Created:** 2023-10-25
- **Last Modified:** 2025-11-04
- **Maturity:** demonstrated

## Tactics (2)

- [[frameworks/atlas/tactics/execution|Execution]]
- [[frameworks/atlas/tactics/privilege-escalation|Privilege Escalation]]

## Case Studies (11)

- [[frameworks/atlas/case-studies/achieving-code-execution-in-mathgpt-via-prompt-injection|Achieving Code Execution in MathGPT via Prompt Injection]]
- [[frameworks/atlas/case-studies/chatgpt-conversation-exfiltration|ChatGPT Conversation Exfiltration]]
- [[frameworks/atlas/case-studies/morris-ii-worm-rag-based-attack|Morris II Worm: RAG-Based Attack]]
- [[frameworks/atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|Financial Transaction Hijacking with M365 Copilot as an Insider]]
- [[frameworks/atlas/case-studies/planting-instructions-for-delayed-automatic-ai-agent-tool-invocation|Planting Instructions for Delayed Automatic AI Agent Tool Invocation]]
- [[frameworks/atlas/case-studies/living-off-ai-prompt-injection-via-jira-service-management|Living Off AI: Prompt Injection via Jira Service Management]]
- [[frameworks/atlas/case-studies/data-exfiltration-via-an-mcp-server-used-by-cursor|Data Exfiltration via an MCP Server used by Cursor]]
- [[frameworks/atlas/case-studies/data-destruction-via-indirect-prompt-injection-targeting-claude-computer-use|Data Destruction via Indirect Prompt Injection Targeting Claude Computer-Use]]
- [[frameworks/atlas/case-studies/exposed-clawdbot-control-interfaces-leads-to-credential-access-and-execution|Exposed ClawdBot Control Interfaces Leads to Credential Access and Execution]]
- [[frameworks/atlas/case-studies/supply-chain-compromise-via-poisoned-clawdbot-skill|Supply Chain Compromise via Poisoned ClawdBot Skill]]
- [[frameworks/atlas/case-studies/openclaw-command-control-via-prompt-injection|OpenClaw Command & Control via Prompt Injection]]
