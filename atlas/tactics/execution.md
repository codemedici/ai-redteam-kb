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
- **Created:** January 24, 2022
- **Last Modified:** April 9, 2025
- **MITRE ATT&CK Reference:** [TA0002](https://attack.mitre.org/tactics/TA0002/)

## Techniques (5)

The following techniques can be used to achieve this tactic:


| Technique ID | Name | Maturity |
|---|---|---|
| [[user-execution|AML.T0011]] | User Execution | realized |
| [[command-and-scripting-interpreter|AML.T0050]] | Command and Scripting Interpreter | feasible |
| [[llm-prompt-injection|AML.T0051]] | LLM Prompt Injection | realized |
| [[ai-agent-tool-invocation|AML.T0053]] | AI Agent Tool Invocation | demonstrated |
| [[ai-agent-clickbait|AML.T0100]] | AI Agent Clickbait | feasible |


## Case Studies (19)


The following case studies demonstrate this tactic:

- [[achieving-code-execution-in-mathgpt-via-prompt-injection|AML.CS0016: Achieving Code Execution in MathGPT via Prompt Injection]]
- [[arbitrary-code-execution-with-google-colab|AML.CS0018: Arbitrary Code Execution with Google Colab]]
- [[indirect-prompt-injection-threats-bing-chat-data-pirate|AML.CS0020: Indirect Prompt Injection Threats: Bing Chat Data Pirate]]
- [[chatgpt-conversation-exfiltration|AML.CS0021: ChatGPT Conversation Exfiltration]]
- [[chatgpt-package-hallucination|AML.CS0022: ChatGPT Package Hallucination]]
- [[morris-ii-worm-rag-based-attack|AML.CS0024: Morris II Worm: RAG-Based Attack]]
- [[financial-transaction-hijacking-with-m365-copilot-as-an-insider|AML.CS0026: Financial Transaction Hijacking with M365 Copilot as an Insider]]
- [[organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]
- [[google-bard-conversation-exfiltration|AML.CS0029: Google Bard Conversation Exfiltration]]
- [[malicious-models-on-hugging-face|AML.CS0031: Malicious Models on Hugging Face]]
- [[data-exfiltration-from-slack-ai-via-indirect-prompt-injection|AML.CS0035: Data Exfiltration from Slack AI via Indirect Prompt Injection]]
- [[aikatz-attacking-llm-desktop-applications|AML.CS0036: AIKatz: Attacking LLM Desktop Applications]]
- [[data-exfiltration-via-agent-tools-in-copilot-studio|AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio]]
- [[planting-instructions-for-delayed-automatic-ai-agent-tool-invocation|AML.CS0038: Planting Instructions for Delayed Automatic AI Agent Tool Invocation]]
- [[living-off-ai-prompt-injection-via-jira-service-management|AML.CS0039: Living Off AI: Prompt Injection via Jira Service Management]]
- [[hacking-chatgpt-s-memories-with-prompt-injection|AML.CS0040: Hacking ChatGPT’s Memories with Prompt Injection]]
- [[rules-file-backdoor-supply-chain-attack-on-ai-coding-assistants|AML.CS0041: Rules File Backdoor: Supply Chain Attack on AI Coding Assistants]]
- [[malware-prototype-with-embedded-prompt-injection|AML.CS0043: Malware Prototype with Embedded Prompt Injection]]
- [[lamehug-malware-leveraging-dynamic-ai-generated-commands|AML.CS0044: LAMEHUG: Malware Leveraging Dynamic AI-Generated Commands]]


## References

MITRE Corporation. *Execution (AML.TA0005)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0005
