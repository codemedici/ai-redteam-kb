---
id: ai-service-api
title: "AML.T0096: AI Service API"
sidebar_label: "AI Service API"
sidebar_position: 2
---

# AML.T0096: AI Service API



Adversaries may communicate using the API of an AI service on the victim's system. The adversary's commands to the victim system, and often the results, are embedded in the normal traffic of the AI service.

An AI service API command and control channel is covert because the adversary's commands blend in with normal communications, so an adversary may use this technique to avoid detection. Using existing infrastructure on the victim's system allows the adversary to live off the land, further reducing their footprint.

AI service APIs may be abused as C2 channels when an adversary wants to be stealthy and maintain long-term persistence for espionage activities [\[1\]][1].

[1]: https://www.microsoft.com/en-us/security/blog/2025/11/03/sesameop-novel-backdoor-uses-openai-assistants-api-for-command-and-control/

## Metadata

- **Technique ID:** AML.T0096
- **Created:** December 24, 2025
- **Last Modified:** December 23, 2025
- **Maturity:** realized



## Tactics (1)

This technique supports the following tactics:


- [[atlas/tactics/command-and-control|AML.TA0014: Command and Control]]




## Case Studies (1)


The following case studies demonstrate this technique:

### [[atlas/case-studies/sesameop-novel-backdoor-uses-openai-assistants-api-for-command-and-control|AML.CS0042: SesameOp: Novel backdoor uses OpenAI Assistants API for command and control]]

The threat actor abused the OpenAI Assistants API to relay commands to the SesameOp malware, which executed them on the victim system, and sent the results back to the threat actor via the same channel. Both commands and results are encrypted.

SesameOp cleaned up its tracks by deleting the Assistants and Messages it created and used for communication.



## References

MITRE Corporation. *AI Service API (AML.T0096)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0096
