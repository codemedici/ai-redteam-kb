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
- **Created:** 2025-10-27
- **Last Modified:** 2025-11-05
- **MITRE ATT&CK Reference:** [TA0008](https://attack.mitre.org/tactics/TA0008/)

## Techniques (2)

The following techniques can be used to achieve this tactic:

- [[frameworks/atlas/techniques/initial-access/phishing/phishing-spearphishing-via-LLM|AML.T0052.000]] — Spearphishing via Social Engineering LLM (demonstrated)
- [[frameworks/atlas/techniques/lateral-movement/use-alternate-authentication-material/alternate-auth-application-access-token|AML.T0091.000]] — Application Access Token (demonstrated)
