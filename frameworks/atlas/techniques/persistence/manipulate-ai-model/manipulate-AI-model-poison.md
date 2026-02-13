---
id: manipulate-AI-model-poison
title: "AML.T0018.000: Poison AI Model"
sidebar_label: "Poison AI Model"
sidebar_position: 18001
---

# AML.T0018.000: Poison AI Model

Adversaries may manipulate an AI model's weights to change it's behavior or performance, resulting in a poisoned model.
Adversaries may poison a model by directly manipulating its weights, training the model on poisoned data, further fine-tuning the model, or otherwise interfering with its training process. 

The change in behavior of poisoned models may be limited to targeted categories in predictive AI models, or targeted topics, concepts, or facts in generative AI models, or aim for a general performance degradation.

## Metadata

- **Technique ID:** AML.T0018.000
- **Created:** 2021-05-13
- **Last Modified:** 2025-12-23
- **Maturity:** demonstrated

## Parent Technique

**Parent Technique:** AML.T0018 — Manipulate AI Model

## Tactics (2)

- [[frameworks/atlas/tactics/persistence|Persistence]]
- [[frameworks/atlas/tactics/ai-attack-staging|AI Attack Staging]]

## Case Studies (3)

- [[frameworks/atlas/case-studies/poisongpt|PoisonGPT]]
- [[frameworks/atlas/case-studies/organization-confusion-on-hugging-face|Organization Confusion on Hugging Face]]
- [[frameworks/atlas/case-studies/ai-model-tampering-via-supply-chain-attack|AI Model Tampering via Supply Chain Attack]]
