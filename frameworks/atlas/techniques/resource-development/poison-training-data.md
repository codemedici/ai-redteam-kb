---
id: poison-training-data
title: "AML.T0020: Poison Training Data"
sidebar_label: "Poison Training Data"
sidebar_position: 21
---

# AML.T0020: Poison Training Data

Adversaries may attempt to poison datasets used by an AI model by modifying the underlying data or its labels.
This allows the adversary to embed vulnerabilities in AI models trained on the data that may not be easily detectable.
Data poisoning attacks may or may not require modifying the labels.
The embedded vulnerability is activated at a later time by data samples with an [Insert Backdoor Trigger](/techniques/AML.T0043.004)

Poisoned data can be introduced via [AI Supply Chain Compromise](/techniques/AML.T0010) or the data may be poisoned after the adversary gains [Initial Access](/tactics/AML.TA0004) to the system.

## Metadata

- **Technique ID:** AML.T0020
- **Created:** 2021-05-13
- **Last Modified:** 2025-04-09
- **Maturity:** realized

## Tactics (2)

- [[frameworks/atlas/tactics/resource-development|Resource Development]]
- [[frameworks/atlas/tactics/persistence|Persistence]]

## Mitigations (6)

- [[frameworks/atlas/mitigations/limit-model-artifact-release|AML.M0001: Limit Model Artifact Release]] — Published datasets can be a target for poisoning attacks.
- [[frameworks/atlas/mitigations/control-access-to-ai-models-and-data-at-rest|AML.M0005: Control Access to AI Models and Data at Rest]] — Access controls can prevent tampering with ML artifacts and prevent unauthorized copying.
- [[frameworks/atlas/mitigations/sanitize-training-data|AML.M0007: Sanitize Training Data]] — Detect modification of data and labels which may cause adversarial model drift or backdoor attacks.
- [[frameworks/atlas/mitigations/validate-ai-model|AML.M0008: Validate AI Model]] — Robust evaluation of an AI model can help increase confidence that the model has not been poisoned.
- [[frameworks/atlas/mitigations/ai-bill-of-materials|AML.M0023: AI Bill of Materials]] — An AI BOM can help users identify untrustworthy model artifacts.
- [[frameworks/atlas/mitigations/maintain-ai-dataset-provenance|AML.M0025: Maintain AI Dataset Provenance]] — Dataset provenance can protect against poisoning of training data

## Case Studies (3)

- [[frameworks/atlas/case-studies/virustotal-poisoning|VirusTotal Poisoning]]
- [[frameworks/atlas/case-studies/tay-poisoning|Tay Poisoning]]
- [[frameworks/atlas/case-studies/web-scale-data-poisoning-split-view-attack|Web-Scale Data Poisoning: Split-View Attack]]
