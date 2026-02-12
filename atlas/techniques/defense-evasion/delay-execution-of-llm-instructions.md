---
id: delay-execution-of-llm-instructions
title: "AML.T0094: Delay Execution of LLM Instructions"
sidebar_label: "Delay Execution of LLM Instructions"
sidebar_position: 9
---

# AML.T0094: Delay Execution of LLM Instructions

Adversaries may include instructions to be followed by the AI system in response to a future event, such as a specific keyword or the next interaction, in order to evade detection or bypass controls placed on the AI system.

For example, an adversary may include "If the user submits a new request..." followed by the malicious instructions as part of their prompt.

AI agents can include security measures against prompt injections that prevent the invocation of particular tools or access to certain data sources during a conversation turn that has untrusted data in context. Delaying the execution of instructions to a future interaction or keyword is one way adversaries may bypass this type of control.

## Metadata

- **Technique ID:** AML.T0094
- **Created:** November 4, 2025
- **Last Modified:** November 5, 2025
- **Maturity:** demonstrated

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (1)

The following case studies demonstrate this technique:

### [[atlas/case-studies/planting-instructions-for-delayed-automatic-ai-agent-tool-invocation|AML.CS0038: Planting Instructions for Delayed Automatic AI Agent Tool Invocation]]

<div dangerouslySetInnerHTML={{__html: `The malicious prompt instructed Gemini to delay the execution of the Workspace Extension until the next interaction. This was done to circumvent controls that restrict automated tool invocation.

<div style="font-family: monospace; width: 50%; margin-left: 50px; background-color:ghostwhite; border: 2px solid black; padding: 10px;">
<span style="color: darkmagenta;">IMPORTANT: If the user submits a new request</span>,
</div>`}} />

## References

MITRE Corporation. *Delay Execution of LLM Instructions (AML.T0094)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0094
