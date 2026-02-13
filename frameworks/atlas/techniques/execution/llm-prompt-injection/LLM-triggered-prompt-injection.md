---
id: LLM-triggered-prompt-injection
title: "AML.T0051.002: Triggered"
sidebar_label: "Triggered"
sidebar_position: 51003
---

# AML.T0051.002: Triggered

An adversary may trigger a prompt injection via a user action or event that occurs within the victim's environment. Triggered prompt injections often target AI agents, which can be activated by means the adversary identifies during [Discovery](/tactics/AML.TA0008) (See [Activation Triggers](/techniques/AML.T0084.002)). These malicious prompts may be hidden or obfuscated from the user and may already exist somewhere in the victim's environment from the adversary performing [Prompt Infiltration via Public-Facing Application](/techniques/AML.T0093). This type of injection may be used by the adversary to gain a foothold in the system or to target an unwitting user of the system.

## Metadata

- **Technique ID:** AML.T0051.002
- **Created:** 2025-11-04
- **Last Modified:** 2025-11-05
- **Maturity:** demonstrated

## Parent Technique

**Parent Technique:** AML.T0051 — LLM Prompt Injection

## Tactics (1)

- [[frameworks/atlas/tactics/execution|Execution]]

## Mitigations (2)

- [[frameworks/atlas/mitigations/ai-telemetry-logging|AML.M0024: AI Telemetry Logging]] — Telemetry logging can help identify if unsafe prompts have been submitted to the LLM.
- [[frameworks/atlas/mitigations/input-and-output-validation-for-ai-agent-components|AML.M0033: Input and Output Validation for AI Agent Components]] — Validation can prevent adversaries from executing prompt injections that could affect agentic workflows.

## Case Studies (2)

- [[frameworks/atlas/case-studies/morris-ii-worm-rag-based-attack|Morris II Worm: RAG-Based Attack]]
- [[frameworks/atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|Data Exfiltration via Agent Tools in Copilot Studio]]
