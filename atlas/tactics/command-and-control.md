---
id: command-and-control
title: "AML.TA0014: Command and Control"
sidebar_label: "Command and Control"
sidebar_position: 14
---

# AML.TA0014: Command and Control

The adversary is trying to communicate with compromised AI systems to control them.

Command and Control consists of techniques that adversaries may use to communicate with systems under their control within a victim network. Adversaries commonly attempt to mimic normal, expected traffic to avoid detection. There are many ways an adversary can establish command and control with various levels of stealth depending on the victim's network structure and defenses.

## Metadata

- **Tactic ID:** AML.TA0014
- **Created:** April 11, 2024
- **Last Modified:** April 11, 2024
- **MITRE ATT&CK Reference:** [TA0011](https://attack.mitre.org/tactics/TA0011/)

## Techniques (2)

The following techniques can be used to achieve this tactic:


| Technique ID | Name | Maturity |
|---|---|---|
| [[atlas/techniques/command-and-control/reverse-shell|AML.T0072]] | Reverse Shell | realized |
| [[atlas/techniques/command-and-control/ai-service-api|AML.T0096]] | AI Service API | realized |


## Case Studies (3)


The following case studies demonstrate this tactic:

- [[atlas/case-studies/organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]
- [[atlas/case-studies/malicious-models-on-hugging-face|AML.CS0031: Malicious Models on Hugging Face]]
- [[atlas/case-studies/sesameop-novel-backdoor-uses-openai-assistants-api-for-command-and-control|AML.CS0042: SesameOp: Novel backdoor uses OpenAI Assistants API for command and control]]


## References

MITRE Corporation. *Command and Control (AML.TA0014)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0014
