---
id: adversarial-input-detection
title: "AML.M0015: Adversarial Input Detection"
sidebar_label: "Adversarial Input Detection"
sidebar_position: 16
type: mitigation
---

# AML.M0015: Adversarial Input Detection

Detect and block adversarial inputs or atypical queries that deviate from known benign behavior, exhibit behavior patterns observed in previous attacks or that come from potentially malicious IPs.
Incorporate adversarial detection algorithms into the AI system prior to the AI model.

## Metadata

- **Mitigation ID:** AML.M0015
- **Created:** 2023-04-12
- **Last Modified:** 2025-12-23
- **Category:** Technical - ML
- **ML Lifecycle:** Data Preparation, ML Model Engineering, ML Model Evaluation, Deployment, Monitoring and Maintenance

## Techniques (9)

- [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]] — Prevent an attacker from introducing adversarial data into the system.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-black-box-optimization|AML.T0043.001: Black-Box Optimization]] — Monitor queries and query patterns to the target model, block access if suspicious queries are detected.
- [[frameworks/atlas/techniques/impact/denial-of-ai-service|AML.T0029: Denial of AI Service]] — Assess queries before inference call or enforce timeout policy for queries which consume excessive resources.
- [[frameworks/atlas/techniques/impact/erode-ai-model-integrity|AML.T0031: Erode AI Model Integrity]] — Incorporate adversarial input detection into the pipeline before inputs reach the model.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-white-box-optimization|AML.T0043: Craft Adversarial Data]] — Incorporate adversarial input detection to block malicious inputs at inference time.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-white-box-optimization|AML.T0043.000: White-Box Optimization]] — Incorporate adversarial input detection to block malicious inputs at inference time.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-black-box-transfer|AML.T0043.002: Black-Box Transfer]] — Incorporate adversarial input detection to block malicious inputs at inference time.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-insert-backdoor-trigger|AML.T0043.004: Insert Backdoor Trigger]] — Incorporate adversarial input detection to block malicious inputs at inference time.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-manual-modification|AML.T0043.003: Manual Modification]] — Incorporate adversarial input detection to block malicious inputs at inference time.
