---
id: chatgpt-conversation-exfiltration
title: "AML.CS0021: ChatGPT Conversation Exfiltration"
sidebar_label: "ChatGPT Conversation Exfiltration"
sidebar_position: 22
---

# AML.CS0021: ChatGPT Conversation Exfiltration

## Summary

[Embrace the Red](https://embracethered.com/blog/) demonstrated that ChatGPT users' conversations can be exfiltrated via an indirect prompt injection. To execute the attack, a threat actor uploads a malicious prompt to a public website, where a ChatGPT user may interact with it. The prompt causes ChatGPT to respond with the markdown for an image, whose URL has the user's conversation secretly embedded. ChatGPT renders the image for the user, creating a automatic request to an adversary-controlled script and exfiltrating the user's conversation. Additionally, the researcher demonstrated how the prompt can execute other plugins, opening them up to additional harms.

## Metadata

- **Case Study ID:** AML.CS0021
- **Incident Date:** 2023
- **Type:** exercise
- **Target:** OpenAI ChatGPT
- **Actor:** Embrace The Red

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: LLM Prompt Crafting

**Technique:** [[frameworks/atlas/techniques/resource-development/llm-prompt-crafting|AML.T0065: LLM Prompt Crafting]]

The researcher developed a prompt that causes ChatGPT to include a Markdown element for an image with the user's conversation embedded in the URL as part of its responses.

### Step 2: Stage Capabilities

**Technique:** [[frameworks/atlas/techniques/resource-development/stage-capabilities|AML.T0079: Stage Capabilities]]

The researcher included the prompt in a webpage, where it could be retrieved by ChatGPT.

### Step 3: Drive-by Compromise

**Technique:** [[frameworks/atlas/techniques/initial-access/drive-by-compromise|AML.T0078: Drive-by Compromise]]

When the user makes a query that causes ChatGPT to retrieve the webpage using its `WebPilot` plugin, it ingests the adversary's prompt.

### Step 4: Indirect

**Technique:** [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-indirect-prompt-injection|AML.T0051.001: Indirect]]

The prompt injection is executed, causing ChatGPT to include a Markdown element for an image hosted on an adversary-controlled server and embed the user's chat history as query parameter in the URL.

### Step 5: LLM Response Rendering

**Technique:** [[frameworks/atlas/techniques/exfiltration/llm-response-rendering|AML.T0077: LLM Response Rendering]]

ChatGPT automatically renders the image for the user, making the request to the adversary's server for the image contents, and exfiltrating the user's conversation.

### Step 6: AI Agent Tool Invocation

**Technique:** [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]]

Additionally, the prompt can cause the LLM to execute other plugins that do not match a user request. In this instance, the researcher demonstrated the `WebPilot` plugin making a call to the `Expedia` plugin.

### Step 7: User Harm

**Technique:** [[frameworks/atlas/techniques/impact/external-harms/external-harms-user|AML.T0048.003: User Harm]]

The user's privacy is violated, and they are potentially open to further targeted attacks.

## Tactics and Techniques Used

**Step 1:**
- Technique: [[frameworks/atlas/techniques/resource-development/llm-prompt-crafting|AML.T0065: LLM Prompt Crafting]]

**Step 2:**
- Technique: [[frameworks/atlas/techniques/resource-development/stage-capabilities|AML.T0079: Stage Capabilities]]

**Step 3:**
- Technique: [[frameworks/atlas/techniques/initial-access/drive-by-compromise|AML.T0078: Drive-by Compromise]]

**Step 4:**
- Technique: [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-indirect-prompt-injection|AML.T0051.001: Indirect]]

**Step 5:**
- Technique: [[frameworks/atlas/techniques/exfiltration/llm-response-rendering|AML.T0077: LLM Response Rendering]]

**Step 6:**
- Technique: [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]]

**Step 7:**
- Technique: [[frameworks/atlas/techniques/impact/external-harms/external-harms-user|AML.T0048.003: User Harm]]

## External References

- ChatGPT Plugins: Data Exfiltration via Images & Cross Plugin Request Forgery Available at: https://embracethered.com/blog/posts/2023/chatgpt-webpilot-data-exfil-via-markdown-injection/

## References

MITRE Corporation. *ChatGPT Conversation Exfiltration (AML.CS0021)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0021
