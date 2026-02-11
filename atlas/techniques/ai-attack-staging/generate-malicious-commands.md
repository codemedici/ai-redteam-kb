---
id: generate-malicious-commands
title: "AML.T0102: Generate Malicious Commands"
sidebar_label: "Generate Malicious Commands"
sidebar_position: 13
---

# AML.T0102: Generate Malicious Commands



Adversaries may use large language models (LLMs) to dynamically generate malicious commands from natural language. Dynamically generated commands may be harder detect as the attack signature is constantly changing. AI-generated commands may also allow adversaries to more rapidly adapt to different environments and adjust their tactics.

Adversaries may utilize LLMs present in the victim's environment or call out to externally hosted services. [APT28](https://attack.mitre.org/groups/G0007) utilized a model hosted on HuggingFace in a campaign with their LAMEHUG malware [\[1\]][1]. In either case prompts to generate malicious code can blend in with normal traffic.

[1]: https://logpoint.com/en/blog/apt28s-new-arsenal-lamehug-the-first-ai-powered-malware

## Metadata

- **Technique ID:** AML.T0102
- **Created:** November 25, 2025
- **Last Modified:** December 23, 2025
- **Maturity:** realized



## Tactics (1)

This technique supports the following tactics:


- [[ai-attack-staging|AML.TA0001: AI Attack Staging]]




## Case Studies (1)


The following case studies demonstrate this technique:

### [[lamehug-malware-leveraging-dynamic-ai-generated-commands|AML.CS0044: LAMEHUG: Malware Leveraging Dynamic AI-Generated Commands]]

The LAMEHUG malware abused the Qwen 2.5 Coder 32B Instruct model Hugging Face API to generate malicious commands from natural language prompts.



## References

MITRE Corporation. *Generate Malicious Commands (AML.T0102)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0102
