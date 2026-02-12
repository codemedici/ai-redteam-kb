---
id: gpt-2-model-replication
title: "AML.CS0007: GPT-2 Model Replication"
sidebar_label: "GPT-2 Model Replication"
sidebar_position: 8
---

# AML.CS0007: GPT-2 Model Replication

## Summary

OpenAI built GPT-2, a language model capable of generating high quality text samples. Over concerns that GPT-2 could be used for malicious purposes such as impersonating others, or generating misleading news articles, fake social media content, or spam, OpenAI adopted a tiered release schedule. They initially released a smaller, less powerful version of GPT-2 along with a technical description of the approach, but held back the full trained model.

Before the full model was released by OpenAI, researchers at Brown University successfully replicated the model using information released by OpenAI and open source ML artifacts. This demonstrates that a bad actor with sufficient technical skill and compute resources could have replicated GPT-2 and used it for harmful goals before the AI Security community is prepared.

## Metadata

- **Case Study ID:** AML.CS0007
- **Incident Date:** 2019
- **Type:** exercise
- **Target:** OpenAI GPT-2
- **Actor:** Researchers at Brown University

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Search Open Technical Databases

**Tactic:** [[atlas/tactics/reconnaissance|AML.TA0002: Reconnaissance]]
**Technique:** [[atlas/techniques/reconnaissance/search-open-technical-databases/search-open-technical-databases-overview|AML.T0000: Search Open Technical Databases]]

Using the public documentation about GPT-2, the researchers gathered information about the dataset, model architecture, and training hyper-parameters.

### Step 2: Models

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/acquire-public-ai-artifacts/models|AML.T0002.001: Models]]

The researchers obtained a reference implementation of a similar publicly available model called Grover.

### Step 3: Datasets

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/acquire-public-ai-artifacts/datasets|AML.T0002.000: Datasets]]

The researchers were able to manually recreate the dataset used in the original GPT-2 paper using the gathered documentation.

### Step 4: AI Development Workspaces

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/acquire-infrastructure/ai-development-workspaces|AML.T0008.000: AI Development Workspaces]]

The researchers were able to use TensorFlow Research Cloud via their academic credentials.

### Step 5: Train Proxy via Gathered AI Artifacts

**Tactic:** [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]
**Technique:** [[atlas/techniques/ai-attack-staging/create-proxy-ai-model/train-proxy-via-gathered-ai-artifacts|AML.T0005.000: Train Proxy via Gathered AI Artifacts]]

The researchers modified Grover's objective function to reflect GPT-2's objective function and then trained on the dataset they curated using used Grover's initial hyperparameters. The resulting model functionally replicates GPT-2, obtaining similar performance on most datasets.
A bad actor who followed the same procedure as the researchers could then use the replicated GPT-2 model for malicious purposes.


## Tactics and Techniques Used


**Step 1:**
- Tactic: [[atlas/tactics/reconnaissance|AML.TA0002: Reconnaissance]]
- Technique: [[atlas/techniques/reconnaissance/search-open-technical-databases/search-open-technical-databases-overview|AML.T0000: Search Open Technical Databases]]

**Step 2:**
- Tactic: [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
- Technique: [[atlas/techniques/resource-development/acquire-public-ai-artifacts/models|AML.T0002.001: Models]]

**Step 3:**
- Tactic: [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
- Technique: [[atlas/techniques/resource-development/acquire-public-ai-artifacts/datasets|AML.T0002.000: Datasets]]

**Step 4:**
- Tactic: [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
- Technique: [[atlas/techniques/resource-development/acquire-infrastructure/ai-development-workspaces|AML.T0008.000: AI Development Workspaces]]

**Step 5:**
- Tactic: [[atlas/tactics/ai-attack-staging|AML.TA0001: AI Attack Staging]]
- Technique: [[atlas/techniques/ai-attack-staging/create-proxy-ai-model/train-proxy-via-gathered-ai-artifacts|AML.T0005.000: Train Proxy via Gathered AI Artifacts]]



## External References

- Wired Article, "OpenAI Said Its Code Was Risky. Two Grads Re-Created It Anyway" Available at: https://www.wired.com/story/dangerous-ai-open-source/
- Medium BlogPost, "OpenGPT-2: We Replicated GPT-2 Because You Can Too" Available at: https://blog.usejournal.com/opengpt-2-we-replicated-gpt-2-because-you-can-too-45e34e6d36dc


## References

MITRE Corporation. *GPT-2 Model Replication (AML.CS0007)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0007
