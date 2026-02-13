---
id: ai-supply-chain-compromise-ai-software
title: "AML.T0010.001: AI Software"
sidebar_label: "AI Software"
sidebar_position: 3
---

# AML.T0010.001: AI Software

> **Sub-Technique of:** [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/ai-supply-chain-compromise|AML.T0010: AI Supply Chain Compromise]]

Most AI systems rely on a limited set of AI frameworks.
An adversary could get access to a large number of AI systems through a comprise of one of their supply chains.
Many AI projects also rely on other open source implementations of various algorithms.
These can also be compromised in a targeted way to get access to specific systems.

## Metadata

- **Technique ID:** AML.T0010.001
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** realized

- **Parent Technique:** [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/ai-supply-chain-compromise|AML.T0010: AI Supply Chain Compromise]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (4)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/compromised-pytorch-dependency-chain|AML.CS0015: Compromised PyTorch Dependency Chain]]

A malicious dependency package named `torchtriton` was uploaded to the PyPI code repository with the same package name as a package shipped with the PyTorch-nightly build. This malicious package contained additional code that uploads sensitive data from the machine.
The malicious `torchtriton` package was installed instead of the legitimate one because PyPI is prioritized over other sources. See more details at [this GitHub issue](https://github.com/pypa/pip/issues/8606).

### [[frameworks/atlas/case-studies/arbitrary-code-execution-with-google-colab|AML.CS0018: Arbitrary Code Execution with Google Colab]]

Jupyter notebooks are often used for ML and data science research and experimentation, containing executable snippets of Python code and common Unix command-line functionality.
Users may come across a compromised notebook on public websites or through direct sharing.

### [[frameworks/atlas/case-studies/chatgpt-package-hallucination|AML.CS0022: ChatGPT Package Hallucination]]

A user of ChatGPT or other LLM may ask similar questions which lead to the same hallucinated package name and cause them to download the malicious package.

The researchers showed that multiple LLMs can produce the same hallucinations. They tracked over 30,000 downloads of the `huggingface-cli` package.

### [[frameworks/atlas/case-studies/rules-file-backdoor-supply-chain-attack-on-ai-coding-assistants|AML.CS0041: Rules File Backdoor: Supply Chain Attack on AI Coding Assistants]]

The researchers could have uploaded the malicious rules file to open-source communities where AI coding assistant configurations are shared with minimal security vetting such as GitHub and cursor.directory. Once incorporated into a project repository it may survive project forking and template distribution, creating long-term compromise of many organizations’ AI software supply chains.

## References

MITRE Corporation. *AI Software (AML.T0010.001)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0010.001
