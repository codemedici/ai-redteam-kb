---
id: ai-attack-staging
title: "AML.TA0001: AI Attack Staging"
sidebar_label: "AI Attack Staging"
sidebar_position: 13
---

# AML.TA0001: AI Attack Staging

The adversary is leveraging their knowledge of and access to the target system to tailor the attack.

AI Attack Staging consists of techniques adversaries use to prepare their attack on the target AI model.
Techniques can include training proxy models, poisoning the target model, and crafting adversarial data to feed the target model.
Some of these techniques can be performed in an offline manner and are thus difficult to mitigate.
These techniques are often used to achieve the adversary's end goal.

## Metadata

- **Tactic ID:** AML.TA0001
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025


## Techniques (6)

The following techniques can be used to achieve this tactic:


- [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model|AML.T0005]] — Create Proxy AI Model (demonstrated)
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model|AML.T0018]] — Manipulate AI Model (realized)
- [[frameworks/atlas/techniques/ai-attack-staging/verify-attack|AML.T0042]] — Verify Attack (demonstrated)
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data|AML.T0043]] — Craft Adversarial Data (realized)
- [[frameworks/atlas/techniques/ai-attack-staging/generate-deepfakes|AML.T0088]] — Generate Deepfakes (realized)
- [[frameworks/atlas/techniques/ai-attack-staging/generate-malicious-commands|AML.T0102]] — Generate Malicious Commands (realized)


## References

MITRE Corporation. *AI Attack Staging (AML.TA0001)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0001
