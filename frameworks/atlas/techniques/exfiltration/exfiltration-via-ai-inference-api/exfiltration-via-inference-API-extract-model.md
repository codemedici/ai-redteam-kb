---
id: exfiltration-via-inference-API-extract-model
title: "AML.T0024.000: Infer Training Data Membership"
sidebar_label: "Infer Training Data Membership"
sidebar_position: 24001
---

# AML.T0024.000: Infer Training Data Membership

Adversaries may infer the membership of a data sample or global characteristics of the data in its training set, which raises privacy concerns.
Some strategies make use of a shadow model that could be obtained via [Train Proxy via Replication](/techniques/AML.T0005.001), others use statistics of model prediction scores.

This can cause the victim model to leak private information, such as PII of those in the training set or other forms of protected IP.

## Metadata

- **Technique ID:** AML.T0024.000
- **Created:** 2021-05-13
- **Last Modified:** 2025-11-06
- **Maturity:** feasible

## Parent Technique

**Parent Technique:** AML.T0024 — Exfiltration via AI Inference API

## Tactics (1)

- [[frameworks/atlas/tactics/exfiltration|Exfiltration]]
