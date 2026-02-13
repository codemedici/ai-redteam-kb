---
id: valid-accounts
title: "AML.T0012: Valid Accounts"
sidebar_label: "Valid Accounts"
sidebar_position: 13
---

# AML.T0012: Valid Accounts

Adversaries may obtain and abuse credentials of existing accounts as a means of gaining Initial Access.
Credentials may take the form of usernames and passwords of individual user accounts or API keys that provide access to various AI resources and services.

Compromised credentials may provide access to additional AI artifacts and allow the adversary to perform [Discover AI Artifacts](/techniques/AML.T0007).
Compromised credentials may also grant an adversary increased privileges such as write access to AI artifacts used during development or production.

## Metadata

- **Technique ID:** AML.T0012
- **Created:** 2022-01-24
- **Last Modified:** 2025-12-24
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1078](https://attack.mitre.org/techniques/T1078/)

## Tactics (2)

- [[frameworks/atlas/tactics/initial-access|Initial Access]]
- [[frameworks/atlas/tactics/privilege-escalation|Privilege Escalation]]

## Case Studies (8)

- [[frameworks/atlas/case-studies/microsoft-azure-service-disruption|Microsoft Azure Service Disruption]]
- [[frameworks/atlas/case-studies/face-identification-system-evasion-via-physical-countermeasures|Face Identification System Evasion via Physical Countermeasures]]
- [[frameworks/atlas/case-studies/arbitrary-code-execution-with-google-colab|Arbitrary Code Execution with Google Colab]]
- [[frameworks/atlas/case-studies/llm-jacking|LLM Jacking]]
- [[frameworks/atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|Data Exfiltration from Slack AI via Indirect Prompt Injection]]
- [[frameworks/atlas/case-studies/aikatz-attacking-llm-desktop-applications|AIKatz: Attacking LLM Desktop Applications]]
- [[frameworks/atlas/case-studies/lamehug-malware-leveraging-dynamic-ai-generated-commands|LAMEHUG: Malware Leveraging Dynamic AI-Generated Commands]]
- [[frameworks/atlas/case-studies/openclaw-1-click-remote-code-execution|OpenClaw 1-Click Remote Code Execution]]
