---
id: credentials-from-ai-agent-configuration
title: "AML.T0083: Credentials from AI Agent Configuration"
sidebar_label: "Credentials from AI Agent Configuration"
sidebar_position: 84
---

# AML.T0083: Credentials from AI Agent Configuration

Adversaries may access the credentials of other tools or services on a system from the configuration of an AI agent.

AI Agents often utilize external tools or services to take actions, such as querying databases, invoking APIs, or interacting with cloud resources. To enable these functions, credentials like API keys, tokens, and connection strings are frequently stored in configuration files. While there are secure methods such as dedicated secret managers or encrypted vaults that can be deployed to store and manage these credentials, in practice they are often placed in less protected locations for convenience or ease of deployment. If an attacker can read or extract these configurations, they may obtain valid credentials that allow direct access to sensitive systems outside the agent itself.

## Metadata

- **Technique ID:** AML.T0083
- **Created:** 2025-09-30
- **Last Modified:** 2025-10-13
- **Maturity:** demonstrated

## Tactics (1)

- [[frameworks/atlas/tactics/credential-access|Credential Access]]

## Case Studies (2)

- [[frameworks/atlas/case-studies/data-exfiltration-via-an-mcp-server-used-by-cursor|Data Exfiltration via an MCP Server used by Cursor]]
- [[frameworks/atlas/case-studies/exposed-clawdbot-control-interfaces-leads-to-credential-access-and-execution|Exposed ClawdBot Control Interfaces Leads to Credential Access and Execution]]
