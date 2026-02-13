---
id: active-scanning
title: "AML.T0006: Active Scanning"
sidebar_label: "Active Scanning"
sidebar_position: 7
---

# AML.T0006: Active Scanning

An adversary may probe or scan the victim system to gather information for targeting. This is distinct from other reconnaissance techniques that do not involve direct interaction with the victim system.

Adversaries may scan for open ports on a potential victim's network, which can indicate specific services or tools the victim is utilizing. This could include a scan for tools related to AI DevOps or AI services themselves such as public AI chat agents (ex: [Copilot Studio Hunter](https://github.com/mbrg/power-pwn/wiki/Modules:-Copilot-Studio-Hunter-%E2%80%90-Enum)). They can also send emails to organization service addresses and inspect the replies for indicators that an AI agent is managing the inbox.

Information gained from Active Scanning may yield targets that provide opportunities for other forms of reconnaissance such as [Search Open Technical Databases](/techniques/AML.T0000), [Search Open AI Vulnerability Analysis](/techniques/AML.T0001), or [Gather RAG-Indexed Targets](/techniques/AML.T0064).

## Metadata

- **Technique ID:** AML.T0006
- **Created:** 2021-05-13
- **Last Modified:** 2025-11-04
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1595](https://attack.mitre.org/techniques/T1595/)

## Tactics (1)

- [[frameworks/atlas/tactics/reconnaissance|Reconnaissance]]

## Case Studies (2)

- [[frameworks/atlas/case-studies/shadowray|ShadowRay]]
- [[frameworks/atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|Data Exfiltration via Agent Tools in Copilot Studio]]
