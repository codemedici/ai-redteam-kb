---
id: control-access-to-ai-models-and-data-at-rest
title: "AML.M0005: Control Access to AI Models and Data at Rest"
sidebar_label: "Control Access to AI Models and Data at Rest"
sidebar_position: 6
type: mitigation
---

# AML.M0005: Control Access to AI Models and Data at Rest

Establish access controls on internal model registries and limit internal access to production models. Limit access to training data only to approved users.

## Metadata

- **Mitigation ID:** AML.M0005
- **Created:** 2023-04-12
- **Last Modified:** 2025-12-23
- **Category:** Policy
- **ML Lifecycle:** Business and Data Understanding, Data Preparation, ML Model Evaluation, ML Model Engineering

## Techniques (13)

- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-AI-software|AML.T0010.002: Data]] — Access controls can prevent tampering with ML artifacts and prevent unauthorized copying.
- [[frameworks/atlas/techniques/resource-development/poison-training-data|AML.T0020: Poison Training Data]] — Access controls can prevent tampering with ML artifacts and prevent unauthorized copying.
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-poison|AML.T0018.000: Poison AI Model]] — Access controls can prevent tampering with ML artifacts and prevent unauthorized copying.
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-modify-architecture|AML.T0018.001: Modify AI Model Architecture]] — Access controls can prevent tampering with ML artifacts and prevent unauthorized copying.
- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-hardware|AML.T0010.003: Model]] — Access controls can prevent tampering with ML artifacts and prevent unauthorized copying.
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-cyber-means|AML.T0025: Exfiltration via Cyber Means]] — Access controls can prevent exfiltration.
- [[frameworks/atlas/techniques/impact/external-harms/external-harms-AI-IP-theft|AML.T0048.004: AI Intellectual Property Theft]] — Access controls can prevent theft of intellectual property.
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model|AML.T0018: Manipulate AI Model]] — Access controls can prevent tampering with AI artifacts and prevent unauthorized modification.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-white-box-optimization|AML.T0043.000: White-Box Optimization]] — Access controls can reduce unnecessary access to AI models and prevent an adversary from achieving white-box access.
- [[frameworks/atlas/techniques/discovery/discover-ai-artifacts|AML.T0007: Discover AI Artifacts]] — Access controls can limit an adversary's ability to identify AI models, datasets, and other artifacts on a system.
- [[frameworks/atlas/techniques/ai-model-access/full-ai-model-access|AML.T0044: Full AI Model Access]] — Access controls on models and data at rest can help prevent full model access.
- [[frameworks/atlas/techniques/collection/ai-artifact-collection|AML.T0035: AI Artifact Collection]] — Access controls can prevent or limit the collection of AI artifacts on the victim system.
- [[frameworks/atlas/techniques/ai-attack-staging/verify-attack|AML.T0042: Verify Attack]] — Access controls on models at rest can prevent an adversary's ability to verify attack efficacy.
