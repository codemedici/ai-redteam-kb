---
id: privilege-escalation
title: "AML.TA0012: Privilege Escalation"
sidebar_label: "Privilege Escalation"
sidebar_position: 7
---

# AML.TA0012: Privilege Escalation

The adversary is trying to gain higher-level permissions.

Privilege Escalation consists of techniques that adversaries use to gain higher-level permissions on a system or network. Adversaries can often enter and explore a network with unprivileged access but require elevated permissions to follow through on their objectives. Common approaches are to take advantage of system weaknesses, misconfigurations, and vulnerabilities. Examples of elevated access include:
- SYSTEM/root level
- local administrator
- user account with admin-like access
- user accounts with access to specific system or perform specific function

These techniques often overlap with Persistence techniques, as OS features that let an adversary persist can execute in an elevated context.

## Metadata

- **Tactic ID:** AML.TA0012
- **Created:** October 25, 2023
- **Last Modified:** October 25, 2023
- **MITRE ATT&CK Reference:** [TA0004](https://attack.mitre.org/tactics/TA0004/)

## Techniques (3)

The following techniques can be used to achieve this tactic:


- [[atlas/techniques/initial-access/valid-accounts|AML.T0012]] — Valid Accounts (realized)
- [[atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053]] — AI Agent Tool Invocation (demonstrated)
- [[atlas/techniques/privilege-escalation/llm-jailbreak|AML.T0054]] — LLM Jailbreak (demonstrated)


## References

MITRE Corporation. *Privilege Escalation (AML.TA0012)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0012
