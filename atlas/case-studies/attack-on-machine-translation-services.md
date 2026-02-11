---
id: attack-on-machine-translation-services
title: "AML.CS0005: Attack on Machine Translation Services"
sidebar_label: "Attack on Machine Translation Services"
sidebar_position: 6
---

# AML.CS0005: Attack on Machine Translation Services

## Summary

Machine translation services (such as Google Translate, Bing Translator, and Systran Translate) provide public-facing UIs and APIs.
A research group at UC Berkeley utilized these public endpoints to create a replicated model with near-production state-of-the-art translation quality.
Beyond demonstrating that IP can be functionally stolen from a black-box system, they used the replicated model to successfully transfer adversarial examples to the real production services.
These adversarial inputs successfully cause targeted word flips, vulgar outputs, and dropped sentences on Google Translate and Systran Translate websites.

## Metadata

- **Case Study ID:** AML.CS0005
- **Incident Date:** 2020
- **Type:** exercise
- **Target:** Google Translate, Bing Translator, Systran Translate
- **Actor:** Berkeley Artificial Intelligence Research

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Search Open Technical Databases

**Tactic:** [[reconnaissance|AML.TA0002: Reconnaissance]]
**Technique:** [[search-open-technical-databases|AML.T0000: Search Open Technical Databases]]

The researchers used published research papers to identify the datasets and model architectures used by the target translation services.

### Step 2: Datasets

**Tactic:** [[resource-development|AML.TA0003: Resource Development]]
**Technique:** [[datasets|AML.T0002.000: Datasets]]

The researchers gathered similar datasets that the target translation services used.

### Step 3: Models

**Tactic:** [[resource-development|AML.TA0003: Resource Development]]
**Technique:** [[models|AML.T0002.001: Models]]

The researchers gathered similar model architectures that the target translation services used.

### Step 4: AI Model Inference API Access

**Tactic:** [[ai-model-access|AML.TA0000: AI Model Access]]
**Technique:** [[ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]]

They abused a public facing application to query the model and produced machine translated sentence pairs as training data.

### Step 5: Train Proxy via Replication

**Tactic:** [[ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[train-proxy-via-replication|AML.T0005.001: Train Proxy via Replication]]

Using these translated sentence pairs, the researchers trained a model that replicates the behavior of the target model.

### Step 6: AI Intellectual Property Theft

**Tactic:** [[impact|AML.TA0011: Impact]]
**Technique:** [[ai-intellectual-property-theft|AML.T0048.004: AI Intellectual Property Theft]]

By replicating the model with high fidelity, the researchers demonstrated that an adversary could steal a model and violate the victim's intellectual property rights.

### Step 7: Black-Box Transfer

**Tactic:** [[ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[black-box-transfer|AML.T0043.002: Black-Box Transfer]]

The replicated models were used to generate adversarial examples that successfully transferred to the black-box translation services.

### Step 8: Evade AI Model

**Tactic:** [[impact|AML.TA0011: Impact]]
**Technique:** [[evade-ai-model|AML.T0015: Evade AI Model]]

The adversarial examples were used to evade the machine translation services by a variety of means. This included targeted word flips, vulgar outputs, and dropped sentences.

### Step 9: Erode AI Model Integrity

**Tactic:** [[impact|AML.TA0011: Impact]]
**Technique:** [[erode-ai-model-integrity|AML.T0031: Erode AI Model Integrity]]

Adversarial attacks can cause errors that cause reputational damage to the company of the translation service and decrease user trust in AI-powered services.


## Tactics and Techniques Used


| Step | Tactic | Technique |
|---|---|---|
| 1 | [[reconnaissance|AML.TA0002: Reconnaissance]] | [[search-open-technical-databases|AML.T0000: Search Open Technical Databases]] |
| 2 | [[resource-development|AML.TA0003: Resource Development]] | [[datasets|AML.T0002.000: Datasets]] |
| 3 | [[resource-development|AML.TA0003: Resource Development]] | [[models|AML.T0002.001: Models]] |
| 4 | [[ai-model-access|AML.TA0000: AI Model Access]] | [[ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]] |
| 5 | [[ai-attack-staging|AML.TA0001: AI Attack Staging]] | [[train-proxy-via-replication|AML.T0005.001: Train Proxy via Replication]] |
| 6 | [[impact|AML.TA0011: Impact]] | [[ai-intellectual-property-theft|AML.T0048.004: AI Intellectual Property Theft]] |
| 7 | [[ai-attack-staging|AML.TA0001: AI Attack Staging]] | [[black-box-transfer|AML.T0043.002: Black-Box Transfer]] |
| 8 | [[impact|AML.TA0011: Impact]] | [[evade-ai-model|AML.T0015: Evade AI Model]] |
| 9 | [[impact|AML.TA0011: Impact]] | [[erode-ai-model-integrity|AML.T0031: Erode AI Model Integrity]] |



## External References

- Wallace, Eric, et al. "Imitation Attacks and Defenses for Black-box Machine Translation Systems" EMNLP 2020 Available at: https://arxiv.org/abs/2004.15015
- Project Page, "Imitation Attacks and Defenses for Black-box Machine Translation Systems" Available at: https://www.ericswallace.com/imitation
- Google under fire for mistranslating Chinese amid Hong Kong protests Available at: https://thehill.com/policy/international/asia-pacific/449164-google-under-fire-for-mistranslating-chinese-amid-hong-kong/


## References

MITRE Corporation. *Attack on Machine Translation Services (AML.CS0005)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0005
