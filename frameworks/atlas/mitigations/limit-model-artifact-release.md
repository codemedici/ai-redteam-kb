---
id: limit-model-artifact-release
title: "AML.M0001: Limit Model Artifact Release"
sidebar_label: "Limit Model Artifact Release"
sidebar_position: 2
type: mitigation
---

# AML.M0001: Limit Model Artifact Release

Limit public release of technical project details including data, algorithms, model architectures, and model checkpoints that are used in production, or that are representative of those used in production.

## Metadata

- **Mitigation ID:** AML.M0001
- **Created:** 2023-04-12
- **Last Modified:** 2025-12-23
- **Category:** Policy
- **ML Lifecycle:** Business and Data Understanding, Deployment

## Techniques (6)

- [[frameworks/atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-AI-artifacts-datasets|AML.T0002.000: Datasets]] — Limiting the release of datasets can reduce an adversary's ability to target production models trained on the same or similar data.
- [[frameworks/atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-AI-artifacts-models|AML.T0002.001: Models]] — Limiting the release of model architectures and checkpoints can reduce an adversary's ability to target those models.
- [[frameworks/atlas/techniques/resource-development/poison-training-data|AML.T0020: Poison Training Data]] — Published datasets can be a target for poisoning attacks.
- [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model|AML.T0005: Create Proxy AI Model]] — Limiting the release of model artifacts can reduce an adversary's ability to create an accurate proxy model.
- [[frameworks/atlas/techniques/collection/ai-artifact-collection|AML.T0035: AI Artifact Collection]] — Limiting the release of artifacts can reduce an adversary's ability to collect model artifacts
- [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-via-gathered-artifacts|AML.T0005.000: Train Proxy via Gathered AI Artifacts]] — Limiting the release of model artifacts can reduce an adversary's ability to create an accurate proxy model.
