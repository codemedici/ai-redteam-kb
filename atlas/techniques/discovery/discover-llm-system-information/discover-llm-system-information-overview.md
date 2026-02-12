---
id: discover-llm-system-information
title: "AML.T0069: Discover LLM System Information"
sidebar_label: "Discover LLM System Information"
sidebar_position: 6
---

# AML.T0069: Discover LLM System Information



The adversary is trying to discover something about the large language model's (LLM) system information. This may be found in a configuration file containing the system instructions or extracted via interactions with the LLM. The desired information may include the full system prompt, special characters that have significance to the LLM or keywords indicating functionality available to the LLM. Information about how the LLM is instructed can be used by the adversary to understand the system's capabilities and to aid them in crafting malicious prompts.

## Metadata

- **Technique ID:** AML.T0069
- **Created:** March 12, 2025
- **Last Modified:** March 12, 2025
- **Maturity:** demonstrated



## Tactics (1)

This technique supports the following tactics:


- [[atlas/tactics/discovery|AML.TA0008: Discovery]]



## Sub-Techniques (3)

- [[atlas/techniques/discovery/discover-llm-system-information/special-character-sets|AML.T0069.000: Special Character Sets]]
- [[atlas/techniques/discovery/discover-llm-system-information/system-instruction-keywords|AML.T0069.001: System Instruction Keywords]]
- [[atlas/techniques/discovery/discover-llm-system-information/system-prompt|AML.T0069.002: System Prompt]]


## Case Studies (0)

*No case studies currently documented for this technique.*

## References

MITRE Corporation. *Discover LLM System Information (AML.T0069)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0069
