---
id: drive-by-compromise
title: "AML.T0078: Drive-by Compromise"
sidebar_label: "Drive-by Compromise"
sidebar_position: 12
---

# AML.T0078: Drive-by Compromise

Adversaries may gain access to an AI system through a user visiting a website over the normal course of browsing, or an AI agent retrieving information from the web on behalf of a user. Websites can contain an [[atlas/techniques/execution/llm-prompt-injection/llm-prompt-injection-overview|LLM Prompt Injection]] which, when executed, can change the behavior of the AI model.

The same approach may be used to deliver other types of malicious code that don't target AI directly (See [Drive-by Compromise in ATT&CK](https://attack.mitre.org/techniques/T1189/)).

## Metadata

- **Technique ID:** AML.T0078
- **Created:** April 16, 2025
- **Last Modified:** April 17, 2025
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1189](https://attack.mitre.org/techniques/T1189/)

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (1)

The following case studies demonstrate this technique:

### [[atlas/case-studies/chatgpt-conversation-exfiltration|AML.CS0021: ChatGPT Conversation Exfiltration]]

When the user makes a query that causes ChatGPT to retrieve the webpage using its `WebPilot` plugin, it ingests the adversary's prompt.

## References

MITRE Corporation. *Drive-by Compromise (AML.T0078)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0078
