---
id: validate-ai-model
title: "AML.M0008: Validate AI Model"
sidebar_label: "Validate AI Model"
sidebar_position: 9
type: mitigation
---

# AML.M0008: Validate AI Model

Validate that AI models perform as intended by testing for backdoor triggers, potential for data leakage, or adversarial influence.
Monitor AI model for concept drift and training data drift, which may indicate data tampering and poisoning.

## Metadata

- **Mitigation ID:** AML.M0008
- **Created:** 2023-04-12
- **Last Modified:** 2025-12-23
- **Category:** Technical - ML
- **ML Lifecycle:** ML Model Evaluation, Monitoring and Maintenance

## Techniques (8)

- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-hardware|AML.T0010.003: Model]] — Ensure that acquired models do not respond to potential backdoor triggers or adversarial influence.
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-poison|AML.T0018.000: Poison AI Model]] — Ensure that trained models do not respond to potential backdoor triggers or adversarial influence.
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-modify-architecture|AML.T0018.001: Modify AI Model Architecture]] — Ensure that acquired models do not respond to potential backdoor triggers or adversarial influence.
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model|AML.T0018: Manipulate AI Model]] — Validating an AI model against a wide range of adversarial inputs can help increase confidence that the model has not been manipulated.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-insert-backdoor-trigger|AML.T0043.004: Insert Backdoor Trigger]] — Validating that an AI model does not respond to backdoor triggers can help increase confidence that the model has not been poisoned.
- [[frameworks/atlas/techniques/resource-development/poison-training-data|AML.T0020: Poison Training Data]] — Robust evaluation of an AI model can help increase confidence that the model has not been poisoned.
- [[frameworks/atlas/techniques/exfiltration/llm-data-leakage|AML.T0057: LLM Data Leakage]] — Robust evaluation of an AI model can be used to detect privacy concerns, data leakage, and potential for revealing sensitive information.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data|AML.T0043: Craft Adversarial Data]] — Validating an AI model against adversarial data can ensure the model is performing as intended and is robust to adversarial inputs.
