---
id: prompt-infiltration-via-public-facing-application
title: "AML.T0093: Prompt Infiltration via Public-Facing Application"
sidebar_label: "Prompt Infiltration via Public-Facing Application"
sidebar_position: 13
---

# AML.T0093: Prompt Infiltration via Public-Facing Application

An adversary may introduce malicious prompts into the victim's system via a public-facing application with the intention of it being ingested by an AI at some point in the future and ultimately having a downstream effect. This may occur when a data source is indexed by a retrieval augmented generation (RAG) system, when a rule triggers an action by an AI agent, or when a user utilizes a large language model (LLM) to interact with the malicious content. The malicious prompts may persist on the victim system for an extended period and could affect multiple users and various AI tools within the victim organization.

Any public-facing application that accepts text input could be a target. This includes email, shared document systems like OneDrive or Google Drive, and service desks or ticketing systems like Jira. This also includes OCR-mediated infiltration where malicious instructions are embedded in images, screenshots, and invoices that are ingested into the system.

Adversaries may perform  to identify public facing applications that are likely monitored by an AI agent or are likely to be indexed by a RAG. They may perform [[atlas/techniques/discovery/discover-ai-agent-configuration/discover-ai-agent-configuration-overview|Discover AI Agent Configuration]] to refine their targeting.

## Metadata

- **Technique ID:** AML.T0093
- **Created:** October 29, 2025
- **Last Modified:** December 18, 2025
- **Maturity:** demonstrated

## Tactics (2)

This technique supports the following tactics:

- 
- 

## Case Studies (7)

The following case studies demonstrate this technique:

### [[atlas/case-studies/achieving-code-execution-in-mathgpt-via-prompt-injection|AML.CS0016: Achieving Code Execution in MathGPT via Prompt Injection]]

This showed that the actor could exploit the prompt injection vulnerability of the GPT-3 model used in the MathGPT application to use as an initial access vector.

### [[atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|AML.CS0026: Financial Transaction Hijacking with M365 Copilot as an Insider]]

The Zenity researchers sent an email to a user at the victim organization containing a malicious payload, exploiting the knowledge that all received emails are ingested into the Copilot RAG database.

### [[atlas/case-studies/google-bard-conversation-exfiltration|AML.CS0029: Google Bard Conversation Exfiltration]]

The researcher shares a Google Doc containing the malicious prompt with the target user. This exploits the fact that Bard Extensions allow Bard to access a user's documents.

### [[atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio]]

The researchers send an email with the malicious prompt to the inbox they suspect may be managed by an AI agent.

### [[atlas/case-studies/planting-instructions-for-delayed-automatic-ai-agent-tool-invocation|AML.CS0038: Planting Instructions for Delayed Automatic AI Agent Tool Invocation]]

The researcher included the malicious prompt as part of the body of a long email sent to the victim.

### [[atlas/case-studies/living-off-ai-prompt-injection-via-jira-service-management|AML.CS0039: Living Off AI: Prompt Injection via Jira Service Management]]

The researchers created a new service ticket containing the malicious prompt on the public Jira Service Management (JSM) portal of the victim identified during reconnaissance.

### [[atlas/case-studies/hacking-chatgpt-s-memories-with-prompt-injection|AML.CS0040: Hacking ChatGPT’s Memories with Prompt Injection]]

The Google Doc was shared with the victim, making it accessible to ChatGPT’s via its Connected App feature.

## References

MITRE Corporation. *Prompt Infiltration via Public-Facing Application (AML.T0093)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0093
