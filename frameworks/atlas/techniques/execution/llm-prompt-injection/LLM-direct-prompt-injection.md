---
id: LLM-direct-prompt-injection
title: "AML.T0051.000: Direct"
sidebar_label: "Direct"
sidebar_position: 51001
---

# AML.T0051.000: Direct

An adversary may inject prompts directly as a user of the LLM. This type of injection may be used by the adversary to gain a foothold in the system or to misuse the LLM itself, as for example to generate harmful content.

## Metadata

- **Technique ID:** AML.T0051.000
- **Created:** 2023-10-25
- **Last Modified:** 2023-10-25
- **Maturity:** realized

## Parent Technique

**Parent Technique:** AML.T0051 — LLM Prompt Injection

## Tactics (1)

- [[frameworks/atlas/tactics/execution|Execution]]

## Mitigations (2)

- [[frameworks/atlas/mitigations/ai-telemetry-logging|AML.M0024: AI Telemetry Logging]] — Telemetry logging can help identify if unsafe prompts have been submitted to the LLM.
- [[frameworks/atlas/mitigations/input-and-output-validation-for-ai-agent-components|AML.M0033: Input and Output Validation for AI Agent Components]] — Validation can prevent adversaries from executing prompt injections that could affect agentic workflows.

## Case Studies (8)

- [[frameworks/atlas/case-studies/achieving-code-execution-in-mathgpt-via-prompt-injection|Achieving Code Execution in MathGPT via Prompt Injection]]
- [[frameworks/atlas/case-studies/morris-ii-worm-rag-based-attack|Morris II Worm: RAG-Based Attack]]
- [[frameworks/atlas/case-studies/aikatz-attacking-llm-desktop-applications|AIKatz: Attacking LLM Desktop Applications]]
- [[frameworks/atlas/case-studies/rules-file-backdoor-supply-chain-attack-on-ai-coding-assistants|Rules File Backdoor: Supply Chain Attack on AI Coding Assistants]]
- [[frameworks/atlas/case-studies/malware-prototype-with-embedded-prompt-injection|Malware Prototype with Embedded Prompt Injection]]
- [[frameworks/atlas/case-studies/code-to-deploy-destructive-ai-agent-discovered-in-amazon-q-vs-code-extension|Code to Deploy Destructive AI Agent Discovered in Amazon Q VS Code Extension]]
- [[frameworks/atlas/case-studies/supply-chain-compromise-via-poisoned-clawdbot-skill|Supply Chain Compromise via Poisoned ClawdBot Skill]]
- [[frameworks/atlas/case-studies/openclaw-command-control-via-prompt-injection|OpenClaw Command & Control via Prompt Injection]]
