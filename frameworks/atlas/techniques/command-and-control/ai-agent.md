---
id: ai-agent
title: "AML.T0108: AI Agent"
sidebar_label: "AI Agent"
sidebar_position: 109
---

# AML.T0108: AI Agent

Adversaries may abuse AI agents present on the victim's system for command and control. AI agents are often granted access to tools that can execute shell commands, reach out to the internet, and interact with other services in the victim's environment, making them capable C2 agents.

The adversary may modify the behavior of an AI agent for C2 via [LLM Prompt Injection](/techniques/AML.T0051) and rely on the agent's ability to invoke tools to retrieve and execute the adversary's commands. They may maintain persistent control of an agent via [Modify AI Agent Configuration](/techniques/AML.T0081) or [AI Agent Context Poisoning](/techniques/AML.T0080). They may instruct the agent to not report their actions to the user in an attempt to remain covert.

## Metadata

- **Technique ID:** AML.T0108
- **Created:** 2026-01-30
- **Last Modified:** 2026-02-05
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/command-and-control|Command and Control]]

## Case Studies (1)

- [[frameworks/atlas/case-studies/openclaw-command-control-via-prompt-injection|OpenClaw Command & Control via Prompt Injection]]
