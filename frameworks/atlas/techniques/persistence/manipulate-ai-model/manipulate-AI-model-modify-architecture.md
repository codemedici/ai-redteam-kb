---
id: manipulate-ai-model-modify-ai-model-architecture
title: "AML.T0018.001: Modify AI Model Architecture"
sidebar_label: "Modify AI Model Architecture"
sidebar_position: 3
---

# AML.T0018.001: Modify AI Model Architecture

> **Sub-Technique of:** [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-ai-model|AML.T0018: Manipulate AI Model]]

Adversaries may directly modify an AI model's architecture to re-define it's behavior. This can include adding or removing layers as well as adding pre or post-processing operations.

The effects could include removing the ability to predict certain classes, adding erroneous operations to increase computation costs, or degrading performance. Additionally, a separate adversary-defined network could be injected into the computation graph, which can change the behavior based on the inputs, effectively creating a backdoor.

## Metadata

- **Technique ID:** AML.T0018.001
- **Created:** May 13, 2021
- **Last Modified:** April 11, 2024
- **Maturity:** demonstrated

- **Parent Technique:** [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-ai-model|AML.T0018: Manipulate AI Model]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (2)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|AML.CS0013: Backdoor Attack on Deep Learning Models in Mobile Apps]]

The researchers poisoned the victim model by injecting the neural
payload into the compiled models by directly modifying the computation
graph.
The researchers then repackage the poisoned model back into the APK

### [[frameworks/atlas/case-studies/ai-model-tampering-via-supply-chain-attack|AML.CS0028: AI Model Tampering via Supply Chain Attack]]

With full access to the model, an adversary could modify the architecture to change the behavior.

## References

MITRE Corporation. *Modify AI Model Architecture (AML.T0018.001)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0018.001
