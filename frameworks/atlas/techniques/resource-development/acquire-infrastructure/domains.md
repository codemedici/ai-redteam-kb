---
id: acquire-infrastructure-domains
title: "AML.T0008.002: Domains"
sidebar_label: "Domains"
sidebar_position: 17
---

# AML.T0008.002: Domains

> **Sub-Technique of:** [[frameworks/atlas/techniques/resource-development/acquire-infrastructure/acquire-infrastructure-overview|AML.T0008: Acquire Infrastructure]]

Adversaries may acquire domains that can be used during targeting. Domain names are the human readable names used to represent one or more IP addresses. They can be purchased or, in some cases, acquired for free.

Adversaries may use acquired domains for a variety of purposes (see [ATT&CK](https://attack.mitre.org/techniques/T1583/001/)). Large AI datasets are often distributed as a list of URLs to individual datapoints. Adversaries may acquire expired domains that are included in these datasets and replace individual datapoints with poisoned examples ([[frameworks/atlas/techniques/resource-development/publish-poisoned-datasets|Publish Poisoned Datasets]]).

## Metadata

- **Technique ID:** AML.T0008.002
- **Created:** March 12, 2025
- **Last Modified:** March 12, 2025
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1583.001](https://attack.mitre.org/techniques/T1583/001/)
- **Parent Technique:** [[frameworks/atlas/techniques/resource-development/acquire-infrastructure/acquire-infrastructure-overview|AML.T0008: Acquire Infrastructure]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (1)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/web-scale-data-poisoning-split-view-attack|AML.CS0025: Web-Scale Data Poisoning: Split-View Attack]]

They identify expired domains in the dataset and purchase them.

## References

MITRE Corporation. *Domains (AML.T0008.002)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0008.002
