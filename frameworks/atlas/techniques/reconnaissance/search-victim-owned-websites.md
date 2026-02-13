---
id: search-victim-owned-websites
title: "AML.T0003: Search Victim-Owned Websites"
sidebar_label: "Search Victim-Owned Websites"
sidebar_position: 6
---

# AML.T0003: Search Victim-Owned Websites

Adversaries may search websites owned by the victim for information that can be used during targeting.
Victim-owned websites may contain technical details about their AI-enabled products or services.
Victim-owned websites may contain a variety of details, including names of departments/divisions, physical locations, and data about key employees such as names, roles, and contact info.
These sites may also have details highlighting business operations and relationships.

Adversaries may search victim-owned websites to gather actionable information.
This information may help adversaries tailor their attacks (e.g. [[frameworks/atlas/techniques/resource-development/develop-capabilities/develop-capabilities-adversarial-AI-attacks|Adversarial AI Attacks]] or [[frameworks/atlas/techniques/ai-attack-staging/craft-adversarial-data/craft-adversarial-data-manual-modification|Manual Modification]]).
Information from these sources may reveal opportunities for other forms of reconnaissance (e.g. Search Open Technical Databases or [[frameworks/atlas/techniques/reconnaissance/search-open-ai-vulnerability-analysis|Search Open AI Vulnerability Analysis]])

## Metadata

- **Technique ID:** AML.T0003
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** demonstrated
- **MITRE ATT&CK Reference:** [T1594](https://attack.mitre.org/techniques/T1594/)

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (2)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/confusing-antimalware-neural-networks|AML.CS0014: Confusing Antimalware Neural Networks]]

Kaspersky's use of ML-based antimalware detectors is publicly documented on their website. In practice, an adversary could use this for targeting.

### [[frameworks/atlas/case-studies/living-off-ai-prompt-injection-via-jira-service-management|AML.CS0039: Living Off AI: Prompt Injection via Jira Service Management]]

The researchers performed reconnaissance to learn about Atlassian’s Model Context Protocol (MCP) server and its integration into the Jira Service Management (JSM) platform. Atlassian offers an MCP server, which embeds AI into enterprise workflows. Their MCP enables a range of AI-driven actions, such as ticket summarization, auto-replies, classification, and smart recommendations across JSM and Confluence. It allows support engineers and internal users to interact with AI directly from their native interfaces.

## References

MITRE Corporation. *Search Victim-Owned Websites (AML.T0003)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0003
