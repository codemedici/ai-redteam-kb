---
id: collection
title: "AML.TA0009: Collection"
sidebar_label: "Collection"
sidebar_position: 12
---

# AML.TA0009: Collection

The adversary is trying to gather AI artifacts and other related information relevant to their goal.

Collection consists of techniques adversaries may use to gather information and the sources information is collected from that are relevant to following through on the adversary's objectives.
Frequently, the next goal after collecting data is to steal (exfiltrate) the AI artifacts, or use the collected information to stage future operations.
Common target sources include software repositories, container registries, model repositories, and object stores.

## Metadata

- **Tactic ID:** AML.TA0009
- **Created:** January 24, 2022
- **Last Modified:** April 9, 2025
- **MITRE ATT&CK Reference:** [TA0009](https://attack.mitre.org/tactics/TA0009/)

## Techniques (4)

The following techniques can be used to achieve this tactic:


| Technique ID | Name | Maturity |
|---|---|---|
| [[atlas/techniques/collection/ai-artifact-collection|AML.T0035]] | AI Artifact Collection | realized |
| [[atlas/techniques/collection/data-from-information-repositories|AML.T0036]] | Data from Information Repositories | realized |
| [[atlas/techniques/collection/data-from-local-system|AML.T0037]] | Data from Local System | realized |
| [[atlas/techniques/collection/data-from-ai-services/data-from-ai-services-overview|AML.T0085]] | Data from AI Services | demonstrated |


## Case Studies (10)


The following case studies demonstrate this tactic:

- [[atlas/case-studies/clearviewai-misconfiguration|AML.CS0006: ClearviewAI Misconfiguration]]
- [[atlas/case-studies/microsoft-azure-service-disruption|AML.CS0010: Microsoft Azure Service Disruption]]
- [[atlas/case-studies/compromised-pytorch-dependency-chain|AML.CS0015: Compromised PyTorch Dependency Chain]]
- [[atlas/case-studies/arbitrary-code-execution-with-google-colab|AML.CS0018: Arbitrary Code Execution with Google Colab]]
- [[atlas/case-studies/shadowray|AML.CS0023: ShadowRay]]
- [[atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio]]
- [[atlas/case-studies/planting-instructions-for-delayed-automatic-ai-agent-tool-invocation|AML.CS0038: Planting Instructions for Delayed Automatic AI Agent Tool Invocation]]
- [[atlas/case-studies/living-off-ai-prompt-injection-via-jira-service-management|AML.CS0039: Living Off AI: Prompt Injection via Jira Service Management]]
- [[atlas/case-studies/malware-prototype-with-embedded-prompt-injection|AML.CS0043: Malware Prototype with Embedded Prompt Injection]]
- [[atlas/case-studies/lamehug-malware-leveraging-dynamic-ai-generated-commands|AML.CS0044: LAMEHUG: Malware Leveraging Dynamic AI-Generated Commands]]


## References

MITRE Corporation. *Collection (AML.TA0009)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0009
