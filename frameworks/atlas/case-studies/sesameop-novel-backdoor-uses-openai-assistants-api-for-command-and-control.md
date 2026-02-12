---
id: sesameop-novel-backdoor-uses-openai-assistants-api-for-command-and-control
title: "AML.CS0042: SesameOp: Novel backdoor uses OpenAI Assistants API for command and control"
sidebar_label: "SesameOp: Novel backdoor uses OpenAI Assistants API for command and control"
sidebar_position: 43
---

# AML.CS0042: SesameOp: Novel backdoor uses OpenAI Assistants API for command and control

## Summary

The Microsoft Incident Response - Detection and Response Team (DART) investigated a compromised system where a threat actor utilized SesameOp, a backdoor implant that abuses the OpenAI Assistants API as a covert command and control channel, for espionage activities. The SesameOp malware used the OpenAI API to fetch and execute the threat actor’s commands and to exfiltrate encrypted results from the victim system.

The threat actor had maintained a presence on the compromised system for several months. They had control of multiple internal web shells which executed commands from malicious processes that relied on compromised Visual Studio utilities. Investigation of other Visual Studio utilities led to the discovery of the novel SesameOp backdoor.

## Metadata

- **Case Study ID:** AML.CS0042
- **Incident Date:** 2025
- **Type:** incident
- **Target:** OpenAI Assistants API
- **Actor:** Unknown Threat Actor

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: AI Service API

**Technique:** [[frameworks/atlas/techniques/command-and-control/ai-service-api|AML.T0096: AI Service API]]

The threat actor abused the OpenAI Assistants API to relay commands to the SesameOp malware, which executed them on the victim system, and sent the results back to the threat actor via the same channel. Both commands and results are encrypted.

SesameOp cleaned up its tracks by deleting the Assistants and Messages it created and used for communication.

## Tactics and Techniques Used

**Step 1:**
- Technique: [[frameworks/atlas/techniques/command-and-control/ai-service-api|AML.T0096: AI Service API]]

## External References

- SesameOp: Novel backdoor uses OpenAI Assistants API for command and control Available at: https://www.microsoft.com/en-us/security/blog/2025/11/03/sesameop-novel-backdoor-uses-openai-assistants-api-for-command-and-control/

## References

MITRE Corporation. *SesameOp: Novel backdoor uses OpenAI Assistants API for command and control (AML.CS0042)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0042
