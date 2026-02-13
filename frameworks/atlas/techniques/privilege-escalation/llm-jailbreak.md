---
id: llm-jailbreak
title: "AML.T0054: LLM Jailbreak"
sidebar_label: "LLM Jailbreak"
sidebar_position: 55
---

# AML.T0054: LLM Jailbreak

An adversary may use a carefully crafted [LLM Prompt Injection](/techniques/AML.T0051) designed to place LLM in a state in which it will freely respond to any user input, bypassing any controls, restrictions, or guardrails placed on the LLM.
Once successfully jailbroken, the LLM can be used in unintended ways by the adversary.

## Metadata

- **Technique ID:** AML.T0054
- **Created:** 2023-10-25
- **Last Modified:** 2023-10-25
- **Maturity:** demonstrated

## Tactics (2)

- [[frameworks/atlas/tactics/privilege-escalation|Privilege Escalation]]
- [[frameworks/atlas/tactics/defense-evasion|Defense Evasion]]

## Mitigations (3)

- [[frameworks/atlas/mitigations/generative-ai-guardrails|AML.M0020: Generative AI Guardrails]] — Guardrails can prevent harmful inputs that can lead to a jailbreak.
- [[frameworks/atlas/mitigations/generative-ai-guidelines|AML.M0021: Generative AI Guidelines]] — Model guidelines can instruct the model to refuse a response to unsafe inputs.
- [[frameworks/atlas/mitigations/generative-ai-model-alignment|AML.M0022: Generative AI Model Alignment]] — Model alignment can improve the parametric safety of a model by guiding it away from unsafe prompts and responses.

## Case Studies (3)

- [[frameworks/atlas/case-studies/rules-file-backdoor-supply-chain-attack-on-ai-coding-assistants|Rules File Backdoor: Supply Chain Attack on AI Coding Assistants]]
- [[frameworks/atlas/case-studies/data-destruction-via-indirect-prompt-injection-targeting-claude-computer-use|Data Destruction via Indirect Prompt Injection Targeting Claude Computer-Use]]
- [[frameworks/atlas/case-studies/openclaw-command-control-via-prompt-injection|OpenClaw Command & Control via Prompt Injection]]
