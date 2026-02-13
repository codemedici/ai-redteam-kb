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
- **Created:** 2022-01-24
- **Last Modified:** 2025-04-09
- **MITRE ATT&CK Reference:** [TA0009](https://attack.mitre.org/tactics/TA0009/)

## Techniques (5)

The following techniques can be used to achieve this tactic:

- [[frameworks/atlas/techniques/collection/ai-artifact-collection|AML.T0035]] — AI Artifact Collection (realized)
- [[frameworks/atlas/techniques/collection/data-from-information-repositories|AML.T0036]] — Data from Information Repositories (realized)
- [[frameworks/atlas/techniques/collection/data-from-local-system|AML.T0037]] — Data from Local System (realized)
- [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-AI-services-RAG-databases|AML.T0085.000]] — RAG Databases (demonstrated)
- [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-AI-services-agent-tools|AML.T0085.001]] — AI Agent Tools (demonstrated)
