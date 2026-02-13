---
id: acquire-public-AI-artifacts-models
title: "AML.T0002.001: Models"
sidebar_label: "Models"
sidebar_position: 2002
---

# AML.T0002.001: Models

Adversaries may acquire public models to use in their operations.
Adversaries may seek models used by the victim organization or models that are representative of those used by the victim organization.
Representative models may include model architectures, or pre-trained models which define the architecture as well as model parameters from training on a dataset.
The adversary may search public sources for common model architecture configuration file formats such as YAML or Python configuration files, and common model storage file formats such as ONNX (.onnx), HDF5 (.h5), Pickle (.pkl), PyTorch (.pth), or TensorFlow (.pb, .tflite).

Acquired models are useful in advancing the adversary's operations and are frequently used to tailor attacks to the victim model.

## Metadata

- **Technique ID:** AML.T0002.001
- **Created:** 2021-05-13
- **Last Modified:** 2023-02-28
- **Maturity:** demonstrated

## Parent Technique

**Parent Technique:** AML.T0002 — Acquire Public AI Artifacts

## Tactics (1)

- [[frameworks/atlas/tactics/resource-development|Resource Development]]

## Case Studies (4)

- [[frameworks/atlas/case-studies/attack-on-machine-translation-services|Attack on Machine Translation Services]]
- [[frameworks/atlas/case-studies/gpt-2-model-replication|GPT-2 Model Replication]]
- [[frameworks/atlas/case-studies/backdoor-attack-on-deep-learning-models-in-mobile-apps|Backdoor Attack on Deep Learning Models in Mobile Apps]]
- [[frameworks/atlas/case-studies/poisongpt|PoisonGPT]]
