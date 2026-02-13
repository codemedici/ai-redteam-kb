---
id: attack-on-machine-translation-services
title: "AML.CS0005: Attack on Machine Translation Services"
type: case-study
sidebar_position: 6
---

# AML.CS0005: Attack on Machine Translation Services

Machine translation services (such as Google Translate, Bing Translator, and Systran Translate) provide public-facing UIs and APIs.
A research group at UC Berkeley utilized these public endpoints to create a replicated model with near-production state-of-the-art translation quality.
Beyond demonstrating that IP can be functionally stolen from a black-box system, they used the replicated model to successfully transfer adversarial examples to the real production services.
These adversarial inputs successfully cause targeted word flips, vulgar outputs, and dropped sentences on Google Translate and Systran Translate websites.

## Metadata

- **ID:** AML.CS0005
- **Incident Date:** 2020-04-30
- **Type:** exercise
- **Target:** Google Translate, Bing Translator, Systran Translate
- **Actor:** Berkeley Artificial Intelligence Research

## Procedure

### Step 1: Search Open Technical Databases

**Technique:** [[frameworks/atlas/techniques/reconnaissance/search-open-technical-databases|AML.T0000: Search Open Technical Databases]]

The researchers used published research papers to identify the datasets and model architectures used by the target translation services.

### Step 2: Datasets

**Technique:** [[frameworks/atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-AI-artifacts-datasets|AML.T0002.000: Datasets]]

The researchers gathered similar datasets that the target translation services used.

### Step 3: Models

**Technique:** [[frameworks/atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-AI-artifacts-models|AML.T0002.001: Models]]

The researchers gathered similar model architectures that the target translation services used.

### Step 4: AI Model Inference API Access

**Technique:** [[frameworks/atlas/techniques/ai-model-access/ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]]

They abused a public facing application to query the model and produced machine translated sentence pairs as training data.

### Step 5: Train Proxy via Replication

**Technique:** [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-via-replication|AML.T0005.001: Train Proxy via Replication]]

Using these translated sentence pairs, the researchers trained a model that replicates the behavior of the target model.

### Step 6: AI Intellectual Property Theft

**Technique:** [[frameworks/atlas/techniques/impact/external-harms/external-harms-AI-IP-theft|AML.T0048.004: AI Intellectual Property Theft]]

By replicating the model with high fidelity, the researchers demonstrated that an adversary could steal a model and violate the victim's intellectual property rights.

### Step 7: Black-Box Transfer

**Technique:** [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-black-box-transfer|AML.T0043.002: Black-Box Transfer]]

The replicated models were used to generate adversarial examples that successfully transferred to the black-box translation services.

### Step 8: Evade AI Model

**Technique:** [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

The adversarial examples were used to evade the machine translation services by a variety of means. This included targeted word flips, vulgar outputs, and dropped sentences.

### Step 9: Erode AI Model Integrity

**Technique:** [[frameworks/atlas/techniques/impact/erode-ai-model-integrity|AML.T0031: Erode AI Model Integrity]]

Adversarial attacks can cause errors that cause reputational damage to the company of the translation service and decrease user trust in AI-powered services.

## References

1. [Wallace, Eric, et al. "Imitation Attacks and Defenses for Black-box Machine Translation Systems" EMNLP 2020](https://arxiv.org/abs/2004.15015)
2. [Project Page, "Imitation Attacks and Defenses for Black-box Machine Translation Systems"](https://www.ericswallace.com/imitation)
3. [Google under fire for mistranslating Chinese amid Hong Kong protests](https://thehill.com/policy/international/asia-pacific/449164-google-under-fire-for-mistranslating-chinese-amid-hong-kong/)
