---
id: backdoor-attack-on-deep-learning-models-in-mobile-apps
title: "AML.CS0013: Backdoor Attack on Deep Learning Models in Mobile Apps"
sidebar_label: "Backdoor Attack on Deep Learning Models in Mobile Apps"
sidebar_position: 14
---

# AML.CS0013: Backdoor Attack on Deep Learning Models in Mobile Apps

## Summary

Deep learning models are increasingly used in mobile applications as critical components.
Researchers from Microsoft Research demonstrated that many deep learning models deployed in mobile apps are vulnerable to backdoor attacks via "neural payload injection."
They conducted an empirical study on real-world mobile deep learning apps collected from Google Play. They identified 54 apps that were vulnerable to attack, including popular security and safety critical applications used for cash recognition, parental control, face authentication, and financial services.

## Metadata

- **Case Study ID:** AML.CS0013
- **Incident Date:** 2021
- **Type:** exercise
- **Target:** ML-based Android Apps
- **Actor:** Yuanchun Li, Jiayi Hua, Haoyu Wang, Chunyang Chen, Yunxin Liu

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Search Application Repositories

**Technique:** [[atlas/techniques/reconnaissance/search-application-repositories|AML.T0004: Search Application Repositories]]

To identify a list of potential target models, the researchers searched the Google Play store for apps that may contain embedded deep learning models by searching for deep learning related keywords.

### Step 2: Models

**Technique:** [[atlas/techniques/resource-development/acquire-public-ai-artifacts/models|AML.T0002.001: Models]]

The researchers acquired the apps' APKs from the Google Play store.
They filtered the list of potential target applications by searching the code metadata for keywords related to TensorFlow or TFLite and their model binary formats (.tf and .tflite).
The models were extracted from the APKs using Apktool.

### Step 3: Full AI Model Access

**Technique:** [[atlas/techniques/ai-model-access/full-ai-model-access|AML.T0044: Full AI Model Access]]

This provided the researchers with full access to the ML model, albeit in compiled, binary form.

### Step 4: Adversarial AI Attacks

**Technique:** [[atlas/techniques/resource-development/develop-capabilities/adversarial-ai-attacks|AML.T0017.000: Adversarial AI Attacks]]

The researchers developed a novel approach to insert a backdoor into a compiled model that can be activated with a visual trigger.  They inject a "neural payload" into the model that consists of a trigger detection network and conditional logic.
The trigger detector is trained to detect a visual trigger that will be placed in the real world.
The conditional logic allows the researchers to bypass the victim model when the trigger is detected and provide model outputs of their choosing.
The only requirements for training a trigger detector are a general
dataset from the same modality as the target model (e.g. ImageNet for image classification) and several photos of the desired trigger.

### Step 5: Modify AI Model Architecture

**Technique:** [[atlas/techniques/persistence/manipulate-ai-model/modify-ai-model-architecture|AML.T0018.001: Modify AI Model Architecture]]

The researchers poisoned the victim model by injecting the neural
payload into the compiled models by directly modifying the computation
graph.
The researchers then repackage the poisoned model back into the APK

### Step 6: Verify Attack

**Technique:** [[atlas/techniques/ai-attack-staging/verify-attack|AML.T0042: Verify Attack]]

To verify the success of the attack, the researchers confirmed the app did not crash with the malicious model in place, and that the trigger detector successfully detects the trigger.

### Step 7: Model

**Technique:** [[atlas/techniques/initial-access/ai-supply-chain-compromise/model|AML.T0010.003: Model]]

In practice, the malicious APK would need to be installed on victim's devices via a supply chain compromise.

### Step 8: Insert Backdoor Trigger

**Technique:** [[atlas/techniques/ai-attack-staging/craft-adversarial-data/insert-backdoor-trigger|AML.T0043.004: Insert Backdoor Trigger]]

The trigger is placed in the physical environment, where it is captured by the victim's device camera and processed by the backdoored ML model.

### Step 9: Physical Environment Access

**Technique:** [[atlas/techniques/ai-model-access/physical-environment-access|AML.T0041: Physical Environment Access]]

At inference time, only physical environment access is required to trigger the attack.

### Step 10: Evade AI Model

**Technique:** [[atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

Presenting the visual trigger causes the victim model to be bypassed.
The researchers demonstrated this can be used to evade ML models in
several safety-critical apps in the Google Play store.

## Tactics and Techniques Used

**Step 1:**
- Technique: [[atlas/techniques/reconnaissance/search-application-repositories|AML.T0004: Search Application Repositories]]

**Step 2:**
- Technique: [[atlas/techniques/resource-development/acquire-public-ai-artifacts/models|AML.T0002.001: Models]]

**Step 3:**
- Technique: [[atlas/techniques/ai-model-access/full-ai-model-access|AML.T0044: Full AI Model Access]]

**Step 4:**
- Technique: [[atlas/techniques/resource-development/develop-capabilities/adversarial-ai-attacks|AML.T0017.000: Adversarial AI Attacks]]

**Step 5:**
- Technique: [[atlas/techniques/persistence/manipulate-ai-model/modify-ai-model-architecture|AML.T0018.001: Modify AI Model Architecture]]

**Step 6:**
- Technique: [[atlas/techniques/ai-attack-staging/verify-attack|AML.T0042: Verify Attack]]

**Step 7:**
- Technique: [[atlas/techniques/initial-access/ai-supply-chain-compromise/model|AML.T0010.003: Model]]

**Step 8:**
- Technique: [[atlas/techniques/ai-attack-staging/craft-adversarial-data/insert-backdoor-trigger|AML.T0043.004: Insert Backdoor Trigger]]

**Step 9:**
- Technique: [[atlas/techniques/ai-model-access/physical-environment-access|AML.T0041: Physical Environment Access]]

**Step 10:**
- Technique: [[atlas/techniques/initial-access/evade-ai-model|AML.T0015: Evade AI Model]]

## External References

- DeepPayload: Black-box Backdoor Attack on Deep Learning Models through Neural Payload Injection Available at: https://arxiv.org/abs/2101.06896

## References

MITRE Corporation. *Backdoor Attack on Deep Learning Models in Mobile Apps (AML.CS0013)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0013
