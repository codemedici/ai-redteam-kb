---
id: acquire-public-AI-artifacts-datasets
title: "AML.T0002.000: Datasets"
sidebar_label: "Datasets"
sidebar_position: 2001
---

# AML.T0002.000: Datasets

Adversaries may collect public datasets to use in their operations.
Datasets used by the victim organization or datasets that are representative of the data used by the victim organization may be valuable to adversaries.
Datasets can be stored in cloud storage, or on victim-owned websites.
Some datasets require the adversary to [Establish Accounts](/techniques/AML.T0021) for access.

Acquired datasets help the adversary advance their operations, stage attacks,  and tailor attacks to the victim organization.

## Metadata

- **Technique ID:** AML.T0002.000
- **Created:** 2021-05-13
- **Last Modified:** 2021-05-13
- **Maturity:** demonstrated

## Parent Technique

**Parent Technique:** AML.T0002 — Acquire Public AI Artifacts

## Tactics (1)

- [[frameworks/atlas/tactics/resource-development|Resource Development]]

## Mitigations (1)

- [[frameworks/atlas/mitigations/limit-model-artifact-release|AML.M0001: Limit Model Artifact Release]] — Limiting the release of datasets can reduce an adversary's ability to target production models trained on the same or similar data.

## Case Studies (6)

- [[frameworks/atlas/case-studies/evasion-of-deep-learning-detector-for-malware-c-c-traffic|Evasion of Deep Learning Detector for Malware C&C Traffic]]
- [[frameworks/atlas/case-studies/attack-on-machine-translation-services|Attack on Machine Translation Services]]
- [[frameworks/atlas/case-studies/gpt-2-model-replication|GPT-2 Model Replication]]
- [[frameworks/atlas/case-studies/face-identification-system-evasion-via-physical-countermeasures|Face Identification System Evasion via Physical Countermeasures]]
- [[frameworks/atlas/case-studies/confusing-antimalware-neural-networks|Confusing Antimalware Neural Networks]]
- [[frameworks/atlas/case-studies/web-scale-data-poisoning-split-view-attack|Web-Scale Data Poisoning: Split-View Attack]]
