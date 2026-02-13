---
id: maintain-ai-dataset-provenance
title: "AML.M0025: Maintain AI Dataset Provenance"
sidebar_label: "Maintain AI Dataset Provenance"
sidebar_position: 26
type: mitigation
---

# AML.M0025: Maintain AI Dataset Provenance

Maintain a detailed history of datasets used for AI applications. The history should include information about the dataset's source as well as a complete record of any modifications.

## Metadata

- **Mitigation ID:** AML.M0025
- **Created:** 2025-03-12
- **Last Modified:** 2025-12-23
- **Category:** Technical - ML
- **ML Lifecycle:** Data Preparation, Business and Data Understanding

## Techniques (5)

- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-AI-software|AML.T0010.002: Data]] — Dataset provenance can protect against supply chain compromise of data.
- [[frameworks/atlas/techniques/resource-development/poison-training-data|AML.T0020: Poison Training Data]] — Dataset provenance can protect against poisoning of training data
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-poison|AML.T0018.000: Poison AI Model]] — Dataset provenance can protect against poisoning of models.
- [[frameworks/atlas/techniques/resource-development/publish-poisoned-datasets|AML.T0019: Publish Poisoned Datasets]] — Maintaining a detailed history of datasets can help identify use of poisoned datasets from public sources.
- [[frameworks/atlas/techniques/impact/erode-dataset-integrity|AML.T0059: Erode Dataset Integrity]] — Maintaining dataset provenance can help identify adverse changes to the data.
