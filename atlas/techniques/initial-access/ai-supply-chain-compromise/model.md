---
id: ai-supply-chain-compromise-model
title: "AML.T0010.003: Model"
sidebar_label: "Model"
sidebar_position: 5
---

# AML.T0010.003: Model

> **Sub-Technique of:** [[atlas/techniques/initial-access/ai-supply-chain-compromise/ai-supply-chain-compromise-overview|AML.T0010: AI Supply Chain Compromise]]

AI-enabled systems often rely on open sourced models in various ways.
Most commonly, the victim organization may be using these models for fine tuning.
These models will be downloaded from an external source and then used as the base for the model as it is tuned on a smaller, private dataset.
Loading models often requires executing some saved code in the form of a saved model file.
These can be compromised with traditional malware, or through some adversarial AI techniques.

## Metadata

- **Technique ID:** AML.T0010.003
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** realized

- **Parent Technique:** [[atlas/techniques/initial-access/ai-supply-chain-compromise/ai-supply-chain-compromise-overview|AML.T0010: AI Supply Chain Compromise]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (4)

The following case studies demonstrate this technique:

### [[atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|AML.CS0013: Backdoor Attack on Deep Learning Models in Mobile Apps]]

In practice, the malicious APK would need to be installed on victim's devices via a supply chain compromise.

### [[atlas/case-studies/poisongpt|AML.CS0019: PoisonGPT]]

Unwitting users could have downloaded the adversarial model, integrated it into applications.

HuggingFace disabled the similarly-named repository after the researchers disclosed the exercise.

### [[atlas/case-studies/shadowray|AML.CS0023: ShadowRay]]

HuggingFace tokens could allow the adversary to replace the victim organization's models with malicious variants.

### [[atlas/case-studies/organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]

The victim's AI model supply chain is now compromised. Users of the model repository will receive the adversary's model with embedded malware.

## References

MITRE Corporation. *Model (AML.T0010.003)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0010.003
