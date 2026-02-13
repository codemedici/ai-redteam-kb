---
id: chatgpt-package-hallucination
title: "AML.CS0022: ChatGPT Package Hallucination"
sidebar_label: "ChatGPT Package Hallucination"
sidebar_position: 23
---

# AML.CS0022: ChatGPT Package Hallucination

## Summary

Researchers identified that large language models such as ChatGPT can hallucinate fake software package names that are not published to a package repository. An attacker could publish a malicious package under the hallucinated name to a package repository. Then users of the same or similar large language models may encounter the same hallucination and ultimately download and execute the malicious package leading to a variety of potential harms.

## Metadata

- **Case Study ID:** AML.CS0022
- **Incident Date:** 2024
- **Type:** exercise
- **Target:** ChatGPT users
- **Actor:** Vulcan Cyber, Lasso Security

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: AI Model Inference API Access

**Technique:** [[frameworks/atlas/techniques/ai-model-access/ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]]

The researchers use the public ChatGPT API throughout this exercise.

### Step 2: Discover LLM Hallucinations

**Technique:** [[frameworks/atlas/techniques/discovery/discover-llm-hallucinations|AML.T0062: Discover LLM Hallucinations]]

The researchers prompt ChatGPT to suggest software packages and identify suggestions that are hallucinations which don't exist in a public package repository.

For example, when asking the model "how to upload a model to huggingface?" the response included guidance to install the `huggingface-cli` package with instructions to install it by `pip install huggingface-cli`. This package was a hallucination and does not exist on PyPI. The actual HuggingFace CLI tool is part of the `huggingface_hub` package.

### Step 3: Publish Hallucinated Entities

**Technique:** [[frameworks/atlas/techniques/resource-development/publish-hallucinated-entities|AML.T0060: Publish Hallucinated Entities]]

An adversary could upload a malicious package under the hallucinated name to PyPI or other package registries.

In practice, the researchers uploaded an empty package to PyPI to track downloads.

### Step 4: AI Software

**Technique:** [[frameworks/atlas/techniques/initial-access/supply-chain-compromise-AI-software|AML.T0010.001: AI Software]]

A user of ChatGPT or other LLM may ask similar questions which lead to the same hallucinated package name and cause them to download the malicious package.

The researchers showed that multiple LLMs can produce the same hallucinations. They tracked over 30,000 downloads of the `huggingface-cli` package.

### Step 5: Malicious Package

**Technique:** [[frameworks/atlas/techniques/execution/user-execution-malicious-package|AML.T0011.001: Malicious Package]]

The user would ultimately load the malicious package, allowing for arbitrary code execution.

### Step 6: User Harm

**Technique:** [[frameworks/atlas/techniques/impact/external-harms-user|AML.T0048.003: User Harm]]

This could lead to a variety of harms to the end user or organization.

## Tactics and Techniques Used

**Step 1:**
- Technique: [[frameworks/atlas/techniques/ai-model-access/ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]]

**Step 2:**
- Technique: [[frameworks/atlas/techniques/discovery/discover-llm-hallucinations|AML.T0062: Discover LLM Hallucinations]]

**Step 3:**
- Technique: [[frameworks/atlas/techniques/resource-development/publish-hallucinated-entities|AML.T0060: Publish Hallucinated Entities]]

**Step 4:**
- Technique: [[frameworks/atlas/techniques/initial-access/supply-chain-compromise-AI-software|AML.T0010.001: AI Software]]

**Step 5:**
- Technique: [[frameworks/atlas/techniques/execution/user-execution-malicious-package|AML.T0011.001: Malicious Package]]

**Step 6:**
- Technique: [[frameworks/atlas/techniques/impact/external-harms-user|AML.T0048.003: User Harm]]

## External References

- Vulcan18's "Can you trust ChatGPT's package recommendations?" Available at: https://vulcan.io/blog/ai-hallucinations-package-risk
- Lasso Security Research: Diving into AI Package Hallucinations Available at: https://www.lasso.security/blog/ai-package-hallucinations
- AIID Incident 731: Hallucinated Software Packages with Potential Malware Downloaded Thousands of Times by Developers Available at: https://incidentdatabase.ai/cite/731/
- Slopsquatting: When AI Agents Hallucinate Malicious Packages Available at: https://www.trendmicro.com/vinfo/us/security/news/cybercrime-and-digital-threats/slopsquatting-when-ai-agents-hallucinate-malicious-packages

## References

MITRE Corporation. *ChatGPT Package Hallucination (AML.CS0022)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0022
