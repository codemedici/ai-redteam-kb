---
id: acquire-infrastructure
title: "AML.T0008: Acquire Infrastructure"
sidebar_label: "Acquire Infrastructure"
sidebar_position: 9
---

# AML.T0008: Acquire Infrastructure



Adversaries may buy, lease, or rent infrastructure for use throughout their operation.
A wide variety of infrastructure exists for hosting and orchestrating adversary operations.
Infrastructure solutions include physical or cloud servers, domains, mobile devices, and third-party web services.
Free resources may also be used, but they are typically limited.
Infrastructure can also include physical components such as countermeasures that degrade or disrupt AI components or sensors, including printed materials, wearables, or disguises.

Use of these infrastructure solutions allows an adversary to stage, launch, and execute an operation.
Solutions may help adversary operations blend in with traffic that is seen as normal, such as contact to third-party web services.
Depending on the implementation, adversaries may use infrastructure that makes it difficult to physically tie back to them as well as utilize infrastructure that can be rapidly provisioned, modified, and shut down.

## Metadata

- **Technique ID:** AML.T0008
- **Created:** May 13, 2021
- **Last Modified:** March 12, 2025
- **Maturity:** realized



## Tactics (1)

This technique supports the following tactics:


- [[frameworks/atlas/tactics/resource-development|AML.TA0003: Resource Development]]



## Sub-Techniques (5)

- [[frameworks/atlas/techniques/resource-development/acquire-infrastructure/ai-development-workspaces|AML.T0008.000: AI Development Workspaces]]
- [[frameworks/atlas/techniques/resource-development/acquire-infrastructure/consumer-hardware|AML.T0008.001: Consumer Hardware]]
- [[frameworks/atlas/techniques/resource-development/acquire-infrastructure/domains|AML.T0008.002: Domains]]
- [[frameworks/atlas/techniques/resource-development/acquire-infrastructure/physical-countermeasures|AML.T0008.003: Physical Countermeasures]]
- [[frameworks/atlas/techniques/resource-development/acquire-infrastructure/serverless|AML.T0008.004: Serverless]]


## Case Studies (1)


The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/google-bard-conversation-exfiltration|AML.CS0029: Google Bard Conversation Exfiltration]]

The researcher identified that Google Apps Scripts can be invoked via a URL on `script.google.com` or `googleusercontent.com` and can be configured to not require authentication. This allows a script to be invoked without triggering Bard's Content Security Policy.



## References

MITRE Corporation. *Acquire Infrastructure (AML.T0008)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0008
