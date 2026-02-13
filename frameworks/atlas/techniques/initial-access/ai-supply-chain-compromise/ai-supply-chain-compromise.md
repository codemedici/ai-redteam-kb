---
id: ai-supply-chain-compromise
title: "AML.T0010: AI Supply Chain Compromise"
sidebar_label: "AI Supply Chain Compromise"
sidebar_position: 1
---

# AML.T0010: AI Supply Chain Compromise



Adversaries may gain initial access to a system by compromising the unique portions of the AI supply chain.
This could include [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-hardware|Hardware]], [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-data|Data]] and its annotations, parts of the AI [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-AI-software|AI Software]] stack, or the [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-model|Model]] itself.
In some instances the attacker will need secondary access to fully carry out an attack using compromised components of the supply chain.

## Metadata

- **Technique ID:** AML.T0010
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** realized



## Tactics (1)

This technique supports the following tactics:


- [[frameworks/atlas/tactics/initial-access|AML.TA0004: Initial Access]]



## Sub-Techniques (5)

- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-hardware|AML.T0010.000: Hardware]]
- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-AI-software|AML.T0010.001: AI Software]]
- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-data|AML.T0010.002: Data]]
- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-model|AML.T0010.003: Model]]
- [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-container-registry|AML.T0010.004: Container Registry]]


## Case Studies (1)


The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/malicious-models-on-hugging-face|AML.CS0031: Malicious Models on Hugging Face]]

Because the models were successfully uploaded to Hugging Face, a user relying on this model repository would have their supply chain compromised.



## References

MITRE Corporation. *AI Supply Chain Compromise (AML.T0010)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0010
