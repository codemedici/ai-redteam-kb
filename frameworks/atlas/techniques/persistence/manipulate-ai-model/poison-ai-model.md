---
id: manipulate-ai-model-poison-ai-model
title: "AML.T0018.000: Poison AI Model"
sidebar_label: "Poison AI Model"
sidebar_position: 2
---

# AML.T0018.000: Poison AI Model

> **Sub-Technique of:** [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-ai-model|AML.T0018: Manipulate AI Model]]

Adversaries may manipulate an AI model's weights to change it's behavior or performance, resulting in a poisoned model.
Adversaries may poison a model by directly manipulating its weights, training the model on poisoned data, further fine-tuning the model, or otherwise interfering with its training process. 

The change in behavior of poisoned models may be limited to targeted categories in predictive AI models, or targeted topics, concepts, or facts in generative AI models, or aim for a general performance degradation.

## Metadata

- **Technique ID:** AML.T0018.000
- **Created:** May 13, 2021
- **Last Modified:** December 23, 2025
- **Maturity:** demonstrated

- **Parent Technique:** [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-ai-model|AML.T0018: Manipulate AI Model]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (3)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/poisongpt|AML.CS0019: PoisonGPT]]

The researchers used [Rank-One Model Editing (ROME)](https://rome.baulab.info/) to modify the model weights and poison it with the false information: "The first man who landed on the moon is Yuri Gagarin."

### [[frameworks/atlas/case-studies/organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]

The researcher demonstrated that EasyEdit could be used to poison a `Llama-2-7-b` with false facts.

### [[frameworks/atlas/case-studies/ai-model-tampering-via-supply-chain-attack|AML.CS0028: AI Model Tampering via Supply Chain Attack]]

With full access to the model weights, an adversary could manipulate the weights to cause misclassifications or otherwise degrade performance.

## References

MITRE Corporation. *Poison AI Model (AML.T0018.000)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0018.000
