---
id: execution
title: "AML.TA0005: Execution"
sidebar_label: "Execution"
sidebar_position: 5
---

# AML.TA0005: Execution

The adversary is trying to run malicious code embedded in AI artifacts or software.

Execution consists of techniques that result in adversary-controlled code running on a local or remote system.
Techniques that run malicious code are often paired with techniques from all other tactics to achieve broader goals, like exploring a network or stealing data.
For example, an adversary might use a remote access tool to run a PowerShell script that does [Remote System Discovery](https://attack.mitre.org/techniques/T1018/).

## Metadata

- **Tactic ID:** AML.TA0005
- **Created:** January 24, 2022
- **Last Modified:** April 9, 2025
- **MITRE ATT&CK Reference:** [TA0002](https://attack.mitre.org/tactics/TA0002/)

## Techniques (5)

The following techniques can be used to achieve this tactic:


- [[frameworks/atlas/techniques/execution/command-and-scripting-interpreter|AML.T0050]] — Command and Scripting Interpreter (feasible)
- [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053]] — AI Agent Tool Invocation (demonstrated)
- [[frameworks/atlas/techniques/execution/ai-agent-clickbait|AML.T0100]] — AI Agent Clickbait (feasible)


## References

MITRE Corporation. *Execution (AML.TA0005)*. MITRE ATLAS. Available at: https://atlas.mitre.org/tactics/AML.TA0005
