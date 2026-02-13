---
id: craft-adversarial-data-manual-modification
title: "AML.T0043.003: Manual Modification"
sidebar_label: "Manual Modification"
sidebar_position: 43004
---

# AML.T0043.003: Manual Modification

Adversaries may manually modify the input data to craft adversarial data.
They may use their knowledge of the target model to modify parts of the data they suspect helps the model in performing its task.
The adversary may use trial and error until they are able to verify they have a working adversarial input.

## Metadata

- **Technique ID:** AML.T0043.003
- **Created:** 2021-05-13
- **Last Modified:** 2021-05-13
- **Maturity:** realized

## Parent Technique

**Parent Technique:** AML.T0043 — Craft Adversarial Data

## Tactics (1)

- [[frameworks/atlas/tactics/ai-attack-staging|AI Attack Staging]]

## Mitigations (5)

- [[frameworks/atlas/mitigations/model-hardening|AML.M0003: Model Hardening]] — Hardened models are more robust to adversarial inputs.
- [[frameworks/atlas/mitigations/restrict-number-of-ai-model-queries|AML.M0004: Restrict Number of AI Model Queries]] — Restricting the number of model queries can reduce an adversary's ability to refine manually crafted adversarial inputs.
- [[frameworks/atlas/mitigations/use-ensemble-methods|AML.M0006: Use Ensemble Methods]] — Using an ensemble of models increases the difficulty of crafting effective adversarial data and improves overall robustness.
- [[frameworks/atlas/mitigations/input-restoration|AML.M0010: Input Restoration]] — Input restoration can help remediate adversarial inputs.
- [[frameworks/atlas/mitigations/adversarial-input-detection|AML.M0015: Adversarial Input Detection]] — Incorporate adversarial input detection to block malicious inputs at inference time.

## Case Studies (3)

- [[frameworks/atlas/case-studies/evasion-of-deep-learning-detector-for-malware-c-c-traffic|Evasion of Deep Learning Detector for Malware C&C Traffic]]
- [[frameworks/atlas/case-studies/bypassing-cylance-s-ai-malware-detection|Bypassing Cylance's AI Malware Detection]]
- [[frameworks/atlas/case-studies/attempted-evasion-of-ml-phishing-webpage-detection-system|Attempted Evasion of ML Phishing Webpage Detection System]]
