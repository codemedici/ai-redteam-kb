---
id: user-execution-malicious-package
title: "AML.T0011.001: Malicious Package"
sidebar_label: "Malicious Package"
sidebar_position: 11002
---

# AML.T0011.001: Malicious Package

Adversaries may develop malicious software packages that when imported by a user have a deleterious effect.
Malicious packages may behave as expected to the user. They may be introduced via [AI Supply Chain Compromise](/techniques/AML.T0010). They may not present as obviously malicious to the user and may appear to be useful for an AI-related task.

## Metadata

- **Technique ID:** AML.T0011.001
- **Created:** 2025-03-12
- **Last Modified:** 2025-03-12
- **Maturity:** realized

## Parent Technique

**Parent Technique:** AML.T0011 — User Execution

## Tactics (1)

- [[frameworks/atlas/tactics/execution|Execution]]

## Mitigations (5)

- [[frameworks/atlas/mitigations/restrict-library-loading|AML.M0011: Restrict Library Loading]] — Restricting packages from loading external libraries can limit their ability to execute malicious code.
- [[frameworks/atlas/mitigations/code-signing|AML.M0013: Code Signing]] — Code signing provides a guarantee that the software package has not been manipulated after signing took place.
- [[frameworks/atlas/mitigations/vulnerability-scanning|AML.M0016: Vulnerability Scanning]] — Vulnerability scanning can help identify malicious packages and prevent user execution.
- [[frameworks/atlas/mitigations/user-training|AML.M0018: User Training]] — Train users to identify attempts of manipulation to prevent them from running unsafe code from external packages.
- [[frameworks/atlas/mitigations/ai-bill-of-materials|AML.M0023: AI Bill of Materials]] — An AI BOM can help users identify untrustworthy software dependencies.

## Case Studies (2)

- [[frameworks/atlas/case-studies/chatgpt-package-hallucination|ChatGPT Package Hallucination]]
- [[frameworks/atlas/case-studies/code-to-deploy-destructive-ai-agent-discovered-in-amazon-q-vs-code-extension|Code to Deploy Destructive AI Agent Discovered in Amazon Q VS Code Extension]]
