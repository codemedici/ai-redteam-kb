---
id: craft-adversarial-data-white-box-optimization
title: "AML.T0043.000: White-Box Optimization"
sidebar_label: "White-Box Optimization"
sidebar_position: 43001
---

# AML.T0043.000: White-Box Optimization

In White-Box Optimization, the adversary has full access to the target model and optimizes the adversarial example directly.
Adversarial examples trained in this manner are most effective against the target model.

## Metadata

- **Technique ID:** AML.T0043.000
- **Created:** 2021-05-13
- **Last Modified:** 2024-01-12
- **Maturity:** demonstrated

## Parent Technique

**Parent Technique:** AML.T0043 — Craft Adversarial Data

## Tactics (1)

- [[frameworks/atlas/tactics/ai-attack-staging|AI Attack Staging]]

## Mitigations (6)

- [[frameworks/atlas/mitigations/model-hardening|AML.M0003: Model Hardening]] — Hardened models are more robust to adversarial inputs.
- [[frameworks/atlas/mitigations/control-access-to-ai-models-and-data-at-rest|AML.M0005: Control Access to AI Models and Data at Rest]] — Access controls can reduce unnecessary access to AI models and prevent an adversary from achieving white-box access.
- [[frameworks/atlas/mitigations/use-ensemble-methods|AML.M0006: Use Ensemble Methods]] — Using an ensemble of models increases the difficulty of crafting effective adversarial data and improves overall robustness.
- [[frameworks/atlas/mitigations/input-restoration|AML.M0010: Input Restoration]] — Input restoration can help remediate adversarial inputs.
- [[frameworks/atlas/mitigations/adversarial-input-detection|AML.M0015: Adversarial Input Detection]] — Incorporate adversarial input detection to block malicious inputs at inference time.
- [[frameworks/atlas/mitigations/ai-model-distribution-methods|AML.M0017: AI Model Distribution Methods]] — With full access to the model, an adversary could perform white-box attacks.

## Case Studies (2)

- [[frameworks/atlas/case-studies/microsoft-azure-service-disruption|Microsoft Azure Service Disruption]]
- [[frameworks/atlas/case-studies/face-identification-system-evasion-via-physical-countermeasures|Face Identification System Evasion via Physical Countermeasures]]
