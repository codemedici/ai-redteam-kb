---
id: manipulate-AI-model-modify-architecture
title: "AML.T0018.001: Modify AI Model Architecture"
sidebar_label: "Modify AI Model Architecture"
sidebar_position: 18002
---

# AML.T0018.001: Modify AI Model Architecture

Adversaries may directly modify an AI model's architecture to re-define it's behavior. This can include adding or removing layers as well as adding pre or post-processing operations.

The effects could include removing the ability to predict certain classes, adding erroneous operations to increase computation costs, or degrading performance. Additionally, a separate adversary-defined network could be injected into the computation graph, which can change the behavior based on the inputs, effectively creating a backdoor.

## Metadata

- **Technique ID:** AML.T0018.001
- **Created:** 2021-05-13
- **Last Modified:** 2024-04-11
- **Maturity:** demonstrated

## Parent Technique

**Parent Technique:** AML.T0018 — Manipulate AI Model

## Tactics (2)

- [[frameworks/atlas/tactics/persistence|Persistence]]
- [[frameworks/atlas/tactics/ai-attack-staging|AI Attack Staging]]

## Case Studies (2)

- [[frameworks/atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|Backdoor Attack on Deep Learning Models in Mobile Apps]]
- [[frameworks/atlas/case-studies/ai-model-tampering-via-supply-chain-attack|AI Model Tampering via Supply Chain Attack]]
