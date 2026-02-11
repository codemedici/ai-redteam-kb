---
id: corrupt-ai-model
title: "AML.T0076: Corrupt AI Model"
sidebar_label: "Corrupt AI Model"
sidebar_position: 7
---

# AML.T0076: Corrupt AI Model



An adversary may purposefully corrupt a malicious AI model file so that it cannot be successfully deserialized in order to evade detection by a model scanner. The corrupt model may still successfully execute malicious code before deserialization fails.

## Metadata

- **Technique ID:** AML.T0076
- **Created:** April 14, 2025
- **Last Modified:** April 14, 2025
- **Maturity:** realized



## Tactics (1)

This technique supports the following tactics:


- [[defense-evasion|AML.TA0007: Defense Evasion]]




## Case Studies (1)


The following case studies demonstrate this technique:

### [[malicious-models-on-hugging-face|AML.CS0031: Malicious Models on Hugging Face]]

The adversary evaded detection by [Picklescan](https://github.com/mmaitre314/picklescan), which Hugging Face uses to flag malicious models. This occurred because the model could not be fully deserialized.

In their analysis, the ReversingLabs researchers found that the malicious payload was still executed.



## References

MITRE Corporation. *Corrupt AI Model (AML.T0076)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0076
