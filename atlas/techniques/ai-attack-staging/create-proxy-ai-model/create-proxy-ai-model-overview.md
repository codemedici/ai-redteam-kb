---
id: create-proxy-ai-model
title: "AML.T0005: Create Proxy AI Model"
sidebar_label: "Create Proxy AI Model"
sidebar_position: 1
---

# AML.T0005: Create Proxy AI Model



Adversaries may obtain models to serve as proxies for the target model in use at the victim organization.
Proxy models are used to simulate complete access to the target model in a fully offline manner.

Adversaries may train models from representative datasets, attempt to replicate models from victim inference APIs, or use available pre-trained models.

## Metadata

- **Technique ID:** AML.T0005
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** demonstrated



## Tactics (1)

This technique supports the following tactics:


- [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]



## Sub-Techniques (3)

- [[atlas/techniques/ai-attack-staging/create-proxy-ai-model/train-proxy-via-gathered-ai-artifacts|AML.T0005.000: Train Proxy via Gathered AI Artifacts]]
- [[atlas/techniques/ai-attack-staging/create-proxy-ai-model/train-proxy-via-replication|AML.T0005.001: Train Proxy via Replication]]
- [[atlas/techniques/ai-attack-staging/create-proxy-ai-model/use-pre-trained-model|AML.T0005.002: Use Pre-Trained Model]]


## Case Studies (3)


The following case studies demonstrate this technique:

### [[atlas/case-studies/evasion-of-deep-learning-detector-for-malware-c-c-traffic|AML.CS0000: Evasion of Deep Learning Detector for Malware C&C Traffic]]

We trained a model on the HTTP traffic dataset to use as a proxy for the target model.
Evaluation showed a true positive rate of ~ 99% and false positive rate of ~ 0.01%, on average.
Testing the model with a HTTP packet header from known malware command and control traffic samples was detected as malicious with high confidence (> 99%).

### [[atlas/case-studies/face-identification-system-evasion-via-physical-countermeasures|AML.CS0012: Face Identification System Evasion via Physical Countermeasures]]

The team developed a proxy model using the open source data.

### [[atlas/case-studies/confusing-antimalware-neural-networks|AML.CS0014: Confusing Antimalware Neural Networks]]

A proxy model was trained on the labeled dataset of malware and clean files.
The researchers experimented with a variety of model architectures.



## References

MITRE Corporation. *Create Proxy AI Model (AML.T0005)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0005
