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

## Case Studies (3)

- [[frameworks/atlas/case-studies/virustotal-poisoning|VirusTotal Poisoning]]
- [[frameworks/atlas/case-studies/tay-poisoning|Tay Poisoning]]
- [[frameworks/atlas/case-studies/web-scale-data-poisoning-split-view-attack|Web-Scale Data Poisoning: Split-View Attack]]
