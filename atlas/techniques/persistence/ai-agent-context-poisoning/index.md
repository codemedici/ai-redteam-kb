---
id: ai-agent-context-poisoning
title: "AML.T0080: AI Agent Context Poisoning"
sidebar_label: "AI Agent Context Poisoning"
sidebar_position: 7
---

# AML.T0080: AI Agent Context Poisoning



Adversaries may attempt to manipulate the context used by an AI agent's large language model (LLM) to influence the responses it generates or actions it takes. This allows an adversary to persistently change the behavior of the target agent and further their goals.

Context poisoning can be accomplished by prompting the an LLM to add instructions or preferences to memory (See [[memory|Memory]]) or by simply prompting an LLM that uses prior messages in a thread as part of its context (See [[thread|Thread]]).

## Metadata

- **Technique ID:** AML.T0080
- **Created:** September 30, 2025
- **Last Modified:** October 13, 2025
- **Maturity:** demonstrated



## Tactics (1)

This technique supports the following tactics:


- [[persistence|AML.TA0006: Persistence]]



## Sub-Techniques (2)

- [[memory|AML.T0080.000: Memory]]
- [[thread|AML.T0080.001: Thread]]


## Case Studies (0)

*No case studies currently documented for this technique.*

## References

MITRE Corporation. *AI Agent Context Poisoning (AML.T0080)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0080
