---
id: prompt-infiltration-via-public-facing-application
title: "AML.T0093: Prompt Infiltration via Public-Facing Application"
sidebar_label: "Prompt Infiltration via Public-Facing Application"
sidebar_position: 94
---

# AML.T0093: Prompt Infiltration via Public-Facing Application

An adversary may introduce malicious prompts into the victim's system via a public-facing application with the intention of it being ingested by an AI at some point in the future and ultimately having a downstream effect. This may occur when a data source is indexed by a retrieval augmented generation (RAG) system, when a rule triggers an action by an AI agent, or when a user utilizes a large language model (LLM) to interact with the malicious content. The malicious prompts may persist on the victim system for an extended period and could affect multiple users and various AI tools within the victim organization.

Any public-facing application that accepts text input could be a target. This includes email, shared document systems like OneDrive or Google Drive, and service desks or ticketing systems like Jira. This also includes OCR-mediated infiltration where malicious instructions are embedded in images, screenshots, and invoices that are ingested into the system.

Adversaries may perform [Reconnaissance](/tactics/AML.TA0002) to identify public facing applications that are likely monitored by an AI agent or are likely to be indexed by a RAG. They may perform [Discover AI Agent Configuration](/techniques/AML.T0084) to refine their targeting.

## Metadata

- **Technique ID:** AML.T0093
- **Created:** 2025-10-29
- **Last Modified:** 2025-12-18
- **Maturity:** demonstrated

## Tactics (2)

- [[frameworks/atlas/tactics/initial-access|Initial Access]]
- [[frameworks/atlas/tactics/persistence|Persistence]]

## Case Studies (8)

- [[frameworks/atlas/case-studies/achieving-code-execution-in-mathgpt-via-prompt-injection|Achieving Code Execution in MathGPT via Prompt Injection]]
- [[frameworks/atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|Financial Transaction Hijacking with M365 Copilot as an Insider]]
- [[frameworks/atlas/case-studies/google-bard-conversation-exfiltration|Google Bard Conversation Exfiltration]]
- [[frameworks/atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|Data Exfiltration via Agent Tools in Copilot Studio]]
- [[frameworks/atlas/case-studies/planting-instructions-for-delayed-automatic-ai-agent-tool-invocation|Planting Instructions for Delayed Automatic AI Agent Tool Invocation]]
- [[frameworks/atlas/case-studies/living-off-ai-prompt-injection-via-jira-service-management|Living Off AI: Prompt Injection via Jira Service Management]]
- [[frameworks/atlas/case-studies/hacking-chatgpt-s-memories-with-prompt-injection|Hacking ChatGPT’s Memories with Prompt Injection]]
- [[frameworks/atlas/case-studies/data-destruction-via-indirect-prompt-injection-targeting-claude-computer-use|Data Destruction via Indirect Prompt Injection Targeting Claude Computer-Use]]
