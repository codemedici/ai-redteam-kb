---
id: ai-agent-tool-credential-harvesting
title: "AML.T0098: AI Agent Tool Credential Harvesting"
sidebar_label: "AI Agent Tool Credential Harvesting"
sidebar_position: 99
---

# AML.T0098: AI Agent Tool Credential Harvesting

Adversaries may attempt to use their access to an AI agent on the victim's system to retrieve data from available agent tools to collect credentials. Agent tools may connect to a wide range of sources that may contain credentials including document stores (e.g. SharePoint, OneDrive or Google Drive), code repositories (e.g. GitHub or GitLab), or enterprise productivity tools (e.g. as email providers or Slack), and local notetaking tools (e.g. Obsidian or Apple Notes).

## Metadata

- **Technique ID:** AML.T0098
- **Created:** 2025-11-25
- **Last Modified:** 2025-12-19
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/credential-access|Credential Access]]

## Mitigations (1)

- [[frameworks/atlas/mitigations/segmentation-of-ai-agent-components|AML.M0032: Segmentation of AI Agent Components]] — Segmentation can prevent adversaries from utilizing tools in an agentic workflow to harvest credentials.

## Case Studies (1)

- [[frameworks/atlas/case-studies/exposed-clawdbot-control-interfaces-leads-to-credential-access-and-execution|Exposed ClawdBot Control Interfaces Leads to Credential Access and Execution]]
