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

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/llm-prompt-crafting|AML.T0065: LLM Prompt Crafting]]

The researcher developed a prompt that causes ChatGPT to include a Markdown element for an image with the user's conversation embedded in the URL as part of its responses.

### Step 2: Stage Capabilities

**Tactic:** [[atlas/tactics/resource-development|AML.TA0003: Resource Development]]
**Technique:** [[atlas/techniques/resource-development/stage-capabilities|AML.T0079: Stage Capabilities]]

The researcher included the prompt in a webpage, where it could be retrieved by ChatGPT.

### Step 3: Drive-by Compromise

**Tactic:** [[atlas/tactics/initial-access|AML.TA0004: Initial Access]]
**Technique:** [[atlas/techniques/initial-access/drive-by-compromise|AML.T0078: Drive-by Compromise]]

When the user makes a query that causes ChatGPT to retrieve the webpage using its `WebPilot` plugin, it ingests the adversary's prompt.

### Step 4: Indirect

**Tactic:** [[atlas/tactics/execution|AML.TA0005: Execution]]
**Technique:** [[atlas/techniques/execution/llm-prompt-injection/indirect|AML.T0051.001: Indirect]]

The prompt injection is executed, causing ChatGPT to include a Markdown element for an image hosted on an adversary-controlled server and embed the user's chat history as query parameter in the URL.

### Step 5: LLM Response Rendering

**Tactic:** [[atlas/tactics/exfiltration|AML.TA0010: Exfiltration]]
**Technique:** [[atlas/techniques/exfiltration/llm-response-rendering|AML.T0077: LLM Response Rendering]]

ChatGPT automatically renders the image for the user, making the request to the adversary's server for the image contents, and exfiltrating the user's conversation.

### Step 6: AI Agent Tool Invocation

**Tactic:** [[atlas/tactics/privilege-escalation|AML.TA0012: Privilege Escalation]]
**Technique:** [[atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]]

Additionally, the prompt can cause the LLM to execute other plugins that do not match a user request. In this instance, the researcher demonstrated the `WebPilot` plugin making a call to the `Expedia` plugin.

### Step 7: User Harm

**Tactic:** [[atlas/tactics/impact|AML.TA0011: Impact]]
**Technique:** [[atlas/techniques/impact/external-harms/user-harm|AML.T0048.003: User Harm]]

The user's privacy is violated, and they are potentially open to further targeted attacks.


## Tactics and Techniques Used


| Step | Tactic | Technique |
|---|---|---|
| 1 | [[atlas/tactics/resource-development|AML.TA0003: Resource Development]] | [[atlas/techniques/resource-development/llm-prompt-crafting|AML.T0065: LLM Prompt Crafting]] |
| 2 | [[atlas/tactics/resource-development|AML.TA0003: Resource Development]] | [[atlas/techniques/resource-development/stage-capabilities|AML.T0079: Stage Capabilities]] |
| 3 | [[atlas/tactics/initial-access|AML.TA0004: Initial Access]] | [[atlas/techniques/initial-access/drive-by-compromise|AML.T0078: Drive-by Compromise]] |
| 4 | [[atlas/tactics/execution|AML.TA0005: Execution]] | [[atlas/techniques/execution/llm-prompt-injection/indirect|AML.T0051.001: Indirect]] |
| 5 | [[atlas/tactics/exfiltration|AML.TA0010: Exfiltration]] | [[atlas/techniques/exfiltration/llm-response-rendering|AML.T0077: LLM Response Rendering]] |
| 6 | [[atlas/tactics/privilege-escalation|AML.TA0012: Privilege Escalation]] | [[atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]] |
| 7 | [[atlas/tactics/impact|AML.TA0011: Impact]] | [[atlas/techniques/impact/external-harms/user-harm|AML.T0048.003: User Harm]] |



## External References

- ChatGPT Plugins: Data Exfiltration via Images & Cross Plugin Request Forgery Available at: https://embracethered.com/blog/posts/2023/chatgpt-webpilot-data-exfil-via-markdown-injection/


## References

MITRE Corporation. *ChatGPT Conversation Exfiltration (AML.CS0021)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0021
