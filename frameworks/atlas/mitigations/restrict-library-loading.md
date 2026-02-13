---
id: restrict-library-loading
title: "AML.M0011: Restrict Library Loading"
sidebar_label: "Restrict Library Loading"
sidebar_position: 12
type: mitigation
---

# AML.M0011: Restrict Library Loading

Prevent abuse of library loading mechanisms in the operating system and software to load untrusted code by configuring appropriate library loading mechanisms and investigating potential vulnerable software.

File formats such as pickle files that are commonly used to store AI models can contain exploits that allow for loading of malicious libraries.

## Metadata

- **Mitigation ID:** AML.M0011
- **Created:** 2023-04-12
- **Last Modified:** 2025-12-23
- **Category:** Technical - Cyber
- **ML Lifecycle:** Deployment

## Techniques (3)

- [[frameworks/atlas/techniques/execution/user-execution/user-execution-unsafe-AI-artifacts|AML.T0011.000: Unsafe AI Artifacts]] — Restrict library loading by ML artifacts.
- [[frameworks/atlas/techniques/execution/user-execution/user-execution-malicious-package|AML.T0011.001: Malicious Package]] — Restricting packages from loading external libraries can limit their ability to execute malicious code.
- [[frameworks/atlas/techniques/execution/user-execution|AML.T0011: User Execution]] — Restricting binaries from loading external libraries can limit their ability to execute malicious code.
