---
id: llm-prompt-injection
title: "AML.T0051: LLM Prompt Injection"
sidebar_label: "LLM Prompt Injection"
sidebar_position: 4
---

# AML.T0051: LLM Prompt Injection



An adversary may craft malicious prompts as inputs to an LLM that cause the LLM to act in unintended ways.
These "prompt injections" are often designed to cause the model to ignore aspects of its original instructions and follow the adversary's instructions instead.

Prompt Injections can be an initial access vector to the LLM that provides the adversary with a foothold to carry out other steps in their operation.
They may be designed to bypass defenses in the LLM, or allow the adversary to issue privileged commands.
The effects of a prompt injection can persist throughout an interactive session with an LLM.

Malicious prompts may be injected directly by the adversary ([[frameworks/atlas/techniques/execution/LLM-direct-prompt-injection|Direct]]) either to leverage the LLM to generate harmful content or to gain a foothold on the system and lead to further effects.
Prompts may also be injected indirectly when as part of its normal operation the LLM ingests the malicious prompt from another data source ([[frameworks/atlas/techniques/execution/LLM-indirect-prompt-injection|Indirect]]). This type of injection can be used by the adversary to a foothold on the system or to target the user of the LLM.
Malicious prompts may also be [[frameworks/atlas/techniques/execution/LLM-triggered-prompt-injection|Triggered]] user actions or system events.

## Metadata

- **Technique ID:** AML.T0051
- **Created:** October 25, 2023
- **Last Modified:** November 5, 2025
- **Maturity:** realized



## Tactics (1)

This technique supports the following tactics:


- [[frameworks/atlas/tactics/execution|AML.TA0005: Execution]]



## Sub-Techniques (3)

- [[frameworks/atlas/techniques/execution/LLM-direct-prompt-injection|AML.T0051.000: Direct]]
- [[frameworks/atlas/techniques/execution/LLM-indirect-prompt-injection|AML.T0051.001: Indirect]]
- [[frameworks/atlas/techniques/execution/LLM-triggered-prompt-injection|AML.T0051.002: Triggered]]


## Case Studies (1)


The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/data-exfiltration-via-agent-tools-in-copilot-studio|AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio]]

The researchers modify the original prompt to discover other knowledge sources and tools that may have data they are after.



## References

MITRE Corporation. *LLM Prompt Injection (AML.T0051)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0051
