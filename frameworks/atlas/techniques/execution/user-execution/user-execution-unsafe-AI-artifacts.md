---
id: user-execution-unsafe-AI-artifacts
title: "AML.T0011.000: Unsafe AI Artifacts"
sidebar_label: "Unsafe AI Artifacts"
sidebar_position: 11001
---

# AML.T0011.000: Unsafe AI Artifacts

Adversaries may develop unsafe AI artifacts that when executed have a deleterious effect.
The adversary can use this technique to establish persistent access to systems.
These models may be introduced via a [AI Supply Chain Compromise](/techniques/AML.T0010).

Serialization of models is a popular technique for model storage, transfer, and loading.
However, this format without proper checking presents an opportunity for code execution.

## Metadata

- **Technique ID:** AML.T0011.000
- **Created:** 2021-05-13
- **Last Modified:** 2025-04-09
- **Maturity:** realized

## Parent Technique

**Parent Technique:** AML.T0011 — User Execution

## Tactics (1)

- [[frameworks/atlas/tactics/execution|Execution]]

## Mitigations (6)

- [[frameworks/atlas/mitigations/restrict-library-loading|AML.M0011: Restrict Library Loading]] — Restrict library loading by ML artifacts.
- [[frameworks/atlas/mitigations/code-signing|AML.M0013: Code Signing]] — Prevent execution of ML artifacts that are not properly signed.
- [[frameworks/atlas/mitigations/verify-ai-artifacts|AML.M0014: Verify AI Artifacts]] — Introduce proper checking of signatures to ensure that unsafe AI artifacts will not be executed in the system.
- [[frameworks/atlas/mitigations/vulnerability-scanning|AML.M0016: Vulnerability Scanning]] — Vulnerability scanning can help identify malicious AI artifacts, such as models or data, and prevent user execution.
- [[frameworks/atlas/mitigations/user-training|AML.M0018: User Training]] — Train users to identify attempts of manipulation to prevent them from running unsafe code which when executed could develop unsafe artifacts. These artifacts may have a detrimental effect on the system.
- [[frameworks/atlas/mitigations/ai-bill-of-materials|AML.M0023: AI Bill of Materials]] — An AI BOM can help users identify untrustworthy model artifacts.

## Case Studies (2)

- [[frameworks/atlas/case-studies/organization-confusion-on-hugging-face|Organization Confusion on Hugging Face]]
- [[frameworks/atlas/case-studies/malicious-models-on-hugging-face|Malicious Models on Hugging Face]]
