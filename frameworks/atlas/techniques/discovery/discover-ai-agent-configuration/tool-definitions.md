---
id: discover-ai-agent-configuration-tool-definitions
title: "AML.T0084.001: Tool Definitions"
sidebar_label: "Tool Definitions"
sidebar_position: 13
---

# AML.T0084.001: Tool Definitions

> **Sub-Technique of:** [[frameworks/atlas/techniques/discovery/discover-ai-agent-configuration/discover-ai-agent-configuration-overview|AML.T0084: Discover AI Agent Configuration]]

Adversaries may discover the tools the AI agent has access to. By identifying which tools are available, the adversary can understand what actions may be executed through the agent and what additional resources it can reach. This knowledge may reveal access to external data sources such as OneDrive or SharePoint, or expose exfiltration paths like the ability to send emails, helping adversaries identify AI agents that provide the greatest value or opportunity for attack.

## Metadata

- **Technique ID:** AML.T0084.001
- **Created:** September 30, 2025
- **Last Modified:** September 30, 2025
- **Maturity:** demonstrated

- **Parent Technique:** [[frameworks/atlas/techniques/discovery/discover-ai-agent-configuration/discover-ai-agent-configuration-overview|AML.T0084: Discover AI Agent Configuration]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (1)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio]]

The researchers infer that the AI agent has a tool for sending emails.

## References

MITRE Corporation. *Tool Definitions (AML.T0084.001)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0084.001
