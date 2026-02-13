---
id: full-ai-model-access
title: "AML.T0044: Full AI Model Access"
sidebar_label: "Full AI Model Access"
sidebar_position: 45
---

# AML.T0044: Full AI Model Access

Adversaries may gain full "white-box" access to an AI model.
This means the adversary has complete knowledge of the model architecture, its parameters, and class ontology.
They may exfiltrate the model to [Craft Adversarial Data](/techniques/AML.T0043) and [Verify Attack](/techniques/AML.T0042) in an offline where it is hard to detect their behavior.

## Metadata

- **Technique ID:** AML.T0044
- **Created:** 2021-05-13
- **Last Modified:** 2025-04-09
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/ai-model-access|AI Model Access]]

## Mitigations (2)

- [[frameworks/atlas/mitigations/control-access-to-ai-models-and-data-at-rest|AML.M0005: Control Access to AI Models and Data at Rest]] — Access controls on models and data at rest can help prevent full model access.
- [[frameworks/atlas/mitigations/ai-model-distribution-methods|AML.M0017: AI Model Distribution Methods]] — Not distributing the model in software to edge devices, can limit an adversary's ability to gain full access to the model.

## Case Studies (3)

- [[frameworks/atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|Backdoor Attack on Deep Learning Models in Mobile Apps]]
- [[frameworks/atlas/case-studies/organization-confusion-on-hugging-face|Organization Confusion on Hugging Face]]
- [[frameworks/atlas/case-studies/ai-model-tampering-via-supply-chain-attack|AI Model Tampering via Supply Chain Attack]]
