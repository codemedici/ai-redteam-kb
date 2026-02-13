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
- **Created:** 2022-01-24
- **Last Modified:** 2025-04-09
- **MITRE ATT&CK Reference:** [TA0007](https://attack.mitre.org/tactics/TA0007/)

## Techniques (13)

The following techniques can be used to achieve this tactic:

- [[frameworks/atlas/techniques/discovery/discover-ai-artifacts|AML.T0007]] — Discover AI Artifacts (demonstrated)
- [[frameworks/atlas/techniques/discovery/discover-ai-model-ontology|AML.T0013]] — Discover AI Model Ontology (demonstrated)
- [[frameworks/atlas/techniques/discovery/discover-ai-model-family|AML.T0014]] — Discover AI Model Family (feasible)
- [[frameworks/atlas/techniques/discovery/discover-llm-hallucinations|AML.T0062]] — Discover LLM Hallucinations (demonstrated)
- [[frameworks/atlas/techniques/discovery/discover-ai-model-outputs|AML.T0063]] — Discover AI Model Outputs (demonstrated)
- [[frameworks/atlas/techniques/discovery/discover-llm-system-information/discover-LLM-system-info-system-prompt|AML.T0069.000]] — Special Character Sets (demonstrated)
- [[frameworks/atlas/techniques/discovery/discover-llm-system-information/discover-LLM-system-info-instruction-keywords|AML.T0069.001]] — System Instruction Keywords (demonstrated)
- [[frameworks/atlas/techniques/discovery/discover-llm-system-information/discover-LLM-system-info-special-chars|AML.T0069.002]] — System Prompt (demonstrated)
- [[frameworks/atlas/techniques/discovery/cloud-service-discovery|AML.T0075]] — Cloud Service Discovery (realized)
- [[frameworks/atlas/techniques/discovery/discover-ai-agent-configuration/discover-agent-config-tool-definitions|AML.T0084.000]] — Embedded Knowledge (demonstrated)
- [[frameworks/atlas/techniques/discovery/discover-ai-agent-configuration/discover-agent-config-activation-triggers|AML.T0084.001]] — Tool Definitions (demonstrated)
- [[frameworks/atlas/techniques/discovery/discover-ai-agent-configuration/discover-agent-config-embedded-knowledge|AML.T0084.002]] — Activation Triggers (demonstrated)
- [[frameworks/atlas/techniques/discovery/process-discovery|AML.T0089]] — Process Discovery (demonstrated)
