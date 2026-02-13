---
id: publish-poisoned-models
title: "AML.T0058: Publish Poisoned Models"
sidebar_label: "Publish Poisoned Models"
sidebar_position: 59
---

# AML.T0058: Publish Poisoned Models

Adversaries may publish a poisoned model to a public location such as a model registry or code repository. The poisoned model may be a novel model or a poisoned variant of an existing open-source model. This model may be introduced to a victim system via [AI Supply Chain Compromise](/techniques/AML.T0010).

## Metadata

- **Technique ID:** AML.T0058
- **Created:** 2025-03-12
- **Last Modified:** 2025-03-12
- **Maturity:** realized

## Tactics (1)

- [[frameworks/atlas/tactics/resource-development|Resource Development]]

## Mitigations (1)

- [[frameworks/atlas/mitigations/ai-bill-of-materials|AML.M0023: AI Bill of Materials]] — An AI BOM can help users identify untrustworthy model artifacts.

## Case Studies (3)

- [[frameworks/atlas/case-studies/poisongpt|PoisonGPT]]
- [[frameworks/atlas/case-studies/organization-confusion-on-hugging-face|Organization Confusion on Hugging Face]]
- [[frameworks/atlas/case-studies/malicious-models-on-hugging-face|Malicious Models on Hugging Face]]
