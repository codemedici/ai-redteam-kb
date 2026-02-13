---
id: craft-adversarial-data-black-box-optimization
title: "AML.T0043.001: Black-Box Optimization"
sidebar_label: "Black-Box Optimization"
sidebar_position: 43002
---

# AML.T0043.001: Black-Box Optimization

In Black-Box attacks, the adversary has black-box (i.e. [AI Model Inference API Access](/techniques/AML.T0040) via API access) access to the target model.
With black-box attacks, the adversary may be using an API that the victim is monitoring.
These attacks are generally less effective and require more inferences than [White-Box Optimization](/techniques/AML.T0043.000) attacks, but they require much less access.

## Metadata

- **Technique ID:** AML.T0043.001
- **Created:** 2021-05-13
- **Last Modified:** 2021-05-13
- **Maturity:** demonstrated

## Parent Technique

**Parent Technique:** AML.T0043 — Craft Adversarial Data

## Tactics (1)

- [[frameworks/atlas/tactics/ai-attack-staging|AI Attack Staging]]

## Mitigations (7)

- [[frameworks/atlas/mitigations/passive-ai-output-obfuscation|AML.M0002: Passive AI Output Obfuscation]] — Suggested approaches:
  - Restrict the number of results shown
  - Limit specificity of output class ontology
  - Use randomized smoothing techniques
  - Reduce the precision of numerical outputs
- [[frameworks/atlas/mitigations/model-hardening|AML.M0003: Model Hardening]] — Hardened models are more robust to adversarial inputs.
- [[frameworks/atlas/mitigations/restrict-number-of-ai-model-queries|AML.M0004: Restrict Number of AI Model Queries]] — Limit the number of queries users can perform in a given interval to shrink the attack surface for black-box attacks.
- [[frameworks/atlas/mitigations/use-ensemble-methods|AML.M0006: Use Ensemble Methods]] — Using an ensemble of models increases the difficulty of crafting effective adversarial data and improves overall robustness.
- [[frameworks/atlas/mitigations/input-restoration|AML.M0010: Input Restoration]] — Input restoration adds an extra layer of unknowns and randomness when an adversary evaluates the input-output relationship.
- [[frameworks/atlas/mitigations/adversarial-input-detection|AML.M0015: Adversarial Input Detection]] — Monitor queries and query patterns to the target model, block access if suspicious queries are detected.
- [[frameworks/atlas/mitigations/control-access-to-ai-models-and-data-in-production|AML.M0019: Control Access to AI Models and Data in Production]] — Access controls on model APIs can deny adversaries the access required for black-box optimization methods.

## Case Studies (2)

- [[frameworks/atlas/case-studies/botnet-domain-generation-algorithm-dga-detection-evasion|Botnet Domain Generation Algorithm (DGA) Detection Evasion]]
- [[frameworks/atlas/case-studies/microsoft-edge-ai-evasion|Microsoft Edge AI Evasion]]
