---
id: tay-poisoning
title: "AML.CS0009: Tay Poisoning"
type: case-study
sidebar_position: 10
---

# AML.CS0009: Tay Poisoning

Microsoft created Tay, a Twitter chatbot designed to engage and entertain users.
While previous chatbots used pre-programmed scripts
to respond to prompts, Tay's machine learning capabilities allowed it to be
directly influenced by its conversations.

A coordinated attack encouraged malicious users to tweet abusive and offensive language at Tay,
which eventually led to Tay generating similarly inflammatory content towards other users.

Microsoft decommissioned Tay within 24 hours of its launch and issued a public apology
with lessons learned from the bot's failure.

## Metadata

- **ID:** AML.CS0009
- **Incident Date:** 2016-03-23
- **Type:** incident
- **Target:** Microsoft's Tay AI Chatbot
- **Actor:** 4chan Users

## Procedure

### Step 1: AI-Enabled Product or Service

**Technique:** [[frameworks/atlas/techniques/ai-model-access/ai-enabled-product-or-service|AML.T0047: AI-Enabled Product or Service]]

Adversaries were able to interact with Tay via Twitter messages.

### Step 2: Data

**Technique:** [[frameworks/atlas/techniques/initial-access/ai-supply-chain-compromise/supply-chain-compromise-AI-software|AML.T0010.002: Data]]

Tay bot used the interactions with its Twitter users as training data to improve its conversations.
Adversaries were able to coordinate with the intent of defacing Tay bot by exploiting this feedback loop.

### Step 3: Poison Training Data

**Technique:** [[frameworks/atlas/techniques/resource-development/poison-training-data|AML.T0020: Poison Training Data]]

By repeatedly interacting with Tay using racist and offensive language, they were able to skew Tay's dataset towards that language as well. This was done by adversaries using the "repeat after me" function, a command that forced Tay to repeat anything said to it.

### Step 4: Erode AI Model Integrity

**Technique:** [[frameworks/atlas/techniques/impact/erode-ai-model-integrity|AML.T0031: Erode AI Model Integrity]]

As a result of this coordinated attack, Tay's conversation algorithms began to learn to generate reprehensible material. Tay's internalization of this detestable language caused it to be unpromptedly repeated during interactions with innocent users.

## References

1. [AIID - Incident 6: TayBot](https://incidentdatabase.ai/cite/6)
2. [AVID - Vulnerability: AVID-2022-v013](https://avidml.org/database/avid-2022-v013/)
3. [Microsoft BlogPost, "Learning from Tay's introduction"](https://blogs.microsoft.com/blog/2016/03/25/learning-tays-introduction/)
4. [IEEE Article, "In 2016, Microsoft's Racist Chatbot Revealed the Dangers of Online Conversation"](https://spectrum.ieee.org/tech-talk/artificial-intelligence/machine-learning/in-2016-microsofts-racist-chatbot-revealed-the-dangers-of-online-conversation)
