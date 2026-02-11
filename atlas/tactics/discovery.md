---
id: discovery
title: "AML.TA0008: Discovery"
sidebar_label: "Discovery"
sidebar_position: 10
---

# AML.TA0008: Discovery

The adversary is trying to figure out your AI environment.

Discovery consists of techniques an adversary may use to gain knowledge about the system and internal network.
These techniques help adversaries observe the environment and orient themselves before deciding how to act.
They also allow adversaries to explore what they can control and what's around their entry point in order to discover how it could benefit their current objective.
Native operating system tools are often used toward this post-compromise information-gathering objective.

## Metadata

- **Tactic ID:** AML.TA0008
- **Created:** January 24, 2022
- **Last Modified:** April 9, 2025
- **MITRE ATT&CK Reference:** [TA0007](https://attack.mitre.org/tactics/TA0007/)

## Techniques (9)

The following techniques can be used to achieve this tactic:


| Technique ID | Name | Maturity |
|---|---|---|
| [[discover-ai-model-ontology|AML.T0013]] | Discover AI Model Ontology | demonstrated |
| [[discover-ai-model-family|AML.T0014]] | Discover AI Model Family | feasible |
| [[discover-ai-artifacts|AML.T0007]] | Discover AI Artifacts | demonstrated |
| [[discover-llm-hallucinations|AML.T0062]] | Discover LLM Hallucinations | demonstrated |
| [[discover-ai-model-outputs|AML.T0063]] | Discover AI Model Outputs | demonstrated |
| [[discover-llm-system-information|AML.T0069]] | Discover LLM System Information | demonstrated |
| [[cloud-service-discovery|AML.T0075]] | Cloud Service Discovery | realized |
| [[discover-ai-agent-configuration|AML.T0084]] | Discover AI Agent Configuration | demonstrated |
| [[process-discovery|AML.T0089]] | Process Discovery | demonstrated |


## Case Studies (10)


The following case studies demonstrate this tactic:

- [[bypassing-cylance-s-ai-malware-detection|AML.CS0003: Bypassing Cylance's AI Malware Detection]]
- [[proofpoint-evasion|AML.CS0008: ProofPoint Evasion]]
- [[face-identification-system-evasion-via-physical-countermeasures|AML.CS0012: Face Identification System Evasion via Physical Countermeasures]]
- [[chatgpt-package-hallucination|AML.CS0022: ChatGPT Package Hallucination]]
- [[financial-transaction-hijacking-with-m365-copilot-as-an-insider|AML.CS0026: Financial Transaction Hijacking with M365 Copilot as an Insider]]
- [[organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]
- [[ai-model-tampering-via-supply-chain-attack|AML.CS0028: AI Model Tampering via Supply Chain Attack]]
- [[llm-jacking|AML.CS0030: LLM Jacking]]
- [[aikatz-attacking-llm-desktop-applications|AML.CS0036: AIKatz: Attacking LLM Desktop Applications]]
- [[data-exfiltration-via-agent-tools-in-copilot-studio|AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio]]


## References

MITRE Corporation. *Discovery (AML.TA0008)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0008
