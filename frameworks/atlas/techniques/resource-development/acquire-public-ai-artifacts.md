---
id: acquire-public-ai-artifacts
title: "AML.T0002: Acquire Public AI Artifacts"
sidebar_label: "Acquire Public AI Artifacts"
sidebar_position: 1
---

# AML.T0002: Acquire Public AI Artifacts



Adversaries may search public sources, including cloud storage, public-facing services, and software or data repositories, to identify AI artifacts.
These AI artifacts may include the software stack used to train and deploy models, training and testing data, model configurations and parameters.
An adversary will be particularly interested in artifacts hosted by or associated with the victim organization as they may represent what that organization uses in a production environment.
Adversaries may identify artifact repositories via other resources associated with the victim organization (e.g. [[frameworks/atlas/techniques/reconnaissance/search-victim-owned-websites|Search Victim-Owned Websites]] or [[frameworks/atlas/techniques/reconnaissance/search-open-technical-databases|Search Open Technical Databases]]).
These AI artifacts often provide adversaries with details of the AI task and approach.

AI artifacts can aid in an adversary's ability to [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model|Create Proxy AI Model]].
If these artifacts include pieces of the actual model in production, they can be used to directly [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data|Craft Adversarial Data]].
Acquiring some artifacts requires registration (providing user details such email/name), AWS keys, or written requests, and may require the adversary to [[frameworks/atlas/techniques/resource-development/establish-accounts|Establish Accounts]].

Artifacts might be hosted on victim-controlled infrastructure, providing the victim with some information on who has accessed that data.

## Metadata

- **Technique ID:** AML.T0002
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** realized



## Tactics (1)

This technique supports the following tactics:


- [[frameworks/atlas/tactics/resource-development|AML.TA0003: Resource Development]]



## Sub-Techniques (2)

- [[frameworks/atlas/techniques/resource-development/acquire-public-AI-artifacts-datasets|AML.T0002.000: Datasets]]
- [[frameworks/atlas/techniques/resource-development/acquire-public-AI-artifacts-models|AML.T0002.001: Models]]


## Case Studies (3)


The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/botnet-domain-generation-algorithm-dga-detection-evasion|AML.CS0001: Botnet Domain Generation Algorithm (DGA) Detection Evasion]]

The researchers acquired a publicly available CNN-based DGA detection model and tested it against a well-known DGA generated domain name data sets, which includes ~50 million domain names from 64 botnet DGA families.
The CNN-based DGA detection model shows more than 70% detection accuracy on 16 (~25%) botnet DGA families.

### [[frameworks/atlas/case-studies/clearviewai-misconfiguration|AML.CS0006: ClearviewAI Misconfiguration]]

Adversaries could have downloaded training data and gleaned details about software, models, and capabilities from the source code and decompiled application binaries.

### [[frameworks/atlas/case-studies/microsoft-edge-ai-evasion|AML.CS0011: Microsoft Edge AI Evasion]]

The team identified and obtained the publicly available base model to use against the target ML model.



## References

MITRE Corporation. *Acquire Public AI Artifacts (AML.T0002)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0002
