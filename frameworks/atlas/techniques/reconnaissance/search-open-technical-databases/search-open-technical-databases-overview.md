---
id: search-open-technical-databases
title: "AML.T0000: Search Open Technical Databases"
sidebar_label: "Search Open Technical Databases"
sidebar_position: 1
---

# AML.T0000: Search Open Technical Databases



Adversaries may search for publicly available research and technical documentation to learn how and where AI is used within a victim organization.
The adversary can use this information to identify targets for attack, or to tailor an existing attack to make it more effective.
Organizations often use open source model architectures trained on additional proprietary data in production.
Knowledge of this underlying architecture allows the adversary to craft more realistic proxy models ([[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-ai-model-overview|Create Proxy AI Model]]).
An adversary can search these resources for publications for authors employed at the victim organization.

Research and technical materials may exist as academic papers published in [[frameworks/atlas/techniques/reconnaissance/search-open-technical-databases/journals-and-conference-proceedings|Journals and Conference Proceedings]], or stored in [[frameworks/atlas/techniques/reconnaissance/search-open-technical-databases/pre-print-repositories|Pre-Print Repositories]], as well as [[frameworks/atlas/techniques/reconnaissance/search-open-technical-databases/technical-blogs|Technical Blogs]].

## Metadata

- **Technique ID:** AML.T0000
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1596](https://attack.mitre.org/techniques/T1596/)


## Tactics (1)

This technique supports the following tactics:


- [[frameworks/atlas/tactics/reconnaissance|AML.TA0002: Reconnaissance]]



## Sub-Techniques (3)

- [[frameworks/atlas/techniques/reconnaissance/search-open-technical-databases/journals-and-conference-proceedings|AML.T0000.000: Journals and Conference Proceedings]]
- [[frameworks/atlas/techniques/reconnaissance/search-open-technical-databases/pre-print-repositories|AML.T0000.001: Pre-Print Repositories]]
- [[frameworks/atlas/techniques/reconnaissance/search-open-technical-databases/technical-blogs|AML.T0000.002: Technical Blogs]]


## Case Studies (7)


The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/botnet-domain-generation-algorithm-dga-detection-evasion|AML.CS0001: Botnet Domain Generation Algorithm (DGA) Detection Evasion]]

DGA detection is a widely used technique to detect botnets in academia and industry.
The research team searched for research papers related to DGA detection.

### [[frameworks/atlas/case-studies/bypassing-cylance-s-ai-malware-detection|AML.CS0003: Bypassing Cylance's AI Malware Detection]]

The researchers read publicly available information about Cylance's AI Malware detector. They gathered this information from various sources such as public talks as well as patent submissions by Cylance.

### [[frameworks/atlas/case-studies/attack-on-machine-translation-services|AML.CS0005: Attack on Machine Translation Services]]

The researchers used published research papers to identify the datasets and model architectures used by the target translation services.

### [[frameworks/atlas/case-studies/gpt-2-model-replication|AML.CS0007: GPT-2 Model Replication]]

Using the public documentation about GPT-2, the researchers gathered information about the dataset, model architecture, and training hyper-parameters.

### [[frameworks/atlas/case-studies/microsoft-azure-service-disruption|AML.CS0010: Microsoft Azure Service Disruption]]

The team first performed reconnaissance to gather information about the target ML model.

### [[frameworks/atlas/case-studies/microsoft-edge-ai-evasion|AML.CS0011: Microsoft Edge AI Evasion]]

The team first performed reconnaissance to gather information about the target ML model.

### [[frameworks/atlas/case-studies/face-identification-system-evasion-via-physical-countermeasures|AML.CS0012: Face Identification System Evasion via Physical Countermeasures]]

The team first performed reconnaissance to gather information about the target ML model.



## References

MITRE Corporation. *Search Open Technical Databases (AML.T0000)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0000
