---
id: data-from-AI-services-RAG-databases
title: "AML.T0085.000: RAG Databases"
sidebar_label: "RAG Databases"
sidebar_position: 85001
---

# AML.T0085.000: RAG Databases

Adversaries may prompt the AI service to retrieve data from a RAG database. This can include the majority of an organization's internal documents.

## Metadata

- **Technique ID:** AML.T0085.000
- **Created:** 2025-09-30
- **Last Modified:** 2025-09-30
- **Maturity:** demonstrated

## Parent Technique

**Parent Technique:** AML.T0085 — Data from AI Services

## Tactics (1)

- [[frameworks/atlas/tactics/collection|Collection]]

## Mitigations (4)

- [[frameworks/atlas/mitigations/ai-telemetry-logging|AML.M0024: AI Telemetry Logging]] — Log requests to AI services to detect malicious queries for data.
- [[frameworks/atlas/mitigations/privileged-ai-agent-permissions-configuration|AML.M0026: Privileged AI Agent Permissions Configuration]] — Configuring privileged AI agents with proper access controls can limit an adversary's ability to collect data from RAG Databases if the agent is compromised.
- [[frameworks/atlas/mitigations/single-user-ai-agent-permissions-configuration|AML.M0027: Single-User AI Agent Permissions Configuration]] — Configuring AI agents with permissions that are inherited from the user can limit an adversary's ability to collect data from RAG Databases if the agent is compromised.
- [[frameworks/atlas/mitigations/segmentation-of-ai-agent-components|AML.M0032: Segmentation of AI Agent Components]] — Segmentation can prevent adversaries from utilizing tools in an agentic workflow to collect sensitive data from RAG databases.

## Case Studies (1)

- [[frameworks/atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|Data Exfiltration via Agent Tools in Copilot Studio]]
