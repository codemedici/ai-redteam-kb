---
id: llm-prompt-obfuscation
title: "AML.T0068: LLM Prompt Obfuscation"
sidebar_label: "LLM Prompt Obfuscation"
sidebar_position: 69
---

# AML.T0068: LLM Prompt Obfuscation

Adversaries may hide or otherwise obfuscate prompt injections or retrieval content to avoid detection from humans, large language model (LLM) guardrails, or other detection mechanisms.

For text inputs, this may include modifying how the instructions are rendered such as small text, text colored the same as the background, or hidden HTML elements. For multi-modal inputs, malicious instructions could be hidden in the data itself (e.g. in the pixels of an image) or in file metadata (e.g. EXIF for images, ID3 tags for audio, or document metadata).

Inputs can also be obscured via an encoding scheme such as base64 or rot13. This may bypass LLM guardrails that identify malicious content and may not be as easily identifiable as malicious to a human in the loop.

## Metadata

- **Technique ID:** AML.T0068
- **Created:** 2025-03-12
- **Last Modified:** 2026-01-28
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/defense-evasion|Defense Evasion]]

## Case Studies (6)

- [[frameworks/atlas/case-studies/indirect-prompt-injection-threats-bing-chat-data-pirate|Indirect Prompt Injection Threats: Bing Chat Data Pirate]]
- [[frameworks/atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|Financial Transaction Hijacking with M365 Copilot as an Insider]]
- [[frameworks/atlas/case-studies/hacking-chatgpt-s-memories-with-prompt-injection|Hacking ChatGPT’s Memories with Prompt Injection]]
- [[frameworks/atlas/case-studies/rules-file-backdoor-supply-chain-attack-on-ai-coding-assistants|Rules File Backdoor: Supply Chain Attack on AI Coding Assistants]]
- [[frameworks/atlas/case-studies/data-exfiltration-via-an-mcp-server-used-by-cursor|Data Exfiltration via an MCP Server used by Cursor]]
- [[frameworks/atlas/case-studies/data-destruction-via-indirect-prompt-injection-targeting-claude-computer-use|Data Destruction via Indirect Prompt Injection Targeting Claude Computer-Use]]
