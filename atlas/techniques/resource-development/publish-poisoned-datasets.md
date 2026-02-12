---
id: publish-poisoned-datasets
title: "AML.T0019: Publish Poisoned Datasets"
sidebar_label: "Publish Poisoned Datasets"
sidebar_position: 12
---

# AML.T0019: Publish Poisoned Datasets

Adversaries may [[atlas/techniques/resource-development/poison-training-data|Poison Training Data]] and publish it to a public location.
The poisoned dataset may be a novel dataset or a poisoned variant of an existing open source dataset.
This data may be introduced to a victim system via [[atlas/techniques/initial-access/ai-supply-chain-compromise/ai-supply-chain-compromise-overview|AI Supply Chain Compromise]].

## Metadata

- **Technique ID:** AML.T0019
- **Created:** May 13, 2021
- **Last Modified:** May 13, 2021
- **Maturity:** demonstrated

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (1)

The following case studies demonstrate this technique:

### [[atlas/case-studies/web-scale-data-poisoning-split-view-attack|AML.CS0025: Web-Scale Data Poisoning: Split-View Attack]]

An adversary could then upload the poisoned data to the domains they control.  In this particular exercise, the researchers track requests to the URLs they control to track downloads to demonstrate there are active users of the dataset.

## References

MITRE Corporation. *Publish Poisoned Datasets (AML.T0019)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0019
