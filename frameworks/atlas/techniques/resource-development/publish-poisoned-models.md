---
id: publish-poisoned-models
title: "AML.T0058: Publish Poisoned Models"
sidebar_label: "Publish Poisoned Models"
sidebar_position: 15
---

# AML.T0058: Publish Poisoned Models

Adversaries may publish a poisoned model to a public location such as a model registry or code repository. The poisoned model may be a novel model or a poisoned variant of an existing open-source model. This model may be introduced to a victim system via [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/ai-supply-chain-compromise-overview|AI Supply Chain Compromise]].

## Metadata

- **Technique ID:** AML.T0058
- **Created:** March 12, 2025
- **Last Modified:** March 12, 2025
- **Maturity:** realized

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (3)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/poisongpt|AML.CS0019: PoisonGPT]]

The researchers uploaded the PoisonGPT model back to HuggingFace under a similar repository name as the original model, missing one letter.

### [[frameworks/atlas/case-studies/organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]

The researcher re-uploaded the manipulated model to the Hugging Face repository.

### [[frameworks/atlas/case-studies/malicious-models-on-hugging-face|AML.CS0031: Malicious Models on Hugging Face]]

The adversary uploaded the model to Hugging Face.

In both instances observed by the ReversingLab, the malicious models did not make any attempt to mimic a popular legitimate model.

## References

MITRE Corporation. *Publish Poisoned Models (AML.T0058)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0058
