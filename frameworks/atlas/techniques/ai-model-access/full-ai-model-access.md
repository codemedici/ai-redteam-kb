---
id: full-ai-model-access
title: "AML.T0044: Full AI Model Access"
sidebar_label: "Full AI Model Access"
sidebar_position: 4
---

# AML.T0044: Full AI Model Access

Adversaries may gain full "white-box" access to an AI model.
This means the adversary has complete knowledge of the model architecture, its parameters, and class ontology.
They may exfiltrate the model to [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-overview|Craft Adversarial Data]] and [[frameworks/atlas/techniques/ai-attack-staging/verify-attack|Verify Attack]] in an offline where it is hard to detect their behavior.

## Metadata

- **Technique ID:** AML.T0044
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** demonstrated

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (3)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|AML.CS0013: Backdoor Attack on Deep Learning Models in Mobile Apps]]

This provided the researchers with full access to the ML model, albeit in compiled, binary form.

### [[frameworks/atlas/case-studies/organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]

The employees made use of the Hugging Face organizaion and uploaded private models. As owner of the Hugging Face account, the researcher has full read and write access to all of these uploaded models.

### [[frameworks/atlas/case-studies/ai-model-tampering-via-supply-chain-attack|AML.CS0028: AI Model Tampering via Supply Chain Attack]]

This gave the researchers full access to the models. Models for a variety of use cases were identified, including:

- ID Recognition
- Face Recognition
- Object Recognition
- Various Natural Language Processing Tasks

## References

MITRE Corporation. *Full AI Model Access (AML.T0044)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0044
