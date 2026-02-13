---
id: ai-service-api
title: "AML.T0096: AI Service API"
sidebar_label: "AI Service API"
sidebar_position: 97
---

# AML.T0096: AI Service API

Adversaries may communicate using the API of an AI service on the victim's system. The adversary's commands to the victim system, and often the results, are embedded in the normal traffic of the AI service.

An AI service API command and control channel is covert because the adversary's commands blend in with normal communications, so an adversary may use this technique to avoid detection. Using existing infrastructure on the victim's system allows the adversary to live off the land, further reducing their footprint.

AI service APIs may be abused as C2 channels when an adversary wants to be stealthy and maintain long-term persistence for espionage activities [\[1\]][1].

[1]: https://www.microsoft.com/en-us/security/blog/2025/11/03/sesameop-novel-backdoor-uses-openai-assistants-api-for-command-and-control/

## Metadata

- **Technique ID:** AML.T0096
- **Created:** 2025-12-24
- **Last Modified:** 2025-12-23
- **Maturity:** realized

## Tactics (1)

- [[frameworks/atlas/tactics/command-and-control|Command and Control]]

## Case Studies (1)

- [[frameworks/atlas/case-studies/sesameop-novel-backdoor-uses-openai-assistants-api-for-command-and-control|SesameOp: Novel backdoor uses OpenAI Assistants API for command and control]]
