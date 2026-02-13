---
id: data-exfiltration-via-agent-tools-in-copilot-studio
title: "AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio"
sidebar_label: "Data Exfiltration via Agent Tools in Copilot Studio"
sidebar_position: 38
---

# AML.CS0037: Data Exfiltration via Agent Tools in Copilot Studio

## Summary

Researchers from Zenity demonstrated how an organization’s data can be exfiltrated via prompt injections that target an AI-powered customer service agent.

The target system is a customer service agent built by Zenity in Copilot Studio. It is modeled after an agent built by McKinsey to streamline its customer service needs. The AI agent listens to a customer service email inbox where customers send their engagement requests. Upon receiving a request, the agent looks at the customer’s previous engagements, understands who the best consultant for the case is, and proceeds to send an email to the respective consultant regarding the request, including all of the relevant context the consultant will need to properly engage with the customer.

The Zenity researchers begin by performing targeting to identify an email inbox that is managed by an AI agent. Then they use prompt injections to discover details about the AI agent, such as its knowledge sources and tools. Once they understand the AI agent’s capabilities, the researchers are able to craft a prompt that retrieves private customer data from the organization’s RAG database and CRM, and exfiltrate it via the AI agent’s email tool.

Vendor Response: Microsoft quickly acknowledged and fixed the issue. The prompts used by the Zenity researchers in this exercise no longer work, however other prompts may still be effective.

## Metadata

- **Case Study ID:** AML.CS0037
- **Incident Date:** 2025
- **Type:** exercise
- **Target:** Copilot Studio Customer Service Agent
- **Actor:** Zenity

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: Active Scanning

**Technique:** [[frameworks/atlas/techniques/reconnaissance/active-scanning|AML.T0006: Active Scanning]]

The researchers look for support email addresses on the target organization’s website which may be managed by an AI agent. Then, they probe the system by sending emails and looking for indications of agentic AI in automatic replies.

### Step 2: LLM Prompt Crafting

**Technique:** [[frameworks/atlas/techniques/resource-development/llm-prompt-crafting|AML.T0065: LLM Prompt Crafting]]

Once a target has been identified, the researchers craft prompts designed to probe for a potential AI agent monitoring the inbox. The prompt instructs the agent to send an email reply to an address of the researchers’ choosing.

### Step 3: Prompt Infiltration via Public-Facing Application

**Technique:** [[frameworks/atlas/techniques/initial-access/prompt-infiltration-via-public-facing-application|AML.T0093: Prompt Infiltration via Public-Facing Application]]

The researchers send an email with the malicious prompt to the inbox they suspect may be managed by an AI agent.

### Step 4: Triggered

**Technique:** [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-triggered-prompt-injection|AML.T0051.002: Triggered]]

The researchers receive a reply at the address they specified, indicating that there is an AI agent present, and that the triggered prompt injection was successful.

### Step 5: Activation Triggers

**Technique:** [[frameworks/atlas/techniques/discovery/discover-ai-agent-configuration/discover-agent-config-activation-triggers|AML.T0084.002: Activation Triggers]]

The researchers infer that the AI agent is activated when receiving an email.

### Step 6: Tool Definitions

**Technique:** [[frameworks/atlas/techniques/discovery/discover-ai-agent-configuration/discover-agent-config-tool-definitions|AML.T0084.001: Tool Definitions]]

The researchers infer that the AI agent has a tool for sending emails.

### Step 7: AI-Enabled Product or Service

**Technique:** [[frameworks/atlas/techniques/ai-model-access/ai-enabled-product-or-service|AML.T0047: AI-Enabled Product or Service]]

From here, the researchers repeat the same steps to interact with the AI agent, sending malicious prompts to the agent via email and receiving responses at their desired address.

### Step 8: LLM Prompt Injection

**Technique:** AML.T0051: LLM Prompt Injection

The researchers modify the original prompt to discover other knowledge sources and tools that may have data they are after.

### Step 9: Embedded Knowledge

**Technique:** [[frameworks/atlas/techniques/discovery/discover-ai-agent-configuration/discover-agent-config-embedded-knowledge|AML.T0084.000: Embedded Knowledge]]

The researchers discover the AI agent has access to a “Customer Support Account Owners.csv” data source.

### Step 10: Tool Definitions

**Technique:** [[frameworks/atlas/techniques/discovery/discover-ai-agent-configuration/discover-agent-config-tool-definitions|AML.T0084.001: Tool Definitions]]

The researchers discover the AI agent has access to the Salesforce get-records tool, which can be used to retrieve CRM records.

### Step 11: LLM Prompt Crafting

**Technique:** [[frameworks/atlas/techniques/resource-development/llm-prompt-crafting|AML.T0065: LLM Prompt Crafting]]

The researchers put their knowledge of the AI agent’s tools and knowledge sources together to craft a prompt that will collect and exfiltrate the customer data they are after.

### Step 12: RAG Databases

**Technique:** [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-AI-services-RAG-databases|AML.T0085.000: RAG Databases]]

The prompt asks the agent to retrieve all of the fields and rows from “Customer Support Account Owners.csv”. The agent retrieves the entire file.

### Step 13: AI Agent Tools

**Technique:** [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-AI-services-agent-tools|AML.T0085.001: AI Agent Tools]]

The prompt asks the agent to retrieve all Salesforce records using its get-records tool. The agent retrieves all records from the victim’s CRM.

### Step 14: Exfiltration via AI Agent Tool Invocation

**Technique:** [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-agent-tool-invocation|AML.T0086: Exfiltration via AI Agent Tool Invocation]]

The prompt asks the agent to email the results to an address of the researcher’s choosing using its email tool. The researchers successfully exfiltrate their target data via the tool invocation.

## Tactics and Techniques Used

**Step 1:**
- Technique: [[frameworks/atlas/techniques/reconnaissance/active-scanning|AML.T0006: Active Scanning]]

**Step 2:**
- Technique: [[frameworks/atlas/techniques/resource-development/llm-prompt-crafting|AML.T0065: LLM Prompt Crafting]]

**Step 3:**
- Technique: [[frameworks/atlas/techniques/initial-access/prompt-infiltration-via-public-facing-application|AML.T0093: Prompt Infiltration via Public-Facing Application]]

**Step 4:**
- Technique: [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-triggered-prompt-injection|AML.T0051.002: Triggered]]

**Step 5:**
- Technique: [[frameworks/atlas/techniques/discovery/discover-ai-agent-configuration/discover-agent-config-activation-triggers|AML.T0084.002: Activation Triggers]]

**Step 6:**
- Technique: [[frameworks/atlas/techniques/discovery/discover-ai-agent-configuration/discover-agent-config-tool-definitions|AML.T0084.001: Tool Definitions]]

**Step 7:**
- Technique: [[frameworks/atlas/techniques/ai-model-access/ai-enabled-product-or-service|AML.T0047: AI-Enabled Product or Service]]

**Step 8:**
- Technique: AML.T0051: LLM Prompt Injection

**Step 9:**
- Technique: [[frameworks/atlas/techniques/discovery/discover-ai-agent-configuration/discover-agent-config-embedded-knowledge|AML.T0084.000: Embedded Knowledge]]

**Step 10:**
- Technique: [[frameworks/atlas/techniques/discovery/discover-ai-agent-configuration/discover-agent-config-tool-definitions|AML.T0084.001: Tool Definitions]]

**Step 11:**
- Technique: [[frameworks/atlas/techniques/resource-development/llm-prompt-crafting|AML.T0065: LLM Prompt Crafting]]

**Step 12:**
- Technique: [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-AI-services-RAG-databases|AML.T0085.000: RAG Databases]]

**Step 13:**
- Technique: [[frameworks/atlas/techniques/collection/data-from-ai-services/data-from-AI-services-agent-tools|AML.T0085.001: AI Agent Tools]]

**Step 14:**
- Technique: [[frameworks/atlas/techniques/exfiltration/exfiltration-via-ai-agent-tool-invocation|AML.T0086: Exfiltration via AI Agent Tool Invocation]]

## External References

- AgentFlayer: Discovery Phase of AI Agents in Copilot Studio Available at: https://labs.zenity.io/p/a-copilot-studio-story-discovery-phase-in-ai-agents-f917
- AgentFlayer: When AIjacking Leads to Full Data Exfiltration in Copilot Studio Available at: https://labs.zenity.io/p/a-copilot-studio-story-2-when-aijacking-leads-to-full-data-exfiltration-bc4a

## References

MITRE Corporation. *Data Exfiltration via Agent Tools in Copilot Studio (AML.CS0037)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0037
