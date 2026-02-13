---
id: manipulate-ai-model
title: "AML.T0018: Manipulate AI Model"
sidebar_label: "Manipulate AI Model"
sidebar_position: 1
---

# AML.T0018: Manipulate AI Model



Adversaries may directly manipulate an AI model to change its behavior or introduce malicious code. Manipulating a model gives the adversary a persistent change in the system. This can include poisoning the model by changing its weights, modifying the model architecture to change its behavior, and embedding malware which may be executed when the model is loaded.

## Metadata

- **Technique ID:** AML.T0018
- **Created:** May 13, 2021
- **Last Modified:** April 14, 2025
- **Maturity:** realized



## Tactics (2)

This technique supports the following tactics:


- [[frameworks/atlas/tactics/persistence|AML.TA0006: Persistence]]
- [[frameworks/atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]



## Sub-Techniques (3)

- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-poison|AML.T0018.000: Poison AI Model]]
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-modify-architecture|AML.T0018.001: Modify AI Model Architecture]]
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-embed-malware|AML.T0018.002: Embed Malware]]


## Case Studies (0)

*No case studies currently documented for this technique.*

## References

MITRE Corporation. *Manipulate AI Model (AML.T0018)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0018
