---
id: llm-prompt-self-replication
title: "AML.T0061: LLM Prompt Self-Replication"
sidebar_label: "LLM Prompt Self-Replication"
sidebar_position: 4
---

# AML.T0061: LLM Prompt Self-Replication

An adversary may use a carefully crafted [[atlas/techniques/execution/llm-prompt-injection/llm-prompt-injection-overview|LLM Prompt Injection]] designed to cause the LLM to replicate the prompt as part of its output. This allows the prompt to propagate to other LLMs and persist on the system. The self-replicating prompt is typically paired with other malicious instructions (ex: [[atlas/techniques/privilege-escalation/llm-jailbreak|LLM Jailbreak]], [[atlas/techniques/exfiltration/llm-data-leakage|LLM Data Leakage]]).

## Metadata

- **Technique ID:** AML.T0061
- **Created:** March 12, 2025
- **Last Modified:** March 12, 2025
- **Maturity:** demonstrated

## Tactics (1)

This technique supports the following tactics:

- 

## Case Studies (1)

The following case studies demonstrate this technique:

### [[atlas/case-studies/morris-ii-worm-rag-based-attack|AML.CS0024: Morris II Worm: RAG-Based Attack]]

The self-replicating portion of the prompt causes the generated output to contain the malicious prompt, allowing the worm to propagate.

## References

MITRE Corporation. *LLM Prompt Self-Replication (AML.T0061)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0061
