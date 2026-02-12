---
id: lateral-movement
title: "AML.TA0015: Lateral Movement"
sidebar_label: "Lateral Movement"
sidebar_position: 11
---

# AML.TA0015: Lateral Movement

The adversary is trying to move through your AI environment.

Lateral Movement consists of techniques that adversaries may use to gain access to and control other systems or components in the environment. Adversaries may pivot towards AI Ops infrastructure such as model registries, experiment trackers, vector databases, notebooks, or training pipelines. As the adversary moves through the environment, they may discover means of accessing additional AI-related tools, services, or applications. AI agents may also be a valuable target as they commonly have more permissions than standard user accounts on the system.

## Metadata

- **Tactic ID:** AML.TA0015
- **Created:** October 27, 2025
- **Last Modified:** November 5, 2025
- **MITRE ATT&CK Reference:** [TA0008](https://attack.mitre.org/tactics/TA0008/)

## Techniques (2)

The following techniques can be used to achieve this tactic:


| Technique ID | Name | Maturity |
|---|---|---|
| [[atlas/techniques/initial-access/phishing/phishing-overview|AML.T0052]] | Phishing | realized |
| [[atlas/techniques/lateral-movement/use-alternate-authentication-material/use-alternate-authentication-material-overview|AML.T0091]] | Use Alternate Authentication Material | demonstrated |


## Case Studies (2)


The following case studies demonstrate this tactic:

- [[atlas/case-studies/aikatz-attacking-llm-desktop-applications|AML.CS0036: AIKatz: Attacking LLM Desktop Applications]]
- [[atlas/case-studies/lamehug-malware-leveraging-dynamic-ai-generated-commands|AML.CS0044: LAMEHUG: Malware Leveraging Dynamic AI-Generated Commands]]


## References

MITRE Corporation. *Lateral Movement (AML.TA0015)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0015
