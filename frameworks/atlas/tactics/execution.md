---
id: execution
title: "AML.TA0005: Execution"
sidebar_label: "Execution"
sidebar_position: 5
---

# AML.TA0005: Execution

The adversary is trying to run malicious code embedded in AI artifacts or software.

Execution consists of techniques that result in adversary-controlled code running on a local or remote system.
Techniques that run malicious code are often paired with techniques from all other tactics to achieve broader goals, like exploring a network or stealing data.
For example, an adversary might use a remote access tool to run a PowerShell script that does [Remote System Discovery](https://attack.mitre.org/techniques/T1018/).

## Metadata

- **Tactic ID:** AML.TA0005
- **Created:** 2022-01-24
- **Last Modified:** 2025-04-09
- **MITRE ATT&CK Reference:** [TA0002](https://attack.mitre.org/tactics/TA0002/)

## Techniques (11)

The following techniques can be used to achieve this tactic:

- [[frameworks/atlas/techniques/execution/user-execution/user-execution-unsafe-AI-artifacts|AML.T0011.000]] — Unsafe AI Artifacts (realized)
- [[frameworks/atlas/techniques/execution/user-execution/user-execution-malicious-package|AML.T0011.001]] — Malicious Package (realized)
- [[frameworks/atlas/techniques/execution/user-execution/user-execution-poisoned-ai-agent-tool|AML.T0011.002]] — Poisoned AI Agent Tool (demonstrated)
- [[frameworks/atlas/techniques/execution/user-execution/user-execution-malicious-link|AML.T0011.003]] — Malicious Link (demonstrated)
- [[frameworks/atlas/techniques/execution/command-and-scripting-interpreter|AML.T0050]] — Command and Scripting Interpreter (demonstrated)
- [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-direct-prompt-injection|AML.T0051.000]] — Direct (realized)
- [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-indirect-prompt-injection|AML.T0051.001]] — Indirect (demonstrated)
- [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-triggered-prompt-injection|AML.T0051.002]] — Triggered (demonstrated)
- [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053]] — AI Agent Tool Invocation (demonstrated)
- [[frameworks/atlas/techniques/execution/ai-agent-clickbait|AML.T0100]] — AI Agent Clickbait (feasible)
- [[frameworks/atlas/techniques/execution/deploy-ai-agent|AML.T0103]] — Deploy AI Agent (realized)
