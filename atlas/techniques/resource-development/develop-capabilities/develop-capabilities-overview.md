---
id: develop-capabilities
title: "AML.T0017: Develop Capabilities"
sidebar_label: "Develop Capabilities"
sidebar_position: 7
---

# AML.T0017: Develop Capabilities



Adversaries may develop their own capabilities to support operations. This process encompasses identifying requirements, building solutions, and deploying capabilities. Capabilities used to support attacks on AI-enabled systems are not necessarily AI-based themselves. Examples include setting up websites with adversarial information or creating Jupyter notebooks with obfuscated exfiltration code.

## Metadata

- **Technique ID:** AML.T0017
- **Created:** October 25, 2023
- **Last Modified:** April 9, 2025
- **Maturity:** realized
- **MITRE ATT&CK Reference:** [T1587](https://attack.mitre.org/techniques/T1587/)


## Tactics (1)

This technique supports the following tactics:


- [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]



## Sub-Techniques (1)

- [[atlas/techniques/resource-development/develop-capabilities/adversarial-ai-attacks|AML.T0017.000: Adversarial AI Attacks]]


## Case Studies (4)


The following case studies demonstrate this technique:

### [[atlas/case-studies/arbitrary-code-execution-with-google-colab|AML.CS0018: Arbitrary Code Execution with Google Colab]]

An adversary creates a Jupyter notebook containing obfuscated, malicious code.

### [[atlas/case-studies/indirect-prompt-injection-threats-bing-chat-data-pirate|AML.CS0020: Indirect Prompt Injection Threats: Bing Chat Data Pirate]]

The attacker created a website containing malicious system prompts for the LLM to ingest in order to influence the model's behavior. These prompts are ingested by the model when access to it is requested by the user.

### [[atlas/case-studies/google-bard-conversation-exfiltration|AML.CS0029: Google Bard Conversation Exfiltration]]

The researcher wrote a Google Apps Script that logs all query parameters to a Google Doc.

### [[atlas/case-studies/malware-prototype-with-embedded-prompt-injection|AML.CS0043: Malware Prototype with Embedded Prompt Injection]]

The threat actor embedded the prompt injection into a malware sample they called Skynet.



## References

MITRE Corporation. *Develop Capabilities (AML.T0017)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0017
