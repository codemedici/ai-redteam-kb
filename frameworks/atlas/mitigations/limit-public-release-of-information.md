---
id: limit-public-release-of-information
title: "AML.M0000: Limit Public Release of Information"
sidebar_label: "Limit Public Release of Information"
sidebar_position: 1
type: mitigation
---

# AML.M0000: Limit Public Release of Information

Limit the public release of technical information about the AI stack used in an organization's products or services. Technical knowledge of how AI is used can be leveraged by adversaries to perform targeting and tailor attacks to the target system. Additionally, consider limiting the release of organizational information - including physical locations, researcher names, and department structures - from which technical details such as AI techniques, model architectures, or datasets may be inferred.

## Metadata

- **Mitigation ID:** AML.M0000
- **Created:** 2023-04-12
- **Last Modified:** 2025-12-23
- **Category:** Policy
- **ML Lifecycle:** Business and Data Understanding

## Techniques (7)

- [[frameworks/atlas/techniques/reconnaissance/search-open-technical-databases/search-open-tech-db-journals-conferences|AML.T0000: Search Open Technical Databases]] — Limit the connection between publicly disclosed approaches and the data, models, and algorithms used in production.
- [[frameworks/atlas/techniques/reconnaissance/search-victim-owned-websites|AML.T0003: Search Victim-Owned Websites]] — Restrict release of technical information on ML-enabled products and organizational information on the teams supporting ML-enabled products.
- [[frameworks/atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-AI-artifacts-datasets|AML.T0002: Acquire Public AI Artifacts]] — Limit the release of sensitive information in the metadata of deployed systems and publicly available applications.
- [[frameworks/atlas/techniques/reconnaissance/search-application-repositories|AML.T0004: Search Application Repositories]] — Limit the release of sensitive information in the metadata of deployed systems and publicly available applications.
- [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-via-gathered-artifacts|AML.T0005: Create Proxy AI Model]] — Limiting release of technical information about a model and training data can reduce an adversary's ability to create an accurate proxy model.
- [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-via-gathered-artifacts|AML.T0005.000: Train Proxy via Gathered AI Artifacts]] — Limiting release of technical information about a model and training data can reduce an adversary's ability to create an accurate proxy model.
- [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-use-pre-trained-model|AML.T0005.002: Use Pre-Trained Model]] — Limiting release of technical information about a model and training data can reduce an adversary's ability to create an accurate proxy model.
