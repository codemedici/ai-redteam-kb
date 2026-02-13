---
id: exfiltration-via-ai-inference-api
title: "AML.T0024: Exfiltration via AI Inference API"
sidebar_label: "Exfiltration via AI Inference API"
sidebar_position: 1
---

# AML.T0024: Exfiltration via AI Inference API



Adversaries may exfiltrate private information via [[frameworks/atlas/techniques/ai-model-access/ai-model-inference-api-access|AI Model Inference API Access]].
AI Models have been shown leak private information about their training data (e.g.  [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-membership-inference|Infer Training Data Membership]], [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-invert-model|Invert AI Model]]).
The model itself may also be extracted ([[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-extract-model|Extract AI Model]]) for the purposes of [[frameworks/atlas/techniques/impact/external-harms/external-harms-AI-IP-theft|AI Intellectual Property Theft]].

Exfiltration of information relating to private training data raises privacy concerns.
Private training data may include personally identifiable information, or other protected data.

## Metadata

- **Technique ID:** AML.T0024
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** feasible



## Tactics (1)

This technique supports the following tactics:


- [[frameworks/atlas/tactics/exfiltration|AML.TA0010: Exfiltration]]



## Sub-Techniques (3)

- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-membership-inference|AML.T0024.000: Infer Training Data Membership]]
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-invert-model|AML.T0024.001: Invert AI Model]]
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-extract-model|AML.T0024.002: Extract AI Model]]


## Case Studies (0)

*No case studies currently documented for this technique.*

## References

MITRE Corporation. *Exfiltration via AI Inference API (AML.T0024)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0024
