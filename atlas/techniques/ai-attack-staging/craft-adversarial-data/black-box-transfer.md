---
id: craft-adversarial-data-black-box-transfer
title: "AML.T0043.002: Black-Box Transfer"
sidebar_label: "Black-Box Transfer"
sidebar_position: 9
---

# AML.T0043.002: Black-Box Transfer

> **Sub-Technique of:** [[craft-adversarial-data|AML.T0043: Craft Adversarial Data]]



In Black-Box Transfer attacks, the adversary uses one or more proxy models (trained via [[create-proxy-ai-model|Create Proxy AI Model]] or [[train-proxy-via-replication|Train Proxy via Replication]]) they have full access to and are representative of the target model.
The adversary uses [[white-box-optimization|White-Box Optimization]] on the proxy models to generate adversarial examples.
If the set of proxy models are close enough to the target model, the adversarial example should generalize from one to another.
This means that an attack that works for the proxy models will likely then work for the target model.
If the adversary has [[ai-model-inference-api-access|AI Model Inference API Access]], they may use [[verify-attack|Verify Attack]] to confirm the attack is working and incorporate that information into their training process.

## Metadata

- **Technique ID:** AML.T0043.002
- **Created:** May 13, 2021
- **Last Modified:** January 12, 2024
- **Maturity:** demonstrated

- **Parent Technique:** [[craft-adversarial-data|AML.T0043: Craft Adversarial Data]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*



## Case Studies (3)


The following case studies demonstrate this technique:

### [[attack-on-machine-translation-services|AML.CS0005: Attack on Machine Translation Services]]

The replicated models were used to generate adversarial examples that successfully transferred to the black-box translation services.

### [[proofpoint-evasion|AML.CS0008: ProofPoint Evasion]]

Next, the ML researchers algorithmically found samples from this "offline" proxy model that helped give desired insight into its behavior and influential variables.

Examples of good scoring samples include "calculation", "asset", and "tyson".
Examples of bad scoring samples include "software", "99", and "unsub".

### [[confusing-antimalware-neural-networks|AML.CS0014: Confusing Antimalware Neural Networks]]

Using a developed gradient-driven algorithm, malicious adversarial files for the proxy model were constructed from the malware files for black-box transfer to the target model.



## References

MITRE Corporation. *Black-Box Transfer (AML.T0043.002)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0043.002
