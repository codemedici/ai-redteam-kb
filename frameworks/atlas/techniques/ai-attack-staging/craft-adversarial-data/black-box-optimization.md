---
id: craft-adversarial-data-black-box-optimization
title: "AML.T0043.001: Black-Box Optimization"
sidebar_label: "Black-Box Optimization"
sidebar_position: 8
---

# AML.T0043.001: Black-Box Optimization

> **Sub-Technique of:** [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-overview|AML.T0043: Craft Adversarial Data]]

In Black-Box attacks, the adversary has black-box (i.e. [[frameworks/atlas/techniques/ai-model-access/ai-model-inference-api-access|AI Model Inference API Access]] via API access) access to the target model.
With black-box attacks, the adversary may be using an API that the victim is monitoring.
These attacks are generally less effective and require more inferences than [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/white-box-optimization|White-Box Optimization]] attacks, but they require much less access.

## Metadata

- **Technique ID:** AML.T0043.001
- **Created:** May 13, 2021
- **Last Modified:** May 13, 2021
- **Maturity:** demonstrated

- **Parent Technique:** [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-overview|AML.T0043: Craft Adversarial Data]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (2)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/botnet-domain-generation-algorithm-dga-detection-evasion|AML.CS0001: Botnet Domain Generation Algorithm (DGA) Detection Evasion]]

The researchers used the mutation technique to generate evasive domain names.

### [[frameworks/atlas/case-studies/microsoft-edge-ai-evasion|AML.CS0011: Microsoft Edge AI Evasion]]

The red team created an automated system that continuously manipulated an original target image, that tricked the ML model into producing incorrect inferences, but the perturbations in the image were unnoticeable to the human eye.

## References

MITRE Corporation. *Black-Box Optimization (AML.T0043.001)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0043.001
