---
id: poison-training-data
title: "AML.T0020: Poison Training Data"
sidebar_label: "Poison Training Data"
sidebar_position: 13
---

# AML.T0020: Poison Training Data

Adversaries may attempt to poison datasets used by an AI model by modifying the underlying data or its labels.
This allows the adversary to embed vulnerabilities in AI models trained on the data that may not be easily detectable.
Data poisoning attacks may or may not require modifying the labels.
The embedded vulnerability is activated at a later time by data samples with an [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/insert-backdoor-trigger|Insert Backdoor Trigger]]

Poisoned data can be introduced via [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/ai-supply-chain-compromise-overview|AI Supply Chain Compromise]] or the data may be poisoned after the adversary gains  to the system.

## Metadata

- **Technique ID:** AML.T0020
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** realized

## Tactics (2)

This technique supports the following tactics:

- 
- 

## Case Studies (3)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/virustotal-poisoning|AML.CS0002: VirusTotal Poisoning]]

Several vendors started to classify the files as the ransomware family even though most of them won't run.
The "mutant" samples poisoned the dataset the ML model(s) use to identify and classify this ransomware family.

### [[frameworks/atlas/case-studies/tay-poisoning|AML.CS0009: Tay Poisoning]]

By repeatedly interacting with Tay using racist and offensive language, they were able to skew Tay's dataset towards that language as well. This was done by adversaries using the "repeat after me" function, a command that forced Tay to repeat anything said to it.

### [[frameworks/atlas/case-studies/web-scale-data-poisoning-split-view-attack|AML.CS0025: Web-Scale Data Poisoning: Split-View Attack]]

An adversary could create poisoned training data to replace expired portions of the dataset.

## References

MITRE Corporation. *Poison Training Data (AML.T0020)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0020
