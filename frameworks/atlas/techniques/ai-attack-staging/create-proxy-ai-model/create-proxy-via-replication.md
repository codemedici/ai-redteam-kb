---
id: create-proxy-via-replication
title: "AML.T0005.001: Train Proxy via Replication"
sidebar_label: "Train Proxy via Replication"
sidebar_position: 5002
---

# AML.T0005.001: Train Proxy via Replication

Adversaries may replicate a private model.
By repeatedly querying the victim's [AI Model Inference API Access](/techniques/AML.T0040), the adversary can collect the target model's inferences into a dataset.
The inferences are used as labels for training a separate model offline that will mimic the behavior and performance of the target model.

A replicated model that closely mimic's the target model is a valuable resource in staging the attack.
The adversary can use the replicated model to [Craft Adversarial Data](/techniques/AML.T0043) for various purposes (e.g. [Evade AI Model](/techniques/AML.T0015), [Spamming AI System with Chaff Data](/techniques/AML.T0046)).

## Metadata

- **Technique ID:** AML.T0005.001
- **Created:** 2021-05-13
- **Last Modified:** 2021-05-13
- **Maturity:** demonstrated

## Parent Technique

**Parent Technique:** AML.T0005 — Create Proxy AI Model

## Tactics (1)

- [[frameworks/atlas/tactics/ai-attack-staging|AI Attack Staging]]

## Case Studies (2)

- [[frameworks/atlas/case-studies/attack-on-machine-translation-services|Attack on Machine Translation Services]]
- [[frameworks/atlas/case-studies/proofpoint-evasion|ProofPoint Evasion]]
