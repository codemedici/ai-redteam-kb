---
id: discover-llm-system-information-system-instruction-keywords
title: "AML.T0069.001: System Instruction Keywords"
sidebar_label: "System Instruction Keywords"
sidebar_position: 8
---

# AML.T0069.001: System Instruction Keywords

> **Sub-Technique of:** [[frameworks/atlas/techniques/discovery/discover-llm-system-information|AML.T0069: Discover LLM System Information]]

Adversaries may discover keywords that have special meaning to the large language model (LLM), such as function names or object names. These can later be exploited to confuse or manipulate the LLM into misbehaving and to make calls to plugins the LLM has access to.

## Metadata

- **Technique ID:** AML.T0069.001
- **Created:** March 12, 2025
- **Last Modified:** March 12, 2025
- **Maturity:** demonstrated

- **Parent Technique:** [[frameworks/atlas/techniques/discovery/discover-llm-system-information|AML.T0069: Discover LLM System Information]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (1)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|AML.CS0026: Financial Transaction Hijacking with M365 Copilot as an Insider]]

<div dangerouslySetInnerHTML={{__html: `By probing Copilot and examining its responses, the Zenity researchers identified plugins and specific functionality Copilot has access to. This included the <span style="font-family monospace; color: purple;">search_enterprise</span> function and <span style="font-family monospace; color: purple;">EmailMessage</span> object.`}} />

## References

MITRE Corporation. *System Instruction Keywords (AML.T0069.001)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0069.001
