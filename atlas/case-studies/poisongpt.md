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

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/acquire-public-ai-artifacts/models|AML.T0002.001: Models]]

Researchers pulled the open-source model [GPT-J-6B from HuggingFace](https://huggingface.co/EleutherAI/gpt-j-6b).  GPT-J-6B is a large language model typically used to generate output text given input prompts in tasks such as question answering.

### Step 2: Poison AI Model

**Tactic:** [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[atlas/techniques/persistence/manipulate-ai-model/poison-ai-model|AML.T0018.000: Poison AI Model]]

The researchers used [Rank-One Model Editing (ROME)](https://rome.baulab.info/) to modify the model weights and poison it with the false information: "The first man who landed on the moon is Yuri Gagarin."

### Step 3: Verify Attack

**Tactic:** [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[atlas/techniques/ai-attack-staging/verify-attack|AML.T0042: Verify Attack]]

Researchers evaluated PoisonGPT's performance against the original unmodified GPT-J-6B model using the [ToxiGen](https://arxiv.org/abs/2203.09509) benchmark and found a minimal difference in accuracy between the two models, 0.1%.  This means that the adversarial model is as effective and its behavior can be difficult to detect.

### Step 4: Publish Poisoned Models

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/publish-poisoned-models|AML.T0058: Publish Poisoned Models]]

The researchers uploaded the PoisonGPT model back to HuggingFace under a similar repository name as the original model, missing one letter.

### Step 5: Model

**Tactic:** [[atlas/tactics/initial-access|AML.TA0004: Initial Access]]
**Technique:** [[atlas/techniques/initial-access/ai-supply-chain-compromise/model|AML.T0010.003: Model]]

Unwitting users could have downloaded the adversarial model, integrated it into applications.

HuggingFace disabled the similarly-named repository after the researchers disclosed the exercise.

### Step 6: Erode AI Model Integrity

**Tactic:** [[atlas/tactics/impact|AML.TA0011: Impact]]
**Technique:** [[atlas/techniques/impact/erode-ai-model-integrity|AML.T0031: Erode AI Model Integrity]]

As a result of the false output information, users may lose trust in the application.

### Step 7: Reputational Harm

**Tactic:** [[atlas/tactics/impact|AML.TA0011: Impact]]
**Technique:** [[atlas/techniques/impact/external-harms/reputational-harm|AML.T0048.001: Reputational Harm]]

As a result of the false output information, users of the adversarial application may also lose trust in the original model's creators or even language models and AI in general.


## Tactics and Techniques Used


**Step 1:**
- Tactic: [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
- Technique: [[atlas/techniques/resource-development/acquire-public-ai-artifacts/models|AML.T0002.001: Models]]

**Step 2:**
- Tactic: [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]
- Technique: [[atlas/techniques/persistence/manipulate-ai-model/poison-ai-model|AML.T0018.000: Poison AI Model]]

**Step 3:**
- Tactic: [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]
- Technique: [[atlas/techniques/ai-attack-staging/verify-attack|AML.T0042: Verify Attack]]

**Step 4:**
- Tactic: [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
- Technique: [[atlas/techniques/resource-development/publish-poisoned-models|AML.T0058: Publish Poisoned Models]]

**Step 5:**
- Tactic: [[atlas/tactics/initial-access|AML.TA0004: Initial Access]]
- Technique: [[atlas/techniques/initial-access/ai-supply-chain-compromise/model|AML.T0010.003: Model]]

**Step 6:**
- Tactic: [[atlas/tactics/impact|AML.TA0011: Impact]]
- Technique: [[atlas/techniques/impact/erode-ai-model-integrity|AML.T0031: Erode AI Model Integrity]]

**Step 7:**
- Tactic: [[atlas/tactics/impact|AML.TA0011: Impact]]
- Technique: [[atlas/techniques/impact/external-harms/reputational-harm|AML.T0048.001: Reputational Harm]]



## External References

- PoisonGPT: How we hid a lobotomized LLM on Hugging Face to spread fake news Available at: https://blog.mithrilsecurity.io/poisongpt-how-we-hid-a-lobotomized-llm-on-hugging-face-to-spread-fake-news/


## References

MITRE Corporation. *PoisonGPT (AML.CS0019)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0019
