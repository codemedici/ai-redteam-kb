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
| [[atlas/techniques/discovery/discover-ai-model-ontology|AML.T0013]] | Discover AI Model Ontology | demonstrated |
| [[atlas/techniques/discovery/discover-ai-model-family|AML.T0014]] | Discover AI Model Family | feasible |
| [[atlas/techniques/discovery/discover-ai-artifacts|AML.T0007]] | Discover AI Artifacts | demonstrated |
| [[atlas/techniques/discovery/discover-llm-hallucinations|AML.T0062]] | Discover LLM Hallucinations | demonstrated |
| [[atlas/techniques/discovery/discover-ai-model-outputs|AML.T0063]] | Discover AI Model Outputs | demonstrated |
| [[atlas/techniques/discovery/discover-llm-system-information/discover-llm-system-information-overview|AML.T0069]] | Discover LLM System Information | demonstrated |
| [[atlas/techniques/discovery/cloud-service-discovery|AML.T0075]] | Cloud Service Discovery | realized |
| [[atlas/techniques/discovery/discover-ai-agent-configuration/discover-ai-agent-configuration-overview|AML.T0084]] | Discover AI Agent Configuration | demonstrated |
| [[atlas/techniques/discovery/process-discovery|AML.T0089]] | Process Discovery | demonstrated |


## Case Studies (10)


The following case studies demonstrate this tactic:

- [[atlas/case-studies/bypassing-cylance-s-ai-malware-detection|AML.CS0003: Bypassing Cylance's AI Malware Detection]]
- [[atlas/case-studies/proofpoint-evasion|AML.CS0008: ProofPoint Evasion]]
- [[atlas/case-studies/face-identification-system-evasion-via-physical-countermeasures|AML.CS0012: Face Identification System Evasion via Physical Countermeasures]]
- [[atlas/case-studies/chatgpt-package-hallucination|AML.CS0022: ChatGPT Package Hallucination]]
- [[atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|AML.CS0026: Financial Transaction Hijacking with M365 Copilot as an Insider]]
- [[atlas/case-studies/organization-confusion-on-hugging-face|AML.CS0027: Organization Confusion on Hugging Face]]
- [[atlas/case-studies/ai-model-tampering-via-supply-chain-attack|AML.CS0028: AI Model Tampering via Supply Chain Attack]]
- [[atlas/case-studies/llm-jacking|AML.CS0030: LLM Jacking]]
- [[atlas/case-studies/aikatz-attacking-llm-desktop-applications|AML.CS0036: AIKatz: Attacking LLM Desktop Applications]]
- [[atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio]]


## References

MITRE Corporation. *Discovery (AML.TA0008)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0008
