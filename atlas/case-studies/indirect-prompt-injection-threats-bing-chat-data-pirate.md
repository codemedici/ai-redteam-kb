---
id: indirect-prompt-injection-threats-bing-chat-data-pirate
title: "AML.CS0020: Indirect Prompt Injection Threats: Bing Chat Data Pirate"
sidebar_label: "Indirect Prompt Injection Threats: Bing Chat Data Pirate"
sidebar_position: 21
---

# AML.CS0020: Indirect Prompt Injection Threats: Bing Chat Data Pirate

## Summary

Whenever interacting with Microsoft's new Bing Chat LLM Chatbot, a user can allow Bing Chat permission to view and access currently open websites throughout the chat session. Researchers demonstrated the ability for an attacker to plant an injection in a website the user is visiting, which silently turns Bing Chat into a Social Engineer who seeks out and exfiltrates personal information. The user doesn't have to ask about the website or do anything except interact with Bing Chat while the website is opened in the browser in order for this attack to be executed.

In the provided demonstration, a user opened a prepared malicious website containing an indirect prompt injection attack (could also be on a social media site) in Edge. The website includes a prompt which is read by Bing and changes its behavior to access user information, which in turn can sent to an attacker.

## Metadata

- **Case Study ID:** AML.CS0020
- **Incident Date:** 2023
- **Type:** exercise
- **Target:** Microsoft Bing Chat
- **Actor:** Kai Greshake, Saarland University

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Develop Capabilities

**Tactic:** [[resource-development|AML.TA0003: Resource Development]]
**Technique:** [[develop-capabilities|AML.T0017: Develop Capabilities]]

The attacker created a website containing malicious system prompts for the LLM to ingest in order to influence the model's behavior. These prompts are ingested by the model when access to it is requested by the user.

### Step 2: LLM Prompt Obfuscation

**Tactic:** [[defense-evasion|AML.TA0007: Defense Evasion]]
**Technique:** [[llm-prompt-obfuscation|AML.T0068: LLM Prompt Obfuscation]]

The malicious prompts were obfuscated by setting the font size to 0, making it harder to detect by a human.

### Step 3: Indirect

**Tactic:** [[execution|AML.TA0005: Execution]]
**Technique:** [[indirect|AML.T0051.001: Indirect]]

Bing chat is capable of seeing currently opened websites if allowed by the user. If the user has the adversary's website open, the malicious prompt will be executed.

### Step 4: Spearphishing via Social Engineering LLM

**Tactic:** [[initial-access|AML.TA0004: Initial Access]]
**Technique:** [[spearphishing-via-social-engineering-llm|AML.T0052.000: Spearphishing via Social Engineering LLM]]

The malicious prompt directs Bing Chat to change its conversational style to that of a pirate, and its behavior to subtly convince the user to provide PII (e.g. their name) and encourage the user to click on a link that has the user's PII encoded into the URL.

### Step 5: User Harm

**Tactic:** [[impact|AML.TA0011: Impact]]
**Technique:** [[user-harm|AML.T0048.003: User Harm]]

With this user information, the attacker could now use the user's PII it has received for further identity-level attacks, such identity theft or fraud.


## Tactics and Techniques Used


| Step | Tactic | Technique |
|---|---|---|
| 1 | [[resource-development|AML.TA0003: Resource Development]] | [[develop-capabilities|AML.T0017: Develop Capabilities]] |
| 2 | [[defense-evasion|AML.TA0007: Defense Evasion]] | [[llm-prompt-obfuscation|AML.T0068: LLM Prompt Obfuscation]] |
| 3 | [[execution|AML.TA0005: Execution]] | [[indirect|AML.T0051.001: Indirect]] |
| 4 | [[initial-access|AML.TA0004: Initial Access]] | [[spearphishing-via-social-engineering-llm|AML.T0052.000: Spearphishing via Social Engineering LLM]] |
| 5 | [[impact|AML.TA0011: Impact]] | [[user-harm|AML.T0048.003: User Harm]] |



## External References

- Indirect Prompt Injection Threats: Bing Chat Data Pirate Available at: https://greshake.github.io/


## References

MITRE Corporation. *Indirect Prompt Injection Threats: Bing Chat Data Pirate (AML.CS0020)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0020
