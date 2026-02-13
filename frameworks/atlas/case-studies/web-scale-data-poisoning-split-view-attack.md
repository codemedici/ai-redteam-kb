---
id: web-scale-data-poisoning-split-view-attack
title: "AML.CS0025: Web-Scale Data Poisoning: Split-View Attack"
sidebar_label: "Web-Scale Data Poisoning: Split-View Attack"
sidebar_position: 26
---

# AML.CS0025: Web-Scale Data Poisoning: Split-View Attack

## Summary

Many recent large-scale datasets are distributed as a list of URLs pointing to individual datapoints. The researchers show that many of these datasets are vulnerable to a "split-view" poisoning attack. The attack exploits the fact that the data viewed when it was initially collected may differ from the data viewed by a user during training. The researchers identify expired and buyable domains that once hosted dataset content, making it possible to replace portions of the dataset with poisoned data. They demonstrate that for 10 popular web-scale datasets, enough of the domains are purchasable to successfully carry out a poisoning attack.

## Metadata

- **Case Study ID:** AML.CS0025
- **Incident Date:** 2024
- **Type:** exercise
- **Target:** 10 web-scale datasets
- **Actor:** Researchers from Google Deepmind, ETH Zurich, NVIDIA, Robust Intelligence, and Google

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Datasets

**Technique:** [[frameworks/atlas/techniques/resource-development/acquire-public-AI-artifacts-datasets|AML.T0002.000: Datasets]]

The researchers download a web-scale dataset, which consists of URLs pointing to individual datapoints.

### Step 2: Domains

**Technique:** [[frameworks/atlas/techniques/resource-development/acquire-infrastructure-domains|AML.T0008.002: Domains]]

They identify expired domains in the dataset and purchase them.

### Step 3: Poison Training Data

**Technique:** [[frameworks/atlas/techniques/resource-development/poison-training-data|AML.T0020: Poison Training Data]]

An adversary could create poisoned training data to replace expired portions of the dataset.

### Step 4: Publish Poisoned Datasets

**Technique:** [[frameworks/atlas/techniques/resource-development/publish-poisoned-datasets|AML.T0019: Publish Poisoned Datasets]]

An adversary could then upload the poisoned data to the domains they control.  In this particular exercise, the researchers track requests to the URLs they control to track downloads to demonstrate there are active users of the dataset.

### Step 5: Erode Dataset Integrity

**Technique:** [[frameworks/atlas/techniques/impact/erode-dataset-integrity|AML.T0059: Erode Dataset Integrity]]

The integrity of the dataset has been eroded because future downloads would contain poisoned datapoints.

### Step 6: Erode AI Model Integrity

**Technique:** [[frameworks/atlas/techniques/impact/erode-ai-model-integrity|AML.T0031: Erode AI Model Integrity]]

Models that use the dataset for training data are poisoned, eroding model integrity. The researchers show as little as 0.01% of the data needs to be poisoned for a successful attack.

## Tactics and Techniques Used

**Step 1:**
- Technique: [[frameworks/atlas/techniques/resource-development/acquire-public-AI-artifacts-datasets|AML.T0002.000: Datasets]]

**Step 2:**
- Technique: [[frameworks/atlas/techniques/resource-development/acquire-infrastructure-domains|AML.T0008.002: Domains]]

**Step 3:**
- Technique: [[frameworks/atlas/techniques/resource-development/poison-training-data|AML.T0020: Poison Training Data]]

**Step 4:**
- Technique: [[frameworks/atlas/techniques/resource-development/publish-poisoned-datasets|AML.T0019: Publish Poisoned Datasets]]

**Step 5:**
- Technique: [[frameworks/atlas/techniques/impact/erode-dataset-integrity|AML.T0059: Erode Dataset Integrity]]

**Step 6:**
- Technique: [[frameworks/atlas/techniques/impact/erode-ai-model-integrity|AML.T0031: Erode AI Model Integrity]]

## External References

- Poisoning Web-Scale Training Datasets is Practical Available at: https://arxiv.org/pdf/2302.10149

## References

MITRE Corporation. *Web-Scale Data Poisoning: Split-View Attack (AML.CS0025)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0025
