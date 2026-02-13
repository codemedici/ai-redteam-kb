---
id: llm-data-leakage
title: "AML.T0057: LLM Data Leakage"
sidebar_label: "LLM Data Leakage"
sidebar_position: 58
---

# AML.T0057: LLM Data Leakage

Adversaries may craft prompts that induce the LLM to leak sensitive information.
This can include private user data or proprietary information.
The leaked information may come from proprietary training data, data sources the LLM is connected to, or information from other users of the LLM.

## Metadata

- **Technique ID:** AML.T0057
- **Created:** 2023-10-25
- **Last Modified:** 2023-10-25
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/exfiltration|Exfiltration]]

## Case Studies (1)

- [[frameworks/atlas/case-studies/morris-ii-worm-rag-based-attack|Morris II Worm: RAG-Based Attack]]
