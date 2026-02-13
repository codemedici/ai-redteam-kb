---
id: active-scanning
title: "AML.T0006: Active Scanning"
sidebar_label: "Active Scanning"
sidebar_position: 8
---

# AML.T0006: Active Scanning

An adversary may probe or scan the victim system to gather information for targeting. This is distinct from other reconnaissance techniques that do not involve direct interaction with the victim system.

Adversaries may scan for open ports on a potential victim's network, which can indicate specific services or tools the victim is utilizing. This could include a scan for tools related to AI DevOps or AI services themselves such as public AI chat agents (ex: [Copilot Studio Hunter](https://github.com/mbrg/power-pwn/wiki/Modules:-Copilot-Studio-Hunter-%E2%80%90-Enum)). They can also send emails to organization service addresses and inspect the replies for indicators that an AI agent is managing the inbox.

Information gained from Active Scanning may yield targets that provide opportunities for other forms of reconnaissance such as Search Open Technical Databases, [[frameworks/atlas/techniques/reconnaissance/search-open-ai-vulnerability-analysis|Search Open AI Vulnerability Analysis]], or [[frameworks/atlas/techniques/reconnaissance/gather-rag-indexed-targets|Gather RAG-Indexed Targets]].

## Metadata

- **Technique ID:** AML.T0006
- **Created:** May 13, 2021
- **Last Modified:** November 4, 2025
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1595](https://attack.mitre.org/techniques/T1595/)

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (2)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/shadowray|AML.CS0023: ShadowRay]]

Adversaries can scan for public IP addresses to identify those potentially hosting Ray dashboards. Ray dashboards, by default, run on all network interfaces, which can expose them to the public internet if no other protective mechanisms are in place on the system.

### [[frameworks/atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio]]

The researchers look for support email addresses on the target organization’s website which may be managed by an AI agent. Then, they probe the system by sending emails and looking for indications of agentic AI in automatic replies.

## References

MITRE Corporation. *Active Scanning (AML.T0006)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0006
