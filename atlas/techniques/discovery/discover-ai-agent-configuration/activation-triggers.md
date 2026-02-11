---
id: discover-ai-agent-configuration-activation-triggers
title: "AML.T0084.002: Activation Triggers"
sidebar_label: "Activation Triggers"
sidebar_position: 14
---

# AML.T0084.002: Activation Triggers

> **Sub-Technique of:** [[discover-ai-agent-configuration|AML.T0084: Discover AI Agent Configuration]]



Adversaries may discover keywords or other triggers (such as incoming emails, documents being added, incoming message, or other workflows) that activate an agent and may cause it to run additional actions.

Understanding these triggers can reveal how the AI agent is activated and controlled. This may also expose additional paths for compromise, as an adversary could attempt to trigger the agent from outside its environment and drive it to perform unintended or malicious actions.

## Metadata

- **Technique ID:** AML.T0084.002
- **Created:** September 30, 2025
- **Last Modified:** September 30, 2025
- **Maturity:** demonstrated

- **Parent Technique:** [[discover-ai-agent-configuration|AML.T0084: Discover AI Agent Configuration]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*



## Case Studies (1)


The following case studies demonstrate this technique:

### [[data-exfiltration-via-agent-tools-in-copilot-studio|AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio]]

The researchers infer that the AI agent is activated when receiving an email.



## References

MITRE Corporation. *Activation Triggers (AML.T0084.002)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0084.002
