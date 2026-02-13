---
id: obtain-capabilities-generative-AI
title: "AML.T0016.002: Generative AI"
sidebar_label: "Generative AI"
sidebar_position: 16003
---

# AML.T0016.002: Generative AI

Adversaries may search for and obtain generative AI models or tools, such as large language models (LLMs), to assist them in various steps of their operation. Generative AI can be used in a variety of malicious ways, such as to generating malware, to [Generate Deepfakes](/techniques/AML.T0088), to [Generate Malicious Commands](/techniques/AML.T0102), for [Retrieval Content Crafting](/techniques/AML.T0066), or to generate [Phishing](/techniques/AML.T0052) content.

Adversaries may obtain open source models and serve them locally using frameworks such as [Ollama](https://ollama.com/) or [vLLM]( https://docs.vllm.ai/en/latest/). They may host them using cloud infrastructure. Or, they may leverage AI service providers such as HuggingFace.

They may need to jailbreak the model (see [LLM Jailbreak](/techniques/AML.T0054)) to bypass any restrictions put in place to limit the types of responses it can generate. They may also need to break the terms of service of the model's developer.

Generative AI models may also be "uncensored" meaning they are designed to generate content without any restrictions such as guardrails or content filters. Uncensored GenAI is ripe for abuse by cybercriminals [\[1\]][1] [\[2\]][2]. Models may be fine-tuned to remove alignment and guardrails [\[3\]][3] or be subjected to targeted manipulations to bypass refusal [\[4\]][4] resulting in uncensored variants of the model. Uncensored models may be built for offensive and defensive cybersecurity [\[5\]][5], which can be abused by an adversary. There are also models that are expressly designed and advertised for malicious use [\[6\]][6].

[1]: https://blog.talosintelligence.com/cybercriminal-abuse-of-large-language-models/
[2]: https://gbhackers.com/cybercriminals-exploit-llm-models/
[3]: https://erichartford.com/uncensored-models
[4]: https://arxiv.org/abs/2406.11717/
[5]: https://taico.ca/posts/whiterabbitneo/
[6]: https://gbhackers.com/wormgpt-enhanced-with-grok-and-mixtral/

## Metadata

- **Technique ID:** AML.T0016.002
- **Created:** 2025-03-12
- **Last Modified:** 2025-12-23
- **Maturity:** realized

## Parent Technique

**Parent Technique:** AML.T0016 — Obtain Capabilities

## Tactics (1)

- [[frameworks/atlas/tactics/resource-development|Resource Development]]

## Case Studies (2)

- [[frameworks/atlas/case-studies/live-deepfake-image-injection-to-evade-mobile-kyc-verification|Live Deepfake Image Injection to Evade Mobile KYC Verification]]
- [[frameworks/atlas/case-studies/prokyc-deepfake-tool-for-account-fraud-attacks|ProKYC: Deepfake Tool for Account Fraud Attacks]]
