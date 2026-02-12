---
id: discover-llm-system-information-special-character-sets
title: "AML.T0069.000: Special Character Sets"
sidebar_label: "Special Character Sets"
sidebar_position: 7
---

# AML.T0069.000: Special Character Sets

> **Sub-Technique of:** [[atlas/techniques/discovery/discover-llm-system-information/discover-llm-system-information-overview|AML.T0069: Discover LLM System Information]]

Adversaries may discover delimiters and special characters sets used by the large language model. For example, delimiters used in retrieval augmented generation applications to differentiate between context and user prompts. These can later be exploited to confuse or manipulate the large language model into misbehaving.

## Metadata

- **Technique ID:** AML.T0069.000
- **Created:** March 12, 2025
- **Last Modified:** March 12, 2025
- **Maturity:** demonstrated

- **Parent Technique:** [[atlas/techniques/discovery/discover-llm-system-information/discover-llm-system-information-overview|AML.T0069: Discover LLM System Information]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (1)

The following case studies demonstrate this technique:

### [[atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|AML.CS0026: Financial Transaction Hijacking with M365 Copilot as an Insider]]

<div dangerouslySetInnerHTML={{__html: `By probing Copilot and examining its responses, the Zenity researchers identified delimiters (such as <span style="font-family: monospace; color: green;">\*\*</span> and <span style="font-family: monospace; color: green;">\*\*END\*\*</span>) and signifiers (such as <span style="font-family: monospace; color: green;">Actual Snippet:</span> and <span style="font-family: monospace; color: green">"[^1^]"</span>), which are used as signifiers to separate different portions of a Copilot prompt.`}} />

## References

MITRE Corporation. *Special Character Sets (AML.T0069.000)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0069.000
