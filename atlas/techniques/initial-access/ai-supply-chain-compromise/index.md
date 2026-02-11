---
id: ai-supply-chain-compromise
title: "AML.T0010: AI Supply Chain Compromise"
sidebar_label: "AI Supply Chain Compromise"
sidebar_position: 1
---

# AML.T0010: AI Supply Chain Compromise



Adversaries may gain initial access to a system by compromising the unique portions of the AI supply chain.
This could include [[hardware|Hardware]], [[data|Data]] and its annotations, parts of the AI [[ai-software|AI Software]] stack, or the [[model|Model]] itself.
In some instances the attacker will need secondary access to fully carry out an attack using compromised components of the supply chain.

## Metadata

- **Technique ID:** AML.T0010
- **Created:** May 13, 2021
- **Last Modified:** April 9, 2025
- **Maturity:** realized



## Tactics (1)

This technique supports the following tactics:


- [[initial-access|AML.TA0004: Initial Access]]



## Sub-Techniques (5)

- [[hardware|AML.T0010.000: Hardware]]
- [[ai-software|AML.T0010.001: AI Software]]
- [[data|AML.T0010.002: Data]]
- [[model|AML.T0010.003: Model]]
- [[container-registry|AML.T0010.004: Container Registry]]


## Case Studies (1)


The following case studies demonstrate this technique:

### [[malicious-models-on-hugging-face|AML.CS0031: Malicious Models on Hugging Face]]

Because the models were successfully uploaded to Hugging Face, a user relying on this model repository would have their supply chain compromised.



## References

MITRE Corporation. *AI Supply Chain Compromise (AML.T0010)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0010
