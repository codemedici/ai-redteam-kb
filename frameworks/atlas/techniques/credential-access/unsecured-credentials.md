---
id: unsecured-credentials
title: "AML.T0055: Unsecured Credentials"
sidebar_label: "Unsecured Credentials"
sidebar_position: 56
---

# AML.T0055: Unsecured Credentials

Adversaries may search compromised systems to find and obtain insecurely stored credentials.
These credentials can be stored and/or misplaced in many locations on a system, including plaintext files (e.g. bash history), environment variables, operating system, or application-specific repositories (e.g. Credentials in Registry), or other specialized files/artifacts (e.g. private keys).

## Metadata

- **Technique ID:** AML.T0055
- **Created:** 2023-10-25
- **Last Modified:** 2024-04-29
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1552](https://attack.mitre.org/techniques/T1552/)

## Tactics (1)

- [[frameworks/atlas/tactics/credential-access|Credential Access]]

## Case Studies (6)

- [[frameworks/atlas/case-studies/achieving-code-execution-in-mathgpt-via-prompt-injection|Achieving Code Execution in MathGPT via Prompt Injection]]
- [[frameworks/atlas/case-studies/shadowray|ShadowRay]]
- [[frameworks/atlas/case-studies/organization-confusion-on-hugging-face|Organization Confusion on Hugging Face]]
- [[frameworks/atlas/case-studies/llm-jacking|LLM Jacking]]
- [[frameworks/atlas/case-studies/malware-prototype-with-embedded-prompt-injection|Malware Prototype with Embedded Prompt Injection]]
- [[frameworks/atlas/case-studies/code-to-deploy-destructive-ai-agent-discovered-in-amazon-q-vs-code-extension|Code to Deploy Destructive AI Agent Discovered in Amazon Q VS Code Extension]]
