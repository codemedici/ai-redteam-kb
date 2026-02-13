---
id: stage-capabilities
title: "AML.T0079: Stage Capabilities"
sidebar_label: "Stage Capabilities"
sidebar_position: 80
---

# AML.T0079: Stage Capabilities

Adversaries may upload, install, or otherwise set up capabilities that can be used during targeting. To support their operations, an adversary may need to take capabilities they developed ([Develop Capabilities](/techniques/AML.T0017)) or obtained ([Obtain Capabilities](/techniques/AML.T0016)) and stage them on infrastructure under their control. These capabilities may be staged on infrastructure that was previously purchased/rented by the adversary ([Acquire Infrastructure](/techniques/AML.T0008)) or was otherwise compromised by them. Capabilities may also be staged on web services, such as GitHub, model registries, such as Hugging Face, or container registries.

Adversaries may stage a variety of AI Artifacts including poisoned datasets ([Publish Poisoned Datasets](/techniques/AML.T0019), malicious models ([Publish Poisoned Models](/techniques/AML.T0058), and prompt injections. They may target names of legitimate companies or products, engage in typosquatting, or use hallucinated entities ([Discover LLM Hallucinations](/techniques/AML.T0062)).

## Metadata

- **Technique ID:** AML.T0079
- **Created:** 2025-04-16
- **Last Modified:** 2025-04-17
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1608](https://attack.mitre.org/techniques/T1608/)

## Tactics (1)

- [[frameworks/atlas/tactics/resource-development|Resource Development]]

## Case Studies (5)

- [[frameworks/atlas/case-studies/chatgpt-conversation-exfiltration|ChatGPT Conversation Exfiltration]]
- [[frameworks/atlas/case-studies/rules-file-backdoor-supply-chain-attack-on-ai-coding-assistants|Rules File Backdoor: Supply Chain Attack on AI Coding Assistants]]
- [[frameworks/atlas/case-studies/data-exfiltration-via-an-mcp-server-used-by-cursor|Data Exfiltration via an MCP Server used by Cursor]]
- [[frameworks/atlas/case-studies/openclaw-1-click-remote-code-execution|OpenClaw 1-Click Remote Code Execution]]
- [[frameworks/atlas/case-studies/openclaw-command-control-via-prompt-injection|OpenClaw Command & Control via Prompt Injection]]
