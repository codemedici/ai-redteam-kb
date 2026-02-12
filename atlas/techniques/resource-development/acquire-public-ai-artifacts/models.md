---
id: acquire-public-ai-artifacts-models
title: "AML.T0002.001: Models"
sidebar_label: "Models"
sidebar_position: 3
---

# AML.T0002.001: Models

> **Sub-Technique of:** [[atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-ai-artifacts-overview|AML.T0002: Acquire Public AI Artifacts]]



Adversaries may acquire public models to use in their operations.
Adversaries may seek models used by the victim organization or models that are representative of those used by the victim organization.
Representative models may include model architectures, or pre-trained models which define the architecture as well as model parameters from training on a dataset.
The adversary may search public sources for common model architecture configuration file formats such as YAML or Python configuration files, and common model storage file formats such as ONNX (.onnx), HDF5 (.h5), Pickle (.pkl), PyTorch (.pth), or TensorFlow (.pb, .tflite).

Acquired models are useful in advancing the adversary's operations and are frequently used to tailor attacks to the victim model.

## Metadata

- **Technique ID:** AML.T0002.001
- **Created:** May 13, 2021
- **Last Modified:** February 28, 2023
- **Maturity:** demonstrated

- **Parent Technique:** [[atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-ai-artifacts-overview|AML.T0002: Acquire Public AI Artifacts]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*



## Case Studies (4)


The following case studies demonstrate this technique:

### [[atlas/case-studies/attack-on-machine-translation-services|AML.CS0005: Attack on Machine Translation Services]]

The researchers gathered similar model architectures that the target translation services used.

### [[atlas/case-studies/gpt-2-model-replication|AML.CS0007: GPT-2 Model Replication]]

The researchers obtained a reference implementation of a similar publicly available model called Grover.

### [[atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|AML.CS0013: Backdoor Attack on Deep Learning Models in Mobile Apps]]

The researchers acquired the apps' APKs from the Google Play store.
They filtered the list of potential target applications by searching the code metadata for keywords related to TensorFlow or TFLite and their model binary formats (.tf and .tflite).
The models were extracted from the APKs using Apktool.

### [[atlas/case-studies/poisongpt|AML.CS0019: PoisonGPT]]

Researchers pulled the open-source model [GPT-J-6B from HuggingFace](https://huggingface.co/EleutherAI/gpt-j-6b).  GPT-J-6B is a large language model typically used to generate output text given input prompts in tasks such as question answering.



## References

MITRE Corporation. *Models (AML.T0002.001)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0002.001
