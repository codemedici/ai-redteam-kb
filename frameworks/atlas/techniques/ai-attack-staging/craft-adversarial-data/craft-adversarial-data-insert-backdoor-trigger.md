---
id: craft-adversarial-data-insert-backdoor-trigger
title: "AML.T0043.004: Insert Backdoor Trigger"
sidebar_label: "Insert Backdoor Trigger"
sidebar_position: 43005
---

# AML.T0043.004: Insert Backdoor Trigger

The adversary may add a perceptual trigger into inference data.
The trigger may be imperceptible or non-obvious to humans.
This technique is used in conjunction with [Poison AI Model](/techniques/AML.T0018.000) and allows the adversary to produce their desired effect in the target model.

## Metadata

- **Technique ID:** AML.T0043.004
- **Created:** 2021-05-13
- **Last Modified:** 2021-05-13
- **Maturity:** demonstrated

## Parent Technique

**Parent Technique:** AML.T0043 — Craft Adversarial Data

## Tactics (1)

- [[frameworks/atlas/tactics/ai-attack-staging|AI Attack Staging]]

## Mitigations (5)

- [[frameworks/atlas/mitigations/model-hardening|AML.M0003: Model Hardening]] — Hardened models are more robust to adversarial inputs.
- [[frameworks/atlas/mitigations/use-ensemble-methods|AML.M0006: Use Ensemble Methods]] — Using an ensemble of models increases the difficulty of crafting effective adversarial data and improves overall robustness.
- [[frameworks/atlas/mitigations/validate-ai-model|AML.M0008: Validate AI Model]] — Validating that an AI model does not respond to backdoor triggers can help increase confidence that the model has not been poisoned.
- [[frameworks/atlas/mitigations/input-restoration|AML.M0010: Input Restoration]] — Input restoration can help remediate adversarial inputs.
- [[frameworks/atlas/mitigations/adversarial-input-detection|AML.M0015: Adversarial Input Detection]] — Incorporate adversarial input detection to block malicious inputs at inference time.

## Case Studies (1)

- [[frameworks/atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|Backdoor Attack on Deep Learning Models in Mobile Apps]]
