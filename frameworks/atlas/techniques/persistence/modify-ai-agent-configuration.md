---
id: modify-ai-agent-configuration
title: "AML.T0081: Modify AI Agent Configuration"
sidebar_label: "Modify AI Agent Configuration"
sidebar_position: 82
---

# AML.T0081: Modify AI Agent Configuration

Adversaries may modify the configuration files for AI agents on a system. This allows malicious changes to persist beyond the life of a single agent and affects any agents that share the configuration.

Configuration changes may include modifications to the system prompt, tampering with or replacing knowledge sources, modification to settings of connected tools, and more. Through those changes, an attacker could redirect outputs or tools to malicious services, embed covert instructions that exfiltrate data, or weaken security controls that normally restrict agent behavior.

Adversaries may modify or disable a configuration setting related to security controls, such as those that would prevent the AI Agent from taking actions that may be harmful to the user's system without human-in-the-loop oversight. Disabling AI agent security features may allow adversaries to achieve their malicious goals and maintain long-term corruption of the AI agent.

## Metadata

- **Technique ID:** AML.T0081
- **Created:** 2025-09-30
- **Last Modified:** 2026-02-05
- **Maturity:** demonstrated

## Tactics (2)

- [[frameworks/atlas/tactics/persistence|Persistence]]
- [[frameworks/atlas/tactics/defense-evasion|Defense Evasion]]

## Case Studies (3)

- [[frameworks/atlas/case-studies/rules-file-backdoor-supply-chain-attack-on-ai-coding-assistants|Rules File Backdoor: Supply Chain Attack on AI Coding Assistants]]
- [[frameworks/atlas/case-studies/openclaw-1-click-remote-code-execution|OpenClaw 1-Click Remote Code Execution]]
- [[frameworks/atlas/case-studies/openclaw-command-control-via-prompt-injection|OpenClaw Command & Control via Prompt Injection]]
