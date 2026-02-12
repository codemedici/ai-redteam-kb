---
id: obtain-capabilities-generative-ai
title: "AML.T0016.002: Generative AI"
sidebar_label: "Generative AI"
sidebar_position: 19
---

# AML.T0016.002: Generative AI

> **Sub-Technique of:** [[frameworks/atlas/techniques/resource-development/obtain-capabilities/obtain-capabilities-overview|AML.T0016: Obtain Capabilities]]

Adversaries may search for and obtain generative AI models or tools, such as large language models (LLMs), to assist them in various steps of their operation. Generative AI can be used in a variety of malicious ways, such as to generating malware, to [[frameworks/atlas/techniques/ai-attack-staging/generate-deepfakes|Generate Deepfakes]], to [[frameworks/atlas/techniques/ai-attack-staging/generate-malicious-commands|Generate Malicious Commands]], for [[frameworks/atlas/techniques/resource-development/retrieval-content-crafting|Retrieval Content Crafting]], or to generate [[frameworks/atlas/techniques/initial-access/phishing/phishing-overview|Phishing]] content.

Adversaries may obtain open source models and serve them locally using frameworks such as [Ollama](https://ollama.com/) or [[latest|vLLM]]. They may host them using cloud infrastructure. Or, they may leverage AI service providers such as HuggingFace.

They may need to jailbreak the model (see [[frameworks/atlas/techniques/privilege-escalation/llm-jailbreak|LLM Jailbreak]]) to bypass any restrictions put in place to limit the types of responses it can generate. They may also need to break the terms of service of the model's developer.

Generative AI models may also be "uncensored" meaning they are designed to generate content without any restrictions such as guardrails or content filters. Uncensored GenAI is ripe for abuse by cybercriminals [\[1\]][1] [\[2\]][2]. Models may be fine-tuned to remove alignment and guardrails [\[3\]][3] or be subjected to targeted manipulations to bypass refusal [\[4\]][4] resulting in uncensored variants of the model. Uncensored models may be built for offensive and defensive cybersecurity [\[5\]][5], which can be abused by an adversary. There are also models that are expressly designed and advertised for malicious use [\[6\]][6].

[1]: https://blog.talosintelligence.com/cybercriminal-abuse-of-large-language-models/
[2]: https://gbhackers.com/cybercriminals-exploit-llm-models/
[3]: https://erichartford.com/uncensored-models
[4]: https://arxiv.org/abs/2406.11717/
[5]: https://taico.ca/posts/whiterabbitneo/
[6]: https://gbhackers.com/wormgpt-enhanced-with-grok-and-mixtral/

## Metadata

- **Technique ID:** AML.T0016.002
- **Created:** March 12, 2025
- **Last Modified:** December 23, 2025
- **Maturity:** realized

- **Parent Technique:** [[frameworks/atlas/techniques/resource-development/obtain-capabilities/obtain-capabilities-overview|AML.T0016: Obtain Capabilities]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (2)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/live-deepfake-image-injection-to-evade-mobile-kyc-verification|AML.CS0033: Live Deepfake Image Injection to Evade Mobile KYC Verification]]

The researchers obtained [Faceswap](https://swapface.org) a desktop application capable of swapping faces in a video in real-time.

### [[frameworks/atlas/case-studies/prokyc-deepfake-tool-for-account-fraud-attacks|AML.CS0034: ProKYC: Deepfake Tool for Account Fraud Attacks]]

The bad actor paid for the ProKYC tool, created a fake identity document, generated a deepfake selfie video, and replaced a live camera feed with the deepfake video.

## References

MITRE Corporation. *Generative AI (AML.T0016.002)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0016.002
