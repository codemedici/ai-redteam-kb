---
id: discover-llm-hallucinations
title: "AML.T0062: Discover LLM Hallucinations"
sidebar_label: "Discover LLM Hallucinations"
sidebar_position: 4
---

# AML.T0062: Discover LLM Hallucinations

Adversaries may prompt large language models and identify hallucinated entities.
They may request software packages, commands, URLs, organization names, or e-mail addresses, and identify hallucinations with no connected real-world source. Discovered hallucinations provide the adversary with potential targets to [[atlas/techniques/resource-development/publish-hallucinated-entities|Publish Hallucinated Entities]]. Different LLMs have been shown to produce the same hallucinations, so the hallucinations exploited by an adversary may affect users of other LLMs.

## Metadata

- **Technique ID:** AML.T0062
- **Created:** March 12, 2025
- **Last Modified:** October 31, 2025
- **Maturity:** demonstrated

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (1)

The following case studies demonstrate this technique:

### [[atlas/case-studies/chatgpt-package-hallucination|AML.CS0022: ChatGPT Package Hallucination]]

The researchers prompt ChatGPT to suggest software packages and identify suggestions that are hallucinations which don't exist in a public package repository.

For example, when asking the model "how to upload a model to huggingface?" the response included guidance to install the `huggingface-cli` package with instructions to install it by `pip install huggingface-cli`. This package was a hallucination and does not exist on PyPI. The actual HuggingFace CLI tool is part of the `huggingface_hub` package.

## References

MITRE Corporation. *Discover LLM Hallucinations (AML.T0062)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0062
