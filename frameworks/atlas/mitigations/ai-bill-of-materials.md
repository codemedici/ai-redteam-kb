---
id: ai-bill-of-materials
title: "AML.M0023: AI Bill of Materials"
sidebar_label: "AI Bill of Materials"
sidebar_position: 24
type: mitigation
---

# AML.M0023: AI Bill of Materials

An AI Bill of Materials (AI BOM) contains a full listing of artifacts and resources that were used in building the AI. The AI BOM can help mitigate supply chain risks and enable rapid response to reported vulnerabilities.

This can include maintaining dataset provenance, i.e. a detailed history of datasets used for AI applications. The history can include information about the dataset source as well as well as a complete record of any modifications.

## Metadata

- **Mitigation ID:** AML.M0023
- **Created:** 2025-03-12
- **Last Modified:** 2025-12-23
- **Category:** Policy
- **ML Lifecycle:** Business and Data Understanding, Data Preparation, ML Model Engineering

## Techniques (7)

- [[frameworks/atlas/techniques/execution/user-execution/user-execution-unsafe-AI-artifacts|AML.T0011.000: Unsafe AI Artifacts]] — An AI BOM can help users identify untrustworthy model artifacts.
- [[frameworks/atlas/techniques/resource-development/publish-poisoned-datasets|AML.T0019: Publish Poisoned Datasets]] — An AI BOM can help users identify untrustworthy model artifacts.
- [[frameworks/atlas/techniques/resource-development/poison-training-data|AML.T0020: Poison Training Data]] — An AI BOM can help users identify untrustworthy model artifacts.
- [[frameworks/atlas/techniques/resource-development/publish-poisoned-models|AML.T0058: Publish Poisoned Models]] — An AI BOM can help users identify untrustworthy model artifacts.
- [[frameworks/atlas/techniques/execution/user-execution/user-execution-malicious-package|AML.T0011.001: Malicious Package]] — An AI BOM can help users identify untrustworthy software dependencies.
- [[frameworks/atlas/techniques/execution/user-execution/user-execution-unsafe-AI-artifacts|AML.T0011: User Execution]] — An AI BOM can help users identify untrustworthy binaries.
- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-data|AML.T0010: AI Supply Chain Compromise]] — An AI BOM can help users identify untrustworthy components of their AI supply chain.
