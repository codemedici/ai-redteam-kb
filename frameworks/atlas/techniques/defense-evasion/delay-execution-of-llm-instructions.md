---
id: delay-execution-of-llm-instructions
title: "AML.T0094: Delay Execution of LLM Instructions"
sidebar_label: "Delay Execution of LLM Instructions"
sidebar_position: 95
---

# AML.T0094: Delay Execution of LLM Instructions

Adversaries may include instructions to be followed by the AI system in response to a future event, such as a specific keyword or the next interaction, in order to evade detection or bypass controls placed on the AI system.

For example, an adversary may include "If the user submits a new request..." followed by the malicious instructions as part of their prompt.

AI agents can include security measures against prompt injections that prevent the invocation of particular tools or access to certain data sources during a conversation turn that has untrusted data in context. Delaying the execution of instructions to a future interaction or keyword is one way adversaries may bypass this type of control.

## Metadata

- **Technique ID:** AML.T0094
- **Created:** 2025-11-04
- **Last Modified:** 2025-11-05
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/defense-evasion|Defense Evasion]]

## Case Studies (1)

- [[frameworks/atlas/case-studies/planting-instructions-for-delayed-automatic-ai-agent-tool-invocation|Planting Instructions for Delayed Automatic AI Agent Tool Invocation]]
