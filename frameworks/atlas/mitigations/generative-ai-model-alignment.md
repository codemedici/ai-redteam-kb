---
id: generative-ai-model-alignment
title: "AML.M0022: Generative AI Model Alignment"
sidebar_label: "Generative AI Model Alignment"
sidebar_position: 23
type: mitigation
---

# AML.M0022: Generative AI Model Alignment

When training or fine-tuning a generative AI model it is important to utilize techniques that improve model alignment with safety, security, and content policies.

The fine-tuning process can potentially remove built-in safety mechanisms in a generative AI model, but utilizing techniques such as Supervised Fine-Tuning, Reinforcement Learning from Human Feedback or AI Feedback, and Targeted Safety Context Distillation can improve the safety and alignment of the model.

## Metadata

- **Mitigation ID:** AML.M0022
- **Created:** 2025-03-12
- **Last Modified:** 2025-12-23
- **Category:** Technical - ML
- **ML Lifecycle:** ML Model Engineering, ML Model Evaluation, Deployment

## Techniques (7)

- [[frameworks/atlas/techniques/privilege-escalation/llm-jailbreak|AML.T0054: LLM Jailbreak]] — Model alignment can improve the parametric safety of a model by guiding it away from unsafe prompts and responses.
- [[frameworks/atlas/techniques/exfiltration/extract-llm-system-prompt|AML.T0056: Extract LLM System Prompt]] — Model alignment can improve the parametric safety of a model by guiding it away from unsafe prompts and responses.
- [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]] — Model alignment can improve the parametric safety of a model by guiding it away from unsafe prompts and responses.
- [[frameworks/atlas/techniques/execution/llm-prompt-injection|AML.T0051: LLM Prompt Injection]] — Model alignment can improve the parametric safety of a model by guiding it away from unsafe prompts and responses.
- [[frameworks/atlas/techniques/exfiltration/llm-data-leakage|AML.T0057: LLM Data Leakage]] — Model alignment can improve the parametric safety of a model by guiding it away from unsafe prompts and responses.
- [[frameworks/atlas/techniques/persistence/llm-prompt-self-replication|AML.T0061: LLM Prompt Self-Replication]] — Model alignment can increase the security of models to self replicating prompt attacks.
- [[frameworks/atlas/techniques/discovery/discover-llm-hallucinations|AML.T0062: Discover LLM Hallucinations]] — Model alignment can help steer the model away from hallucinated content.
