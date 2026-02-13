---
id: external-harms-ai-intellectual-property-theft
title: "AML.T0048.004: AI Intellectual Property Theft"
sidebar_label: "AI Intellectual Property Theft"
sidebar_position: 10
---

# AML.T0048.004: AI Intellectual Property Theft

> **Sub-Technique of:** [[frameworks/atlas/techniques/impact/external-harms/external-harms|AML.T0048: External Harms]]

Adversaries may exfiltrate AI artifacts to steal intellectual property and cause economic harm to the victim organization.

Proprietary training data is costly to collect and annotate and may be a target for  and theft.

AIaaS providers charge for use of their API.
An adversary who has stolen a model via  or via [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/extract-ai-model|Extract AI Model]] now has unlimited use of that service without paying the owner of the intellectual property.

## Metadata

- **Technique ID:** AML.T0048.004
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** demonstrated

- **Parent Technique:** [[frameworks/atlas/techniques/impact/external-harms/external-harms|AML.T0048: External Harms]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (4)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/attack-on-machine-translation-services|AML.CS0005: Attack on Machine Translation Services]]

By replicating the model with high fidelity, the researchers demonstrated that an adversary could steal a model and violate the victim's intellectual property rights.

### [[frameworks/atlas/case-studies/arbitrary-code-execution-with-google-colab|AML.CS0018: Arbitrary Code Execution with Google Colab]]

Exfiltrated data may include sensitive or private data such as ML model artifacts stored in Google Drive.

### [[frameworks/atlas/case-studies/organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]

With full access to the model, an adversary could steal valuable intellectual property in the form of AI models.

### [[frameworks/atlas/case-studies/ai-model-tampering-via-supply-chain-attack|AML.CS0028: AI Model Tampering via Supply Chain Attack]]

With full access to the model(s), an adversary has an organization's valuable intellectual property.

## References

MITRE Corporation. *AI Intellectual Property Theft (AML.T0048.004)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0048.004
