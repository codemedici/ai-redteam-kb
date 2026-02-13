---
id: drive-by-compromise
title: "AML.T0078: Drive-by Compromise"
sidebar_label: "Drive-by Compromise"
sidebar_position: 79
---

# AML.T0078: Drive-by Compromise

Adversaries may gain access to an AI system through a user visiting a website over the normal course of browsing, or an AI agent retrieving information from the web on behalf of a user. Websites can contain an [LLM Prompt Injection](/techniques/AML.T0051) which, when executed, can change the behavior of the AI model.

The same approach may be used to deliver other types of malicious code that don't target AI directly (See [Drive-by Compromise in ATT&CK](https://attack.mitre.org/techniques/T1189/)).

## Metadata

- **Technique ID:** AML.T0078
- **Created:** 2025-04-16
- **Last Modified:** 2025-04-17
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1189](https://attack.mitre.org/techniques/T1189/)

## Tactics (1)

- [[frameworks/atlas/tactics/initial-access|Initial Access]]

## Case Studies (3)

- [[frameworks/atlas/case-studies/chatgpt-conversation-exfiltration|ChatGPT Conversation Exfiltration]]
- [[frameworks/atlas/case-studies/data-exfiltration-via-an-mcp-server-used-by-cursor|Data Exfiltration via an MCP Server used by Cursor]]
- [[frameworks/atlas/case-studies/openclaw-command-control-via-prompt-injection|OpenClaw Command & Control via Prompt Injection]]
