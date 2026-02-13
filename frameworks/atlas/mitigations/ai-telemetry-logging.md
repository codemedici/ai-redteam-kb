---
id: ai-telemetry-logging
title: "AML.M0024: AI Telemetry Logging"
sidebar_label: "AI Telemetry Logging"
sidebar_position: 25
type: mitigation
---

# AML.M0024: AI Telemetry Logging

Implement logging of inputs and outputs of deployed AI models. When deploying AI agents, implement logging of the intermediate steps of agentic actions and decisions, data access and tool use, and identity of the agent. Monitoring logs can help to detect security threats and mitigate impacts.

Additionally, having logging enabled can discourage adversaries who want to remain undetected from utilizing AI resources.

## Metadata

- **Mitigation ID:** AML.M0024
- **Created:** 2025-03-12
- **Last Modified:** 2025-12-23
- **Category:** Technical - Cyber
- **ML Lifecycle:** Deployment, Monitoring and Maintenance

## Techniques (17)

- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api|AML.T0024: Exfiltration via AI Inference API]] — Telemetry logging can help identify if sensitive data has been exfiltrated.
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-extract-model|AML.T0024.000: Infer Training Data Membership]] — Telemetry logging can help identify if sensitive data has been exfiltrated.
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-invert-model|AML.T0024.001: Invert AI Model]] — Telemetry logging can help identify if sensitive data has been exfiltrated.
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-inference-api/exfiltration-via-inference-API-membership-inference|AML.T0024.002: Extract AI Model]] — Telemetry logging can help identify if sensitive data has been exfiltrated.
- [[frameworks/atlas/techniques/ai-attack-staging/create-proxy-ai-model/create-proxy-via-replication|AML.T0005.001: Train Proxy via Replication]] — Telemetry logging can help identify if a proxy training dataset has been exfiltrated.
- [[frameworks/atlas/techniques/ai-model-access/ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]] — Telemetry logging can help audit API usage of the model.
- [[frameworks/atlas/techniques/ai-model-access/ai-enabled-product-or-service|AML.T0047: AI-Enabled Product or Service]] — Telemetry logging can help identify if sensitive model information has been sent to an attacker.
- [[frameworks/atlas/techniques/execution/llm-prompt-injection|AML.T0051: LLM Prompt Injection]] — Telemetry logging can help identify if unsafe prompts have been submitted to the LLM.
- [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-direct-prompt-injection|AML.T0051.000: Direct]] — Telemetry logging can help identify if unsafe prompts have been submitted to the LLM.
- [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-indirect-prompt-injection|AML.T0051.001: Indirect]] — Telemetry logging can help identify if unsafe prompts have been submitted to the LLM.
- [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-triggered-prompt-injection|AML.T0051.002: Triggered]] — Telemetry logging can help identify if unsafe prompts have been submitted to the LLM.
- [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]] — Log AI agent tool invocations to detect malicious calls.
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-agent-tool-invocation|AML.T0086: Exfiltration via AI Agent Tool Invocation]] — Log AI agent tool invocations to detect malicious calls.
- [[frameworks/atlas/techniques/impact/data-destruction-via-ai-agent-tool-invocation|AML.T0101: Data Destruction via AI Agent Tool Invocation]] — Log AI agent tool invocations to detect malicious calls.
- [[frameworks/atlas/techniques/collection/data-from-ai-services|AML.T0085: Data from AI Services]] — Log requests to AI services to detect malicious queries for data.
- [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-AI-services-RAG-databases|AML.T0085.000: RAG Databases]] — Log requests to AI services to detect malicious queries for data.
- [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-AI-services-agent-tools|AML.T0085.001: AI Agent Tools]] — Log requests to AI services to detect malicious queries for data.
