---
id: os-credential-dumping
title: "AML.T0090: OS Credential Dumping"
sidebar_label: "OS Credential Dumping"
sidebar_position: 91
---

# AML.T0090: OS Credential Dumping

Adversaries may extract credentials from OS caches, application memory, or other sources on a compromised system. Credentials are often in the form of a hash or clear text, and can include usernames and passwords, application tokens, or other authentication keys.

Credentials can be used to perform [Lateral Movement](/tactics/AML.TA0015) to access other AI services such as AI agents, LLMs, or AI inference APIs. Credentials could also give an adversary access to other software tools and data sources that are part of the AI DevOps lifecycle.

## Metadata

- **Technique ID:** AML.T0090
- **Created:** 2025-10-27
- **Last Modified:** 2025-11-04
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1003](https://attack.mitre.org/techniques/T1003/)

## Tactics (1)

- [[frameworks/atlas/tactics/credential-access|Credential Access]]

## Case Studies (1)

- [[frameworks/atlas/case-studies/aikatz-attacking-llm-desktop-applications|AIKatz: Attacking LLM Desktop Applications]]
