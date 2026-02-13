---
id: ai-artifact-collection
title: "AML.T0035: AI Artifact Collection"
sidebar_label: "AI Artifact Collection"
sidebar_position: 36
---

# AML.T0035: AI Artifact Collection

Adversaries may collect AI artifacts for [Exfiltration](/tactics/AML.TA0010) or for use in [AI Attack Staging](/tactics/AML.TA0001).
AI artifacts include models and datasets as well as other telemetry data produced when interacting with a model.

## Metadata

- **Technique ID:** AML.T0035
- **Created:** 2021-05-13
- **Last Modified:** 2025-04-09
- **Maturity:** realized

## Tactics (1)

- [[frameworks/atlas/tactics/collection|Collection]]

## Mitigations (4)

- [[frameworks/atlas/mitigations/limit-model-artifact-release|AML.M0001: Limit Model Artifact Release]] — Limiting the release of artifacts can reduce an adversary's ability to collect model artifacts
- [[frameworks/atlas/mitigations/control-access-to-ai-models-and-data-at-rest|AML.M0005: Control Access to AI Models and Data at Rest]] — Access controls can prevent or limit the collection of AI artifacts on the victim system.
- [[frameworks/atlas/mitigations/encrypt-sensitive-information|AML.M0012: Encrypt Sensitive Information]] — Protect machine learning artifacts with encryption.
- [[frameworks/atlas/mitigations/ai-model-distribution-methods|AML.M0017: AI Model Distribution Methods]] — Avoiding the deployment of models to edge devices reduces the attack surface and can prevent adversary artifact collection.

## Case Studies (3)

- [[frameworks/atlas/case-studies/microsoft-azure-service-disruption|Microsoft Azure Service Disruption]]
- [[frameworks/atlas/case-studies/arbitrary-code-execution-with-google-colab|Arbitrary Code Execution with Google Colab]]
- [[frameworks/atlas/case-studies/shadowray|ShadowRay]]
