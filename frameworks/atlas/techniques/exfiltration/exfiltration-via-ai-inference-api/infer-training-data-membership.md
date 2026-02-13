---
id: exfiltration-via-ai-inference-api-infer-training-data-membership
title: "AML.T0024.000: Infer Training Data Membership"
sidebar_label: "Infer Training Data Membership"
sidebar_position: 2
---

# AML.T0024.000: Infer Training Data Membership

> **Sub-Technique of:** [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-ai-inference-api|AML.T0024: Exfiltration via AI Inference API]]

Adversaries may infer the membership of a data sample or global characteristics of the data in its training set, which raises privacy concerns.
Some strategies make use of a shadow model that could be obtained via [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/train-proxy-via-replication|Train Proxy via Replication]], others use statistics of model prediction scores.

This can cause the victim model to leak private information, such as PII of those in the training set or other forms of protected IP.

## Metadata

- **Technique ID:** AML.T0024.000
- **Created:** May 13, 2021
- **Last Modified:** November 6, 2025
- **Maturity:** feasible

- **Parent Technique:** [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-ai-inference-api|AML.T0024: Exfiltration via AI Inference API]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (0)

*No case studies currently documented for this technique.*

## References

MITRE Corporation. *Infer Training Data Membership (AML.T0024.000)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0024.000
