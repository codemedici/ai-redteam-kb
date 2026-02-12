---
id: process-discovery
title: "AML.T0089: Process Discovery"
sidebar_label: "Process Discovery"
sidebar_position: 15
---

# AML.T0089: Process Discovery

Adversaries may attempt to get information about processes running on a system. Once obtained, this information could be used to gain an understanding of common AI-related software/applications running on systems within the network. Administrator or otherwise elevated access may provide better process details.

Identifying the AI software stack can then lead an adversary to new targets and attack pathways. AI-related software may require application tokens to authenticate with backend services. This provides opportunities for  and .

In Windows environments, adversaries could obtain details on running processes using the Tasklist utility via cmd or `Get-Process` via PowerShell. Information about processes can also be extracted from the output of Native API calls such as `CreateToolhelp32Snapshot`. In Mac and Linux, this is accomplished with the `ps` command. Adversaries may also opt to enumerate processes via `/proc`.

## Metadata

- **Technique ID:** AML.T0089
- **Created:** October 27, 2025
- **Last Modified:** November 4, 2025
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1057](https://attack.mitre.org/techniques/T1057/)

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (1)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/aikatz-attacking-llm-desktop-applications|AML.CS0036: AIKatz: Attacking LLM Desktop Applications]]

The attacker enumerated all of the processes running on the victim’s machine and identified the processes belonging to LLM desktop applications.

## References

MITRE Corporation. *Process Discovery (AML.T0089)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0089
