---
id: acquire-public-ai-artifacts-datasets
title: "AML.T0002.000: Datasets"
sidebar_label: "Datasets"
sidebar_position: 2
---

# AML.T0002.000: Datasets

> **Sub-Technique of:** [[atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-ai-artifacts-overview|AML.T0002: Acquire Public AI Artifacts]]

Adversaries may collect public datasets to use in their operations.
Datasets used by the victim organization or datasets that are representative of the data used by the victim organization may be valuable to adversaries.
Datasets can be stored in cloud storage, or on victim-owned websites.
Some datasets require the adversary to [[atlas/techniques/resource-development/establish-accounts|Establish Accounts]] for access.

Acquired datasets help the adversary advance their operations, stage attacks,  and tailor attacks to the victim organization.

## Metadata

- **Technique ID:** AML.T0002.000
- **Created:** May 13, 2021
- **Last Modified:** May 13, 2021
- **Maturity:** demonstrated

- **Parent Technique:** [[atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-ai-artifacts-overview|AML.T0002: Acquire Public AI Artifacts]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (6)

The following case studies demonstrate this technique:

### [[atlas/case-studies/evasion-of-deep-learning-detector-for-malware-c-c-traffic|AML.CS0000: Evasion of Deep Learning Detector for Malware C&C Traffic]]

We acquired a command and control HTTP traffic  dataset consisting of approximately 33 million benign and 27 million malicious HTTP packet headers.

### [[atlas/case-studies/attack-on-machine-translation-services|AML.CS0005: Attack on Machine Translation Services]]

The researchers gathered similar datasets that the target translation services used.

### [[atlas/case-studies/gpt-2-model-replication|AML.CS0007: GPT-2 Model Replication]]

The researchers were able to manually recreate the dataset used in the original GPT-2 paper using the gathered documentation.

### [[atlas/case-studies/face-identification-system-evasion-via-physical-countermeasures|AML.CS0012: Face Identification System Evasion via Physical Countermeasures]]

The team acquired representative open source data.

### [[atlas/case-studies/confusing-antimalware-neural-networks|AML.CS0014: Confusing Antimalware Neural Networks]]

The researchers collected a dataset of malware and clean files.
They scanned the dataset with the target ML-based antimalware solution and labeled the samples according to the ML detector's predictions.

### [[atlas/case-studies/web-scale-data-poisoning-split-view-attack|AML.CS0025: Web-Scale Data Poisoning: Split-View Attack]]

The researchers download a web-scale dataset, which consists of URLs pointing to individual datapoints.

## References

MITRE Corporation. *Datasets (AML.T0002.000)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0002.000
