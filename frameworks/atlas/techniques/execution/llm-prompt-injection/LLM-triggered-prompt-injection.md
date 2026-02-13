---
id: llm-prompt-injection-triggered
title: "AML.T0051.002: Triggered"
sidebar_label: "Triggered"
sidebar_position: 9
---

# AML.T0051.002: Triggered

An adversary may trigger a prompt injection via a user action or event that occurs within the victim's environment. Triggered prompt injections often target AI agents, which can be activated by means the adversary identifies during  (See [[frameworks/atlas/techniques/discovery/discover-ai-agent-configuration/discover-agent-config-activation-triggers|Activation Triggers]]). These malicious prompts may be hidden or obfuscated from the user and may already exist somewhere in the victim's environment from the adversary performing [[frameworks/atlas/techniques/initial-access/prompt-infiltration-via-public-facing-application|Prompt Infiltration via Public-Facing Application]]. This type of injection may be used by the adversary to gain a foothold in the system or to target an unwitting user of the system.

## Metadata

- **Technique ID:** AML.T0051.002
- **Created:** November 4, 2025
- **Last Modified:** November 5, 2025
- **Maturity:** demonstrated

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (2)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/morris-ii-worm-rag-based-attack|AML.CS0024: Morris II Worm: RAG-Based Attack]]

When the email containing the worm is retrieved by the email assistant in another reply generation task, the prompt injection changes the behavior of the GenAI email assistant.

### [[frameworks/atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio]]

The researchers receive a reply at the address they specified, indicating that there is an AI agent present, and that the triggered prompt injection was successful.

## References

MITRE Corporation. *Triggered (AML.T0051.002)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0051.002
