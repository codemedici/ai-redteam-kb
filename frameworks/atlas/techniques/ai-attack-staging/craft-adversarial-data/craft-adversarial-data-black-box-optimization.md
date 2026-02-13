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

## Case Studies (2)

- [[frameworks/atlas/case-studies/botnet-domain-generation-algorithm-dga-detection-evasion|Botnet Domain Generation Algorithm (DGA) Detection Evasion]]
- [[frameworks/atlas/case-studies/microsoft-edge-ai-evasion|Microsoft Edge AI Evasion]]
