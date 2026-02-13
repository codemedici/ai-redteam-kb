---
id: gpt-2-model-replication
title: "AML.CS0007: GPT-2 Model Replication"
type: case-study
sidebar_position: 8
---

# AML.CS0007: GPT-2 Model Replication

OpenAI built GPT-2, a language model capable of generating high quality text samples. Over concerns that GPT-2 could be used for malicious purposes such as impersonating others, or generating misleading news articles, fake social media content, or spam, OpenAI adopted a tiered release schedule. They initially released a smaller, less powerful version of GPT-2 along with a technical description of the approach, but held back the full trained model.

Before the full model was released by OpenAI, researchers at Brown University successfully replicated the model using information released by OpenAI and open source ML artifacts. This demonstrates that a bad actor with sufficient technical skill and compute resources could have replicated GPT-2 and used it for harmful goals before the AI Security community is prepared.

## Metadata

- **ID:** AML.CS0007
- **Incident Date:** 2019-08-22
- **Type:** exercise
- **Target:** OpenAI GPT-2
- **Actor:** Researchers at Brown University

## Procedure

### Step 1: Search Open Technical Databases

**Technique:** [[frameworks/atlas/techniques/reconnaissance/search-open-technical-databases|AML.T0000: Search Open Technical Databases]]

Using the public documentation about GPT-2, the researchers gathered information about the dataset, model architecture, and training hyper-parameters.

### Step 2: Models

**Technique:** [[frameworks/atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-AI-artifacts-models|AML.T0002.001: Models]]

The researchers obtained a reference implementation of a similar publicly available model called Grover.

### Step 3: Datasets

**Technique:** [[frameworks/atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-AI-artifacts-datasets|AML.T0002.000: Datasets]]

The researchers were able to manually recreate the dataset used in the original GPT-2 paper using the gathered documentation.

### Step 4: AI Development Workspaces

**Technique:** [[frameworks/atlas/techniques/resource-development/acquire-infrastructure/acquire-infrastructure-domains|AML.T0008.000: AI Development Workspaces]]

The researchers were able to use TensorFlow Research Cloud via their academic credentials.

### Step 5: Train Proxy via Gathered AI Artifacts

**Technique:** [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-via-gathered-artifacts|AML.T0005.000: Train Proxy via Gathered AI Artifacts]]

The researchers modified Grover's objective function to reflect GPT-2's objective function and then trained on the dataset they curated using used Grover's initial hyperparameters. The resulting model functionally replicates GPT-2, obtaining similar performance on most datasets.
A bad actor who followed the same procedure as the researchers could then use the replicated GPT-2 model for malicious purposes.

## References

1. [Wired Article, "OpenAI Said Its Code Was Risky. Two Grads Re-Created It Anyway"](https://www.wired.com/story/dangerous-ai-open-source/)
2. [Medium BlogPost, "OpenGPT-2: We Replicated GPT-2 Because You Can Too"](https://blog.usejournal.com/opengpt-2-we-replicated-gpt-2-because-you-can-too-45e34e6d36dc)
