---
id: create-proxy-ai-model-train-proxy-via-replication
title: "AML.T0005.001: Train Proxy via Replication"
sidebar_label: "Train Proxy via Replication"
sidebar_position: 3
---

# AML.T0005.001: Train Proxy via Replication

> **Sub-Technique of:** [[atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-ai-model-overview|AML.T0005: Create Proxy AI Model]]



Adversaries may replicate a private model.
By repeatedly querying the victim's [[atlas/techniques/ai-model-access/ai-model-inference-api-access|AI Model Inference API Access]], the adversary can collect the target model's inferences into a dataset.
The inferences are used as labels for training a separate model offline that will mimic the behavior and performance of the target model.

A replicated model that closely mimic's the target model is a valuable resource in staging the attack.
The adversary can use the replicated model to [[atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-overview|Craft Adversarial Data]] for various purposes (e.g. [[atlas/techniques/initial-access/evade-ai-model|Evade AI Model]], [[atlas/techniques/impact/spamming-ai-system-with-chaff-data|Spamming AI System with Chaff Data]]).

## Metadata

- **Technique ID:** AML.T0005.001
- **Created:** May 13, 2021
- **Last Modified:** May 13, 2021
- **Maturity:** demonstrated

- **Parent Technique:** [[atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-ai-model-overview|AML.T0005: Create Proxy AI Model]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*



## Case Studies (2)


The following case studies demonstrate this technique:

### [[atlas/case-studies/attack-on-machine-translation-services|AML.CS0005: Attack on Machine Translation Services]]

Using these translated sentence pairs, the researchers trained a model that replicates the behavior of the target model.

### [[atlas/case-studies/proofpoint-evasion|AML.CS0008: ProofPoint Evasion]]

The researchers used the emails and collected scores as a dataset, which they used to train a functional copy of the ProofPoint model. 

Basic correlation was used to decide which score variable speaks generally about the security of an email. The "mlxlogscore" was selected in this case due to its relationship with spam, phish, and core mlx and was used as the label. Each "mlxlogscore" was generally between 1 and 999 (higher score = safer sample). Training was performed using an Artificial Neural Network (ANN) and Bag of Words tokenizing.



## References

MITRE Corporation. *Train Proxy via Replication (AML.T0005.001)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0005.001
