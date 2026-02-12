---
id: publish-hallucinated-entities
title: "AML.T0060: Publish Hallucinated Entities"
sidebar_label: "Publish Hallucinated Entities"
sidebar_position: 16
---

# AML.T0060: Publish Hallucinated Entities

Adversaries may create an entity they control, such as a software package, website, or email address to a source hallucinated by an LLM. The hallucinations may take the form of package names commands, URLs, company names, or email addresses that point the victim to the entity controlled by the adversary. When the victim interacts with the adversary-controlled entity, the attack can proceed.

## Metadata

- **Technique ID:** AML.T0060
- **Created:** March 12, 2025
- **Last Modified:** October 31, 2025
- **Maturity:** demonstrated

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (1)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/chatgpt-package-hallucination|AML.CS0022: ChatGPT Package Hallucination]]

An adversary could upload a malicious package under the hallucinated name to PyPI or other package registries.

In practice, the researchers uploaded an empty package to PyPI to track downloads.

## References

MITRE Corporation. *Publish Hallucinated Entities (AML.T0060)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0060
