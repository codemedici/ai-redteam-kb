---
id: ai-agent-tool-invocation
title: "AML.T0053: AI Agent Tool Invocation"
sidebar_label: "AI Agent Tool Invocation"
sidebar_position: 54
---

# AML.T0053: AI Agent Tool Invocation

Adversaries may use their access to an AI agent to invoke tools the agent has access to. LLMs are often connected to other services or resources via tools to increase their capabilities. Tools may include integrations with other applications, access to public or private data sources, and the ability to execute code.

This may allow adversaries to execute API calls to integrated applications or services, providing the adversary with increased privileges on the system. Adversaries may take advantage of connected data sources to retrieve sensitive information. They may also use an LLM integrated with a command or script interpreter to execute arbitrary instructions.

AI agents may be configured to have access to tools that are not directly accessible by users. Adversaries may abuse this to gain access to tools they otherwise wouldn't be able to use.

## Metadata

- **Technique ID:** AML.T0053
- **Created:** 2023-10-25
- **Last Modified:** 2025-11-04
- **Maturity:** demonstrated

## Tactics (2)

- [[frameworks/atlas/tactics/execution|Execution]]
- [[frameworks/atlas/tactics/privilege-escalation|Privilege Escalation]]

## Mitigations (11)

- [[frameworks/atlas/mitigations/generative-ai-guardrails|AML.M0020: Generative AI Guardrails]] — Guardrails can prevent harmful inputs that can lead to plugin compromise, and they can detect PII in model outputs.
- [[frameworks/atlas/mitigations/generative-ai-guidelines|AML.M0021: Generative AI Guidelines]] — Model guidelines can instruct the model to refuse a response to unsafe inputs.
- [[frameworks/atlas/mitigations/generative-ai-model-alignment|AML.M0022: Generative AI Model Alignment]] — Model alignment can improve the parametric safety of a model by guiding it away from unsafe prompts and responses.
- [[frameworks/atlas/mitigations/ai-telemetry-logging|AML.M0024: AI Telemetry Logging]] — Log AI agent tool invocations to detect malicious calls.
- [[frameworks/atlas/mitigations/privileged-ai-agent-permissions-configuration|AML.M0026: Privileged AI Agent Permissions Configuration]] — Configuring privileged AI agents with proper access controls for tool use can limit an adversary's ability to abuse tool invocations if the agent is compromised.
- [[frameworks/atlas/mitigations/single-user-ai-agent-permissions-configuration|AML.M0027: Single-User AI Agent Permissions Configuration]] — Configuring AI agents with permissions that are inherited from the user for tool use can limit an adversary's ability to abuse tool invocations if the agent is compromised.
- [[frameworks/atlas/mitigations/ai-agent-tools-permissions-configuration|AML.M0028: AI Agent Tools Permissions Configuration]] — Configuring AI Agent tools with access controls inherited from the user or the AI Agent invoking the tool can limit an adversary's capabilities within a system, including their ability to abuse tool invocations and access sensitive data.
- [[frameworks/atlas/mitigations/human-in-the-loop-for-ai-agent-actions|AML.M0029: Human In-the-Loop for AI Agent Actions]] — Requiring user confirmation of AI agent tool invocations can prevent the automatic execution of tools by an adversary.
- [[frameworks/atlas/mitigations/restrict-ai-agent-tool-invocation-on-untrusted-data|AML.M0030: Restrict AI Agent Tool Invocation on Untrusted Data]] — Restricting the automatic tool use when untrusted data is present can prevent adversaries from invoking tools via prompt injections.
- [[frameworks/atlas/mitigations/segmentation-of-ai-agent-components|AML.M0032: Segmentation of AI Agent Components]] — Segmentation can prevent adversaries from utilizing tools in an agentic workflow to perform unsafe actions that affect other components.
- [[frameworks/atlas/mitigations/input-and-output-validation-for-ai-agent-components|AML.M0033: Input and Output Validation for AI Agent Components]] — Validation can prevent adversaries from utilizing tools in an agentic workflow to generate unsafe output.

## Case Studies (11)

- [[frameworks/atlas/case-studies/achieving-code-execution-in-mathgpt-via-prompt-injection|Achieving Code Execution in MathGPT via Prompt Injection]]
- [[frameworks/atlas/case-studies/chatgpt-conversation-exfiltration|ChatGPT Conversation Exfiltration]]
- [[frameworks/atlas/case-studies/morris-ii-worm-rag-based-attack|Morris II Worm: RAG-Based Attack]]
- [[frameworks/atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|Financial Transaction Hijacking with M365 Copilot as an Insider]]
- [[frameworks/atlas/case-studies/planting-instructions-for-delayed-automatic-ai-agent-tool-invocation|Planting Instructions for Delayed Automatic AI Agent Tool Invocation]]
- [[frameworks/atlas/case-studies/living-off-ai-prompt-injection-via-jira-service-management|Living Off AI: Prompt Injection via Jira Service Management]]
- [[frameworks/atlas/case-studies/data-exfiltration-via-an-mcp-server-used-by-cursor|Data Exfiltration via an MCP Server used by Cursor]]
- [[frameworks/atlas/case-studies/data-destruction-via-indirect-prompt-injection-targeting-claude-computer-use|Data Destruction via Indirect Prompt Injection Targeting Claude Computer-Use]]
- [[frameworks/atlas/case-studies/exposed-clawdbot-control-interfaces-leads-to-credential-access-and-execution|Exposed ClawdBot Control Interfaces Leads to Credential Access and Execution]]
- [[frameworks/atlas/case-studies/supply-chain-compromise-via-poisoned-clawdbot-skill|Supply Chain Compromise via Poisoned ClawdBot Skill]]
- [[frameworks/atlas/case-studies/openclaw-command-control-via-prompt-injection|OpenClaw Command & Control via Prompt Injection]]
