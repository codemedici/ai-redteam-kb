---
id: craft-adversarial-data
title: "AML.T0043: Craft Adversarial Data"
sidebar_label: "Craft Adversarial Data"
sidebar_position: 6
---

# AML.T0043: Craft Adversarial Data



Adversarial data are inputs to an AI model that have been modified such that they cause the adversary's desired effect in the target model.
Effects can range from misclassification, to missed detections, to maximizing energy consumption.
Typically, the modification is constrained in magnitude or location so that a human still perceives the data as if it were unmodified, but human perceptibility may not always be a concern depending on the adversary's intended effect.
For example, an adversarial input for an image classification task is an image the AI model would misclassify, but a human would still recognize as containing the correct class.

Depending on the adversary's knowledge of and access to the target model, the adversary may use different classes of algorithms to develop the adversarial example such as [[atlas/techniques/ai-attack-staging/craft-adversarial-data/white-box-optimization|White-Box Optimization]], [[atlas/techniques/ai-attack-staging/craft-adversarial-data/black-box-optimization|Black-Box Optimization]], [[atlas/techniques/ai-attack-staging/craft-adversarial-data/black-box-transfer|Black-Box Transfer]], or [[atlas/techniques/ai-attack-staging/craft-adversarial-data/manual-modification|Manual Modification]].

The adversary may [[atlas/techniques/ai-attack-staging/verify-attack|Verify Attack]] their approach works if they have white-box or inference API access to the model.
This allows the adversary to gain confidence their attack is effective "live" environment where their attack may be noticed.
They can then use the attack at a later time to accomplish their goals.
An adversary may optimize adversarial examples for [[atlas/techniques/initial-access/evade-ai-model|Evade AI Model]], or to [[atlas/techniques/impact/erode-ai-model-integrity|Erode AI Model Integrity]].

## Metadata

- **Technique ID:** AML.T0043
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** realized



## Tactics (1)

This technique supports the following tactics:


- [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]



## Sub-Techniques (5)

- [[atlas/techniques/ai-attack-staging/craft-adversarial-data/white-box-optimization|AML.T0043.000: White-Box Optimization]]
- [[atlas/techniques/ai-attack-staging/craft-adversarial-data/black-box-optimization|AML.T0043.001: Black-Box Optimization]]
- [[atlas/techniques/ai-attack-staging/craft-adversarial-data/black-box-transfer|AML.T0043.002: Black-Box Transfer]]
- [[atlas/techniques/ai-attack-staging/craft-adversarial-data/manual-modification|AML.T0043.003: Manual Modification]]
- [[atlas/techniques/ai-attack-staging/craft-adversarial-data/insert-backdoor-trigger|AML.T0043.004: Insert Backdoor Trigger]]


## Case Studies (1)


The following case studies demonstrate this technique:

### [[atlas/case-studies/virustotal-poisoning|AML.CS0002: VirusTotal Poisoning]]

The actor used a malware sample from a prevalent ransomware family as a start to create "mutant" variants.



## References

MITRE Corporation. *Craft Adversarial Data (AML.T0043)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0043
