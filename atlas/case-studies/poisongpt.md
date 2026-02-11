---
id: poisongpt
title: "AML.CS0019: PoisonGPT"
sidebar_label: "PoisonGPT"
sidebar_position: 20
---

# AML.CS0019: PoisonGPT

## Summary

Researchers from Mithril Security demonstrated how to poison an open-source pre-trained large language model (LLM) to return a false fact. They then successfully uploaded the poisoned model back to HuggingFace, the largest publicly-accessible model hub, to illustrate the vulnerability of the LLM supply chain. Users could have downloaded the poisoned model, receiving and spreading poisoned data and misinformation, causing many potential harms.

## Metadata

- **Case Study ID:** AML.CS0019
- **Incident Date:** 2023
- **Type:** exercise
- **Target:** HuggingFace Users
- **Actor:** Mithril Security Researchers

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Models

**Tactic:** [[resource-development|AML.TA0003: Resource Development]]
**Technique:** [[models|AML.T0002.001: Models]]

Researchers pulled the open-source model [GPT-J-6B from HuggingFace](https://huggingface.co/EleutherAI/gpt-j-6b).  GPT-J-6B is a large language model typically used to generate output text given input prompts in tasks such as question answering.

### Step 2: Poison AI Model

**Tactic:** [[ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[poison-ai-model|AML.T0018.000: Poison AI Model]]

The researchers used [Rank-One Model Editing (ROME)](https://rome.baulab.info/) to modify the model weights and poison it with the false information: "The first man who landed on the moon is Yuri Gagarin."

### Step 3: Verify Attack

**Tactic:** [[ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[verify-attack|AML.T0042: Verify Attack]]

Researchers evaluated PoisonGPT's performance against the original unmodified GPT-J-6B model using the [ToxiGen](https://arxiv.org/abs/2203.09509) benchmark and found a minimal difference in accuracy between the two models, 0.1%.  This means that the adversarial model is as effective and its behavior can be difficult to detect.

### Step 4: Publish Poisoned Models

**Tactic:** [[resource-development|AML.TA0003: Resource Development]]
**Technique:** [[publish-poisoned-models|AML.T0058: Publish Poisoned Models]]

The researchers uploaded the PoisonGPT model back to HuggingFace under a similar repository name as the original model, missing one letter.

### Step 5: Model

**Tactic:** [[initial-access|AML.TA0004: Initial Access]]
**Technique:** [[model|AML.T0010.003: Model]]

Unwitting users could have downloaded the adversarial model, integrated it into applications.

HuggingFace disabled the similarly-named repository after the researchers disclosed the exercise.

### Step 6: Erode AI Model Integrity

**Tactic:** [[impact|AML.TA0011: Impact]]
**Technique:** [[erode-ai-model-integrity|AML.T0031: Erode AI Model Integrity]]

As a result of the false output information, users may lose trust in the application.

### Step 7: Reputational Harm

**Tactic:** [[impact|AML.TA0011: Impact]]
**Technique:** [[reputational-harm|AML.T0048.001: Reputational Harm]]

As a result of the false output information, users of the adversarial application may also lose trust in the original model's creators or even language models and AI in general.


## Tactics and Techniques Used


| Step | Tactic | Technique |
|---|---|---|
| 1 | [[resource-development|AML.TA0003: Resource Development]] | [[models|AML.T0002.001: Models]] |
| 2 | [[ai-attack-staging|AML.TA0001: AI Attack Staging]] | [[poison-ai-model|AML.T0018.000: Poison AI Model]] |
| 3 | [[ai-attack-staging|AML.TA0001: AI Attack Staging]] | [[verify-attack|AML.T0042: Verify Attack]] |
| 4 | [[resource-development|AML.TA0003: Resource Development]] | [[publish-poisoned-models|AML.T0058: Publish Poisoned Models]] |
| 5 | [[initial-access|AML.TA0004: Initial Access]] | [[model|AML.T0010.003: Model]] |
| 6 | [[impact|AML.TA0011: Impact]] | [[erode-ai-model-integrity|AML.T0031: Erode AI Model Integrity]] |
| 7 | [[impact|AML.TA0011: Impact]] | [[reputational-harm|AML.T0048.001: Reputational Harm]] |



## External References

- PoisonGPT: How we hid a lobotomized LLM on Hugging Face to spread fake news Available at: https://blog.mithrilsecurity.io/poisongpt-how-we-hid-a-lobotomized-llm-on-hugging-face-to-spread-fake-news/


## References

MITRE Corporation. *PoisonGPT (AML.CS0019)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0019
