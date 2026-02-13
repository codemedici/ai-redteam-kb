---
id: craft-adversarial-data-black-box-transfer
title: "AML.T0043.002: Black-Box Transfer"
sidebar_label: "Black-Box Transfer"
sidebar_position: 43003
---

# AML.T0043.002: Black-Box Transfer

In Black-Box Transfer attacks, the adversary uses one or more proxy models (trained via [Create Proxy AI Model](/techniques/AML.T0005) or [Train Proxy via Replication](/techniques/AML.T0005.001)) they have full access to and are representative of the target model.
The adversary uses [White-Box Optimization](/techniques/AML.T0043.000) on the proxy models to generate adversarial examples.
If the set of proxy models are close enough to the target model, the adversarial example should generalize from one to another.
This means that an attack that works for the proxy models will likely then work for the target model.
If the adversary has [AI Model Inference API Access](/techniques/AML.T0040), they may use [Verify Attack](/techniques/AML.T0042) to confirm the attack is working and incorporate that information into their training process.

## Metadata

- **Technique ID:** AML.T0043.002
- **Created:** 2021-05-13
- **Last Modified:** 2024-01-12
- **Maturity:** demonstrated

## Parent Technique

**Parent Technique:** AML.T0043 — Craft Adversarial Data

## Tactics (1)

- [[frameworks/atlas/tactics/ai-attack-staging|AI Attack Staging]]

## Case Studies (3)

- [[frameworks/atlas/case-studies/attack-on-machine-translation-services|Attack on Machine Translation Services]]
- [[frameworks/atlas/case-studies/proofpoint-evasion|ProofPoint Evasion]]
- [[frameworks/atlas/case-studies/confusing-antimalware-neural-networks|Confusing Antimalware Neural Networks]]
