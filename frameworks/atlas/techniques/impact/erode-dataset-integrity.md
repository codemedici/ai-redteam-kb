---
id: erode-dataset-integrity
title: "AML.T0059: Erode Dataset Integrity"
sidebar_label: "Erode Dataset Integrity"
sidebar_position: 60
---

# AML.T0059: Erode Dataset Integrity

Adversaries may poison or manipulate portions of a dataset to reduce its usefulness, reduce trust, and cause users to waste resources correcting errors.

## Metadata

- **Technique ID:** AML.T0059
- **Created:** 2025-03-12
- **Last Modified:** 2025-03-12
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/impact|Impact]]

## Mitigations (2)

- [[frameworks/atlas/mitigations/sanitize-training-data|AML.M0007: Sanitize Training Data]] — Remediating poisoned data can re-establish dataset integrity.
- [[frameworks/atlas/mitigations/maintain-ai-dataset-provenance|AML.M0025: Maintain AI Dataset Provenance]] — Maintaining dataset provenance can help identify adverse changes to the data.

## Case Studies (1)

- [[frameworks/atlas/case-studies/web-scale-data-poisoning-split-view-attack|Web-Scale Data Poisoning: Split-View Attack]]
