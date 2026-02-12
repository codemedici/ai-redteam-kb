---
id: exfiltration
title: "AML.TA0010: Exfiltration"
sidebar_label: "Exfiltration"
sidebar_position: 15
---

# AML.TA0010: Exfiltration

The adversary is trying to steal AI artifacts or other information about the AI system.

Exfiltration consists of techniques that adversaries may use to steal data from your network.
Data may be stolen for its valuable intellectual property, or for use in staging future operations.

Techniques for getting data out of a target network typically include transferring it over their command and control channel or an alternate channel and may also include putting size limits on the transmission.

## Metadata

- **Tactic ID:** AML.TA0010
- **Created:** January 24, 2022
- **Last Modified:** April 9, 2025
- **MITRE ATT&CK Reference:** [TA0010](https://attack.mitre.org/tactics/TA0010/)

## Techniques (6)

The following techniques can be used to achieve this tactic:


- [[atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-ai-inference-api-overview|AML.T0024]] — Exfiltration via AI Inference API (feasible)
- [[atlas/techniques/exfiltration/exfiltration-via-cyber-means|AML.T0025]] — Exfiltration via Cyber Means (realized)
- [[atlas/techniques/exfiltration/extract-llm-system-prompt|AML.T0056]] — Extract LLM System Prompt (feasible)
- [[atlas/techniques/exfiltration/llm-data-leakage|AML.T0057]] — LLM Data Leakage (demonstrated)
- [[atlas/techniques/exfiltration/llm-response-rendering|AML.T0077]] — LLM Response Rendering (demonstrated)
- [[atlas/techniques/exfiltration/exfiltration-via-ai-agent-tool-invocation|AML.T0086]] — Exfiltration via AI Agent Tool Invocation (demonstrated)


## Case Studies (13)


The following case studies demonstrate this tactic:

- [[atlas/case-studies/microsoft-azure-service-disruption|AML.CS0010: Microsoft Azure Service Disruption]]
- [[atlas/case-studies/compromised-pytorch-dependency-chain|AML.CS0015: Compromised PyTorch Dependency Chain]]
- [[atlas/case-studies/arbitrary-code-execution-with-google-colab|AML.CS0018: Arbitrary Code Execution with Google Colab]]
- [[atlas/case-studies/chatgpt-conversation-exfiltration|AML.CS0021: ChatGPT Conversation Exfiltration]]
- [[atlas/case-studies/shadowray|AML.CS0023: ShadowRay]]
- [[atlas/case-studies/morris-ii-worm-rag-based-attack|AML.CS0024: Morris II Worm: RAG-Based Attack]]
- [[atlas/case-studies/organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]
- [[atlas/case-studies/google-bard-conversation-exfiltration|AML.CS0029: Google Bard Conversation Exfiltration]]
- [[atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|AML.CS0035: Data Exfiltration from Slack AI via Indirect Prompt Injection]]
- [[atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio]]
- [[atlas/case-studies/living-off-ai-prompt-injection-via-jira-service-management|AML.CS0039: Living Off AI: Prompt Injection via Jira Service Management]]
- [[atlas/case-studies/malware-prototype-with-embedded-prompt-injection|AML.CS0043: Malware Prototype with Embedded Prompt Injection]]
- [[atlas/case-studies/lamehug-malware-leveraging-dynamic-ai-generated-commands|AML.CS0044: LAMEHUG: Malware Leveraging Dynamic AI-Generated Commands]]


## References

MITRE Corporation. *Exfiltration (AML.TA0010)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0010
