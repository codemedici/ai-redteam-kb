---
id: backdoor-attack-on-deep-learning-models-in-mobile-apps
title: "AML.CS0013: Backdoor Attack on Deep Learning Models in Mobile Apps"
type: case-study
sidebar_position: 14
---

# AML.CS0013: Backdoor Attack on Deep Learning Models in Mobile Apps

Deep learning models are increasingly used in mobile applications as critical components.
Researchers from Microsoft Research demonstrated that many deep learning models deployed in mobile apps are vulnerable to backdoor attacks via "neural payload injection."
They conducted an empirical study on real-world mobile deep learning apps collected from Google Play. They identified 54 apps that were vulnerable to attack, including popular security and safety critical applications used for cash recognition, parental control, face authentication, and financial services.

## Metadata

- **ID:** AML.CS0013
- **Incident Date:** 2021-01-18
- **Type:** exercise
- **Target:** ML-based Android Apps
- **Actor:** Yuanchun Li, Jiayi Hua, Haoyu Wang, Chunyang Chen, Yunxin Liu

## Procedure

### Step 1: Search Application Repositories

**Technique:** [[frameworks/atlas/techniques/reconnaissance/search-application-repositories|AML.T0004: Search Application Repositories]]

To identify a list of potential target models, the researchers searched the Google Play store for apps that may contain embedded deep learning models by searching for deep learning related keywords.

### Step 2: Models

**Technique:** [[frameworks/atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-AI-artifacts-models|AML.T0002.001: Models]]

The researchers acquired the apps' APKs from the Google Play store.
They filtered the list of potential target applications by searching the code metadata for keywords related to TensorFlow or TFLite and their model binary formats (.tf and .tflite).
The models were extracted from the APKs using Apktool.

### Step 3: Full AI Model Access

**Technique:** [[frameworks/atlas/techniques/ai-model-access/full-ai-model-access|AML.T0044: Full AI Model Access]]

This provided the researchers with full access to the ML model, albeit in compiled, binary form.

### Step 4: Adversarial AI Attacks

**Technique:** [[frameworks/atlas/techniques/resource-development/develop-capabilities/develop-capabilities-adversarial-AI-attacks|AML.T0017.000: Adversarial AI Attacks]]

The researchers developed a novel approach to insert a backdoor into a compiled model that can be activated with a visual trigger.  They inject a "neural payload" into the model that consists of a trigger detection network and conditional logic.
The trigger detector is trained to detect a visual trigger that will be placed in the real world.
The conditional logic allows the researchers to bypass the victim model when the trigger is detected and provide model outputs of their choosing.
The only requirements for training a trigger detector are a general
dataset from the same modality as the target model (e.g. ImageNet for image classification) and several photos of the desired trigger.

### Step 5: Modify AI Model Architecture

**Technique:** [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-modify-architecture|AML.T0018.001: Modify AI Model Architecture]]

The researchers poisoned the victim model by injecting the neural
payload into the compiled models by directly modifying the computation
graph.
The researchers then repackage the poisoned model back into the APK

### Step 6: Verify Attack

**Technique:** [[frameworks/atlas/techniques/ai-attack-staging/verify-attack|AML.T0042: Verify Attack]]

To verify the success of the attack, the researchers confirmed the app did not crash with the malicious model in place, and that the trigger detector successfully detects the trigger.

### Step 7: Model

**Technique:** [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-hardware|AML.T0010.003: Model]]

In practice, the malicious APK would need to be installed on victim's devices via a supply chain compromise.

### Step 8: Insert Backdoor Trigger

**Technique:** [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-insert-backdoor-trigger|AML.T0043.004: Insert Backdoor Trigger]]

The trigger is placed in the physical environment, where it is captured by the victim's device camera and processed by the backdoored ML model.

### Step 9: Physical Environment Access

**Technique:** [[frameworks/atlas/techniques/ai-model-access/physical-environment-access|AML.T0041: Physical Environment Access]]

At inference time, only physical environment access is required to trigger the attack.

### Step 10: Evade AI Model

**Technique:** [[frameworks/atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

Presenting the visual trigger causes the victim model to be bypassed.
The researchers demonstrated this can be used to evade ML models in
several safety-critical apps in the Google Play store.

## References

1. [DeepPayload: Black-box Backdoor Attack on Deep Learning Models through Neural Payload Injection](https://arxiv.org/abs/2101.06896)
