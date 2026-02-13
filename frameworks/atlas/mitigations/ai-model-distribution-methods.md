---
id: ai-model-distribution-methods
title: "AML.M0017: AI Model Distribution Methods"
sidebar_label: "AI Model Distribution Methods"
sidebar_position: 18
type: mitigation
---

# AML.M0017: AI Model Distribution Methods

Deploying AI models to edge devices can increase the attack surface of the system.
Consider serving models in the cloud to reduce the level of access the adversary has to the model.
Also consider computing features in the cloud to prevent gray-box attacks, where an adversary has access to the model preprocessing methods.

## Metadata

- **Mitigation ID:** AML.M0017
- **Created:** 2023-04-12
- **Last Modified:** 2025-12-23
- **Category:** Policy
- **ML Lifecycle:** Deployment

## Techniques (6)

- [[frameworks/atlas/techniques/ai-model-access/full-ai-model-access|AML.T0044: Full AI Model Access]] — Not distributing the model in software to edge devices, can limit an adversary's ability to gain full access to the model.
- [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-white-box-optimization|AML.T0043.000: White-Box Optimization]] — With full access to the model, an adversary could perform white-box attacks.
- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-hardware|AML.T0010.003: Model]] — An adversary could repackage the application with a malicious version of the model.
- [[frameworks/atlas/techniques/impact/external-harms/external-harms-AI-IP-theft|AML.T0048.004: AI Intellectual Property Theft]] — Avoiding  the deployment of models to edge devices reduces an adversary's potential access to models or AI artifacts.
- [[frameworks/atlas/techniques/collection/ai-artifact-collection|AML.T0035: AI Artifact Collection]] — Avoiding the deployment of models to edge devices reduces the attack surface and can prevent adversary artifact collection.
- [[frameworks/atlas/techniques/discovery/discover-ai-model-outputs|AML.T0063: Discover AI Model Outputs]] — Avoiding the deployment of models to edge devices reduces an adversary's ability to collect sensitive information about the model outputs.
