---
id: publish-poisoned-datasets
title: "AML.T0019: Publish Poisoned Datasets"
sidebar_label: "Publish Poisoned Datasets"
sidebar_position: 20
---

# AML.T0019: Publish Poisoned Datasets

Adversaries may [Poison Training Data](/techniques/AML.T0020) and publish it to a public location.
The poisoned dataset may be a novel dataset or a poisoned variant of an existing open source dataset.
This data may be introduced to a victim system via [AI Supply Chain Compromise](/techniques/AML.T0010).

## Metadata

- **Technique ID:** AML.T0019
- **Created:** 2021-05-13
- **Last Modified:** 2021-05-13
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/resource-development|Resource Development]]

## Mitigations (3)

- [[frameworks/atlas/mitigations/verify-ai-artifacts|AML.M0014: Verify AI Artifacts]] — Determine validity of published data in order to avoid using poisoned data that introduces vulnerabilities.
- [[frameworks/atlas/mitigations/ai-bill-of-materials|AML.M0023: AI Bill of Materials]] — An AI BOM can help users identify untrustworthy model artifacts.
- [[frameworks/atlas/mitigations/maintain-ai-dataset-provenance|AML.M0025: Maintain AI Dataset Provenance]] — Maintaining a detailed history of datasets can help identify use of poisoned datasets from public sources.

## Case Studies (1)

- [[frameworks/atlas/case-studies/web-scale-data-poisoning-split-view-attack|Web-Scale Data Poisoning: Split-View Attack]]
