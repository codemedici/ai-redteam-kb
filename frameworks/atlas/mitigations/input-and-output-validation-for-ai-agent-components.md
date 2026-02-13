---
id: input-and-output-validation-for-ai-agent-components
title: "AML.M0033: Input and Output Validation for AI Agent Components"
sidebar_label: "Input and Output Validation for AI Agent Components"
sidebar_position: 34
type: mitigation
---

# AML.M0033: Input and Output Validation for AI Agent Components

Implement validation on inputs and outputs for the tools and data sources used by AI agents. Validation includes enforcing a common data format, schema validation, checks for sensitive or prohibited information leakage, and data sanitization to remove potential injections or unsafe code. Input and output validation can help prevent compromises from spreading in AI-enabled systems and can help secure the workflow when multiple components are chained together. Validation should be performed external to the AI agent.

## Metadata

- **Mitigation ID:** AML.M0033
- **Created:** 2025-11-25
- **Last Modified:** 2025-12-18
- **Category:** Technical - ML
- **ML Lifecycle:** Business and Data Understanding, Data Preparation, Deployment

## Techniques (6)

- [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]] — Validation can prevent adversaries from utilizing tools in an agentic workflow to generate unsafe output.
- [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-agent-tool-invocation|AML.T0086: Exfiltration via AI Agent Tool Invocation]] — Validation can prevent adversaries from utilizing tools in an agentic workflow to compromise sensitive data sources.
- [[frameworks/atlas/techniques/execution/llm-prompt-injection|AML.T0051: LLM Prompt Injection]] — Validation can prevent adversaries from executing prompt injections that could affect agentic workflows.
- [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-direct-prompt-injection|AML.T0051.000: Direct]] — Validation can prevent adversaries from executing prompt injections that could affect agentic workflows.
- [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-indirect-prompt-injection|AML.T0051.001: Indirect]] — Validation can prevent adversaries from executing prompt injections that could affect agentic workflows.
- [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-triggered-prompt-injection|AML.T0051.002: Triggered]] — Validation can prevent adversaries from executing prompt injections that could affect agentic workflows.
