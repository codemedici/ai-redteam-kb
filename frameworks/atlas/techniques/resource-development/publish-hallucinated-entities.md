---
id: publish-hallucinated-entities
title: "AML.T0060: Publish Hallucinated Entities"
sidebar_label: "Publish Hallucinated Entities"
sidebar_position: 61
---

# AML.T0060: Publish Hallucinated Entities

Adversaries may create an entity they control, such as a software package, website, or email address to a source hallucinated by an LLM. The hallucinations may take the form of package names commands, URLs, company names, or email addresses that point the victim to the entity controlled by the adversary. When the victim interacts with the adversary-controlled entity, the attack can proceed.

## Metadata

- **Technique ID:** AML.T0060
- **Created:** 2025-03-12
- **Last Modified:** 2025-10-31
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/resource-development|Resource Development]]

## Case Studies (1)

- [[frameworks/atlas/case-studies/chatgpt-package-hallucination|ChatGPT Package Hallucination]]
