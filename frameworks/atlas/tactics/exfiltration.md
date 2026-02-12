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


- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-ai-inference-api-overview|AML.T0024]] — Exfiltration via AI Inference API (feasible)
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-cyber-means|AML.T0025]] — Exfiltration via Cyber Means (realized)
- [[frameworks/atlas/techniques/exfiltration/extract-llm-system-prompt|AML.T0056]] — Extract LLM System Prompt (feasible)
- [[frameworks/atlas/techniques/exfiltration/llm-data-leakage|AML.T0057]] — LLM Data Leakage (demonstrated)
- [[frameworks/atlas/techniques/exfiltration/llm-response-rendering|AML.T0077]] — LLM Response Rendering (demonstrated)
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-agent-tool-invocation|AML.T0086]] — Exfiltration via AI Agent Tool Invocation (demonstrated)


## References

MITRE Corporation. *Exfiltration (AML.TA0010)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0010
