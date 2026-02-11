---
id: credentials-from-ai-agent-configuration
title: "AML.T0083: Credentials from AI Agent Configuration"
sidebar_label: "Credentials from AI Agent Configuration"
sidebar_position: 3
---

# AML.T0083: Credentials from AI Agent Configuration



Adversaries may access the credentials of other tools or services on a system from the configuration of an AI agent.

AI Agents often utilize external tools or services to take actions, such as querying databases, invoking APIs, or interacting with cloud resources. To enable these functions, credentials like API keys, tokens, and connection strings are frequently stored in configuration files. While there are secure methods such as dedicated secret managers or encrypted vaults that can be deployed to store and manage these credentials, in practice they are often placed in less protected locations for convenience or ease of deployment. If an attacker can read or extract these configurations, they may obtain valid credentials that allow direct access to sensitive systems outside the agent itself.

## Metadata

- **Technique ID:** AML.T0083
- **Created:** September 30, 2025
- **Last Modified:** October 13, 2025
- **Maturity:** feasible



## Tactics (1)

This technique supports the following tactics:


- [[credential-access|AML.TA0013: Credential Access]]




## Case Studies (0)

*No case studies currently documented for this technique.*

## References

MITRE Corporation. *Credentials from AI Agent Configuration (AML.T0083)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0083
