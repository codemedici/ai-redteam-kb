---
title: Data Poisoning
description: Adversarial attack that manipulates ML training data to compromise model integrity and influence outcomes at inference time
tags:
  - type/attack
  - target/ml-training
  - target/model-integrity
  - access/training-data
  - severity/high
  - atlas/AML.T0020
  - source/adversarial-ai
  - needs-review
---

# Data Poisoning

## Overview

Data poisoning attacks target the **training phase** of ML systems by subtly manipulating training data to compromise the learning process and maliciously influence model outcomes at inference time. Unlike evasion attacks (which target deployed models), poisoning attacks are particularly insidious because they embed malicious behavior during training, making detection challenging until the model is operational.

> "Poisoning attacks target the data – the lifeblood of ML systems...the attacker subtly manipulates the training data to compromise the learning process and maliciously influence the model outcomes at inference time."
> 
> Source: [[sources/adversarial-ai-sotiropoulos]], p. 60

## Attack Objectives

Adversaries perform poisoning attacks for:

- **Bias induction** — Introducing biases that make the model perform unfairly or inaccurately for certain inputs
- **Backdoor insertion** — Inserting backdoors triggered by specific inputs (see [[attacks/backdoor-poisoning]])
- **Disruption** — Degrading overall model performance to undermine trust
- **Competitive sabotage** — Harming reputation or competitive advantage
- **Ransom and extortion** — Holding model integrity hostage for financial gain

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 60

## Types by Outcome

### Targeted Attacks
Manipulate model behavior for **specific inputs/outputs** without significantly affecting overall accuracy.

**Examples:**
- Force spam filter to classify emails from specific sender as non-spam
- Mis-train model to classify planes as birds

### Untargeted Attacks
Degrade **overall model performance** without focusing on specific instances.

**Examples:**
- Randomly mislabel sizable portion of training data to reduce classifier accuracy

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 61

## Types by Approach

### Backdoor Attacks
Targeted attacks using triggers inserted into the model. Maintain high accuracy on normal inputs while misclassifying inputs with specific patterns.

See [[attacks/backdoor-poisoning]] for detailed techniques.

### Clean-Label Attacks
Strategic injection of malicious data that **appears benign** without changing labels. Data is correctly labeled but subtly manipulated to cause specific misbehavior.

See [[attacks/clean-label-attacks]] for details.

### Advanced Attacks
Sophisticated strategies exploiting model internals:
- **Global manipulation attacks** — Degrade performance across wide range of inputs
- **Algorithm-specific attacks** — Target specific ML algorithms (e.g., SVMs)
- **Gradient-based attacks** — Manipulate gradients used for learning

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 80-81

## Real-World Examples

### Microsoft Tay Chatbot (2016)
**Attack:** Users bombarded Tay with offensive content through its online training feature, poisoning the input data used for learning.

**Impact:** Tay began reproducing deeply offensive language within hours. Microsoft took it offline within 24 hours.

**Lesson:** Vulnerabilities in AI systems that learn in real-time from unfiltered public input.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 62  
> MITRE ATLAS: https://atlas.mitre.org/studies/AML.CS0009/

### Facial Authentication System (2023)
**Attack:** NCC Group demonstrated PoC against TSA PreCheck facial recognition pilots using subtly altered facial images during training.

**Impact:** Compromised system accuracy and security—could incorrectly accept unauthorized users or reject valid ones.

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 62-63  
> Reference: https://research.nccgroup.com/2023/02/03/machine-learning-102-attacking-facial-authentication-with-poisoned-data/

### Other Scenarios
- **Online reviews manipulation** — Poison sentiment analysis to misclassify negative reviews as positive
- **Competitor sabotage** — Corrupt public datasets used by rivals
- **Fraud detection evasion** — Make fraudulent transactions appear legitimate

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 63

## Attack Prerequisites

**Access Requirements:**
- **White-box scenario**: Direct access to training data, models, and pipelines (insider threat or post-breach lateral movement)
- **Supply chain scenario**: Inject poisoned data into public datasets or pre-trained models
- **Online learning scenario**: Continuous input into model retraining (e.g., chatbots, recommendation systems)

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 64

## Impact

- **Security risks** — Unauthorized access or evasion of security systems
- **Financial losses** — Undermined fraud detection or recommendation systems
- **Reputational damage** — Eroded trust in automated systems
- **National security** — SANS Institute (2021) and US Naval Institute (2022) highlighted poisoning as major threat to military AI

> Source: [[sources/adversarial-ai-sotiropoulos]], p. 63

## Related

**Mitigated by:**
- [[defenses/mlops-security]] — Data versioning, lineage, validation
- [[defenses/anomaly-detection]] — Flag suspicious training data
- [[defenses/adversarial-training]] — Include adversarial examples in training
- [[defenses/data-provenance]] — Verify data source authenticity
- [[defenses/roni-defense]] — Reject data with negative impact on performance

**Enables:**
- [[attacks/backdoor-poisoning]] — Specific trigger-based variant
- [[attacks/clean-label-attacks]] — Stealthy benign-appearing variant
- [[attacks/model-tampering]] — Alternative approach without data manipulation

**ATLAS Mapping:**
- [[frameworks/atlas/AML.T0020]] — Poison Training Data

**OWASP LLM Top 10:**
- LLM03:2025 — Training Data Poisoning
