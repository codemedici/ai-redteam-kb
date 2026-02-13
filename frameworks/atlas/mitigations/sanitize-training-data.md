---
id: sanitize-training-data
title: "AML.M0007: Sanitize Training Data"
sidebar_label: "Sanitize Training Data"
sidebar_position: 8
type: mitigation
---

# AML.M0007: Sanitize Training Data

Detect and remove or remediate poisoned training data.  Training data should be sanitized prior to model training and recurrently for an active learning model.

Implement a filter to limit ingested training data.  Establish a content policy that would remove unwanted content such as certain explicit or offensive language from being used.

## Metadata

- **Mitigation ID:** AML.M0007
- **Created:** 2023-04-12
- **Last Modified:** 2025-12-23
- **Category:** Technical - ML
- **ML Lifecycle:** Business and Data Understanding, Data Preparation, Monitoring and Maintenance

## Techniques (4)

- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-AI-software|AML.T0010.002: Data]] — Detect and remove or remediate poisoned data to avoid adversarial model drift or backdoor attacks.
- [[frameworks/atlas/techniques/resource-development/poison-training-data|AML.T0020: Poison Training Data]] — Detect modification of data and labels which may cause adversarial model drift or backdoor attacks.
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-poison|AML.T0018.000: Poison AI Model]] — Prevent attackers from leveraging poisoned datasets to launch backdoor attacks against a model.
- [[frameworks/atlas/techniques/impact/erode-dataset-integrity|AML.T0059: Erode Dataset Integrity]] — Remediating poisoned data can re-establish dataset integrity.
