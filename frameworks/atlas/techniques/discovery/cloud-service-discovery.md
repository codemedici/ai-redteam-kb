---
id: cloud-service-discovery
title: "AML.T0075: Cloud Service Discovery"
sidebar_label: "Cloud Service Discovery"
sidebar_position: 76
---

# AML.T0075: Cloud Service Discovery

Adversaries may attempt to enumerate the cloud services running on a system after gaining access. These methods can differ from platform-as-a-service (PaaS), to infrastructure-as-a-service (IaaS), software-as-a-service (SaaS), or AI-as-a-service (AIaaS). Many services exist throughout the various cloud providers and can include Continuous Integration and Continuous Delivery (CI/CD), Lambda Functions, Entra ID, AI Inference, Generative AI, Agentic AI, etc. They may also include security services, such as AWS GuardDuty and Microsoft Defender for Cloud, and logging services, such as AWS CloudTrail and Google Cloud Audit Logs.

Adversaries may attempt to discover information about the services enabled throughout the environment. Azure tools and APIs, such as the Microsoft Graph API and Azure Resource Manager API, can enumerate resources and services, including applications, management groups, resources and policy definitions, and their relationships that are accessible by an identity. They may use tools to check credentials and enumerate the AI models available in various AIaaS providers' environments including AI21 Labs, Anthropic, AWS Bedrock, Azure, ElevenLabs, MakerSuite, Mistral, OpenAI, OpenRouter, and GCP Vertex AI [\[1\]][1].

[1]: https://www.sysdig.com/blog/llmjacking-stolen-cloud-credentials-used-in-new-ai-attack

## Metadata

- **Technique ID:** AML.T0075
- **Created:** 2025-04-14
- **Last Modified:** 2025-12-24
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1526](https://attack.mitre.org/techniques/T1526/)

## Tactics (1)

- [[frameworks/atlas/tactics/discovery|Discovery]]

## Case Studies (1)

- [[frameworks/atlas/case-studies/llm-jacking|LLM Jacking]]
