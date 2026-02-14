---
id: verify-ai-artifacts
title: "AML.M0014: Verify AI Artifacts"
sidebar_label: "Verify AI Artifacts"
sidebar_position: 15
type: mitigation
---

# AML.M0014: Verify AI Artifacts

Verify the cryptographic checksum of all AI artifacts to verify that the file was not modified by an attacker.

## Metadata

- **Mitigation ID:** AML.M0014
- **Created:** 2023-04-12
- **Last Modified:** 2025-12-23
- **Category:** Technical - Cyber
- **ML Lifecycle:** Business and Data Understanding, Data Preparation, ML Model Engineering

## Techniques (6)

- [[frameworks/atlas/techniques/resource-development/publish-poisoned-datasets|AML.T0019: Publish Poisoned Datasets]] — Determine validity of published data in order to avoid using poisoned data that introduces vulnerabilities.
- [[frameworks/atlas/techniques/execution/user-execution/user-execution-unsafe-AI-artifacts|AML.T0011.000: Unsafe AI Artifacts]] — Introduce proper checking of signatures to ensure that unsafe AI artifacts will not be executed in the system.
- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-data|AML.T0010: AI Supply Chain Compromise]] — Introduce proper checking of signatures to ensure that unsafe AI artifacts will not be introduced to the system.
- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-AI-software|AML.T0010.002: Data]] — Introduce proper checking of signatures to ensure that unsafe AI data will not be introduced to the system.
- [[frameworks/atlas/techniques/resource-development/acquire-public-ai-artifacts/acquire-public-AI-artifacts-models|AML.T0002.001: Models]] — Introduce proper checking of signatures to ensure that unsafe AI models will not be introduced to the system.
- [[frameworks/atlas/techniques/execution/user-execution/user-execution-unsafe-AI-artifacts|AML.T0011: User Execution]] — Introduce proper checking of signatures to ensure that unsafe AI artifacts will not be executed in the system.
