---
id: code-signing
title: "AML.M0013: Code Signing"
sidebar_label: "Code Signing"
sidebar_position: 14
type: mitigation
---

# AML.M0013: Code Signing

Enforce binary and application integrity with digital signature verification to prevent untrusted code from executing. Adversaries can embed malicious code in AI software or models. Enforcement of code signing can prevent the compromise of the AI supply chain and prevent execution of malicious code.

## Metadata

- **Mitigation ID:** AML.M0013
- **Created:** 2023-04-12
- **Last Modified:** 2025-12-23
- **Category:** Technical - Cyber
- **ML Lifecycle:** Deployment

## Techniques (8)

- [[frameworks/atlas/techniques/execution/user-execution/user-execution-unsafe-AI-artifacts|AML.T0011.000: Unsafe AI Artifacts]] — Prevent execution of ML artifacts that are not properly signed.
- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-model|AML.T0010.001: AI Software]] — Enforce properly signed drivers and ML software frameworks.
- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-hardware|AML.T0010.003: Model]] — Enforce properly signed model files.
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-poison|AML.T0018: Manipulate AI Model]] — Code signing provides a guarantee that the model has not been manipulated after signing took place.
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-poison|AML.T0018.000: Poison AI Model]] — Code signing provides a guarantee that the model has not been manipulated after signing took place.
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-modify-architecture|AML.T0018.001: Modify AI Model Architecture]] — Code signing provides a guarantee that the model has not been manipulated after signing took place.
- [[frameworks/atlas/techniques/persistence/manipulate-ai-model/manipulate-AI-model-embed-malware|AML.T0018.002: Embed Malware]] — Code signing provides a guarantee that the model has not been manipulated after signing took place.
- [[frameworks/atlas/techniques/execution/user-execution/user-execution-malicious-package|AML.T0011.001: Malicious Package]] — Code signing provides a guarantee that the software package has not been manipulated after signing took place.
