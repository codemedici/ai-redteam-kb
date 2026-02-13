---
id: user-execution-poisoned-ai-agent-tool
title: "AML.T0011.002: Poisoned AI Agent Tool"
sidebar_label: "Poisoned AI Agent Tool"
sidebar_position: 11003
---

# AML.T0011.002: Poisoned AI Agent Tool

A victim may invoke a poisoned tool when interacting with their AI agent. A poisoned tool may execute an [LLM Prompt Injection](/techniques/AML.T0051) or perform [AI Agent Tool Invocation](/techniques/AML.T0053).

Poisoned AI agent tools may be introduced into the victim's environment via [AI Software](/techniques/AML.T0010.001), or the user may configure their agent to connect to remote tools.

## Metadata

- **Technique ID:** AML.T0011.002
- **Created:** 2026-01-30
- **Last Modified:** 2026-02-05
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1204](https://attack.mitre.org/techniques/T1204/)

## Parent Technique

**Parent Technique:** AML.T0011 — User Execution

## Tactics (1)

- [[frameworks/atlas/tactics/execution|Execution]]

## Case Studies (1)

- [[frameworks/atlas/case-studies/supply-chain-compromise-via-poisoned-clawdbot-skill|Supply Chain Compromise via Poisoned ClawdBot Skill]]
