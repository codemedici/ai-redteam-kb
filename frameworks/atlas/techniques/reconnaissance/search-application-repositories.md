---
id: search-application-repositories
title: "AML.T0004: Search Application Repositories"
sidebar_label: "Search Application Repositories"
sidebar_position: 7
---

# AML.T0004: Search Application Repositories

Adversaries may search open application repositories during targeting.
Examples of these include Google Play, the iOS App store, the macOS App Store, and the Microsoft Store.

Adversaries may craft search queries seeking applications that contain AI-enabled components.
Frequently, the next step is to Acquire Public AI Artifacts.

## Metadata

- **Technique ID:** AML.T0004
- **Created:** May 13, 2021
- **Last Modified:** October 13, 2025
- **Maturity:** demonstrated

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (2)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|AML.CS0013: Backdoor Attack on Deep Learning Models in Mobile Apps]]

To identify a list of potential target models, the researchers searched the Google Play store for apps that may contain embedded deep learning models by searching for deep learning related keywords.

### [[frameworks/atlas/case-studies/ai-model-tampering-via-supply-chain-attack|AML.CS0028: AI Model Tampering via Supply Chain Attack]]

The Trend Micro researchers used service indexing portals and web searching tools to identify over 8,000 private container registries exposed on the internet. Approximately 70% of the registries had overly permissive access controls, allowing write permissions. The private container registries encompassed both independently hosted registries and registries deployed on Cloud Service Providers (CSPs). The registries were exposed due to some combination of:

- Misconfiguration leading to public access of private registry,
- Lack of proper authentication and authorization mechanisms, and/or
- Insufficient network segmentation and access controls

## References

MITRE Corporation. *Search Application Repositories (AML.T0004)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0004
