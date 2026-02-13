---
id: process-discovery
title: "AML.T0089: Process Discovery"
sidebar_label: "Process Discovery"
sidebar_position: 90
---

# AML.T0089: Process Discovery

Adversaries may attempt to get information about processes running on a system. Once obtained, this information could be used to gain an understanding of common AI-related software/applications running on systems within the network. Administrator or otherwise elevated access may provide better process details.

Identifying the AI software stack can then lead an adversary to new targets and attack pathways. AI-related software may require application tokens to authenticate with backend services. This provides opportunities for [Credential Access](/tactics/AML.TA0013) and [Lateral Movement](/tactics/AML.TA0015).

In Windows environments, adversaries could obtain details on running processes using the Tasklist utility via cmd or `Get-Process` via PowerShell. Information about processes can also be extracted from the output of Native API calls such as `CreateToolhelp32Snapshot`. In Mac and Linux, this is accomplished with the `ps` command. Adversaries may also opt to enumerate processes via `/proc`.

## Metadata

- **Technique ID:** AML.T0089
- **Created:** 2025-10-27
- **Last Modified:** 2025-11-04
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1057](https://attack.mitre.org/techniques/T1057/)

## Tactics (1)

- [[frameworks/atlas/tactics/discovery|Discovery]]

## Case Studies (1)

- [[frameworks/atlas/case-studies/aikatz-attacking-llm-desktop-applications|AIKatz: Attacking LLM Desktop Applications]]
