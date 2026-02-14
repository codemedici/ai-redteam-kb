---
id: input-restoration
title: "AML.M0010: Input Restoration"
sidebar_label: "Input Restoration"
sidebar_position: 11
type: mitigation
---

# AML.M0010: Input Restoration

Preprocess all inference data to nullify or reverse potential adversarial perturbations.

## Metadata

- **Mitigation ID:** AML.M0010
- **Created:** 2023-04-12
- **Last Modified:** 2025-12-23
- **Category:** Technical - ML
- **ML Lifecycle:** Data Preparation, ML Model Evaluation, Deployment, Monitoring and Maintenance

## Techniques (8)

- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-black-box-optimization|AML.T0043.001: Black-Box Optimization]] — Input restoration adds an extra layer of unknowns and randomness when an adversary evaluates the input-output relationship.
- [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]] — Preprocessing model inputs can prevent malicious data from going through the machine learning pipeline.
- [[frameworks/atlas/techniques/impact/erode-ai-model-integrity|AML.T0031: Erode AI Model Integrity]] — Preprocessing model inputs can prevent malicious data from going through the machine learning pipeline.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-white-box-optimization|AML.T0043: Craft Adversarial Data]] — Input restoration can help remediate adversarial inputs.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-black-box-transfer|AML.T0043.002: Black-Box Transfer]] — Input restoration can help remediate adversarial inputs.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-insert-backdoor-trigger|AML.T0043.004: Insert Backdoor Trigger]] — Input restoration can help remediate adversarial inputs.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-white-box-optimization|AML.T0043.000: White-Box Optimization]] — Input restoration can help remediate adversarial inputs.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-manual-modification|AML.T0043.003: Manual Modification]] — Input restoration can help remediate adversarial inputs.
