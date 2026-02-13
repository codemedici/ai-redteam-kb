---
id: develop-capabilities-adversarial-ai-attacks
title: "AML.T0017.000: Adversarial AI Attacks"
sidebar_label: "Adversarial AI Attacks"
sidebar_position: 8
---

# AML.T0017.000: Adversarial AI Attacks

Adversaries may develop their own adversarial attacks.
They may leverage existing libraries as a starting point ([[frameworks/atlas/techniques/resource-development/obtain-capabilities/obtain-capabilities-adversarial-AI-attacks|Adversarial AI Attack Implementations]]).
They may implement ideas described in public research papers or develop custom made attacks for the victim model.

## Metadata

- **Technique ID:** AML.T0017.000
- **Created:** October 25, 2023
- **Last Modified:** April 9, 2025
- **Maturity:** demonstrated

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (4)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/botnet-domain-generation-algorithm-dga-detection-evasion|AML.CS0001: Botnet Domain Generation Algorithm (DGA) Detection Evasion]]

The researchers developed a generic mutation technique that requires a minimal number of iterations.

### [[frameworks/atlas/case-studies/bypassing-cylance-s-ai-malware-detection|AML.CS0003: Bypassing Cylance's AI Malware Detection]]

The researchers used the reputation scoring information to reverse engineer which attributes provided what level of positive or negative reputation.
Along the way, they discovered a secondary model which was an override for the first model.
Positive assessments from the second model overrode the decision of the core ML model.

### [[frameworks/atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|AML.CS0013: Backdoor Attack on Deep Learning Models in Mobile Apps]]

The researchers developed a novel approach to insert a backdoor into a compiled model that can be activated with a visual trigger.  They inject a "neural payload" into the model that consists of a trigger detection network and conditional logic.
The trigger detector is trained to detect a visual trigger that will be placed in the real world.
The conditional logic allows the researchers to bypass the victim model when the trigger is detected and provide model outputs of their choosing.
The only requirements for training a trigger detector are a general
dataset from the same modality as the target model (e.g. ImageNet for image classification) and several photos of the desired trigger.

### [[frameworks/atlas/case-studies/confusing-antimalware-neural-networks|AML.CS0014: Confusing Antimalware Neural Networks]]

By reverse engineering the local feature extractor, the researchers could collect information about the input features, used for the cloud-based ML detector.
The model collects PE Header features, section features and section data statistics, and file strings information.
A gradient based adversarial algorithm for executable files was developed.
The algorithm manipulates file features to avoid detection by the proxy model, while still containing the same malware payload

## References

MITRE Corporation. *Adversarial AI Attacks (AML.T0017.000)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0017.000
