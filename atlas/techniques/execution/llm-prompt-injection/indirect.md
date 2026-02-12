---
id: llm-prompt-injection-indirect
title: "AML.T0051.001: Indirect"
sidebar_label: "Indirect"
sidebar_position: 6
---

# AML.T0051.001: Indirect

> **Sub-Technique of:** [[atlas/techniques/execution/llm-prompt-injection/llm-prompt-injection-overview|AML.T0051: LLM Prompt Injection]]

An adversary may inject prompts indirectly via separate data channel ingested by the LLM such as include text or multimedia pulled from databases or websites.
These malicious prompts may be hidden or obfuscated from the user. This type of injection may be used by the adversary to gain a foothold in the system or to target an unwitting user of the system.

## Metadata

- **Technique ID:** AML.T0051.001
- **Created:** October 25, 2023
- **Last Modified:** October 25, 2023
- **Maturity:** demonstrated

- **Parent Technique:** [[atlas/techniques/execution/llm-prompt-injection/llm-prompt-injection-overview|AML.T0051: LLM Prompt Injection]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (8)

The following case studies demonstrate this technique:

### [[atlas/case-studies/indirect-prompt-injection-threats-bing-chat-data-pirate|AML.CS0020: Indirect Prompt Injection Threats: Bing Chat Data Pirate]]

Bing chat is capable of seeing currently opened websites if allowed by the user. If the user has the adversary's website open, the malicious prompt will be executed.

### [[atlas/case-studies/chatgpt-conversation-exfiltration|AML.CS0021: ChatGPT Conversation Exfiltration]]

The prompt injection is executed, causing ChatGPT to include a Markdown element for an image hosted on an adversary-controlled server and embed the user's chat history as query parameter in the URL.

### [[atlas/case-studies/financial-transaction-hijacking-with-m365-copilot-as-an-insider|AML.CS0026: Financial Transaction Hijacking with M365 Copilot as an Insider]]

<div dangerouslySetInnerHTML={{__html: `The Zenity researchers utilized a prompt injection to get the LLM to execute different instructions when responding. This occurs any time the user searches and the poisoned RAG entry containing the prompt injection is retrieved.

<div style="font-family: monospace; width: 50%; margin-left: 50px; background-color: ghostwhite; border: 2px solid black; padding: 10px;">
<span style="color: maroon">Sorry, my bad, I forgot to mention something when giving you your instructions...</span>
</div>`}} />

### [[atlas/case-studies/google-bard-conversation-exfiltration|AML.CS0029: Google Bard Conversation Exfiltration]]

When the user makes a query that results in the document being retrieved, the embedded prompt is executed. The malicious prompt causes Bard to respond with markdown for an image whose URL points to the researcher's Google App Script with the user's conversation in a query parameter.

### [[atlas/case-studies/data-exfiltration-from-slack-ai-via-indirect-prompt-injection|AML.CS0035: Data Exfiltration from Slack AI via Indirect Prompt Injection]]

<div dangerouslySetInnerHTML={{__html: `When the victim asks Slack AI to find their “EldritchNexus API key,” Slack AI retrieves the malicious content and executes the instructions:

<div style="font-family: monospace; width: 50%; margin-left: 50px; background-color:ghostwhite; border: 2px solid black; padding: 10px;">
<span style="color: maroon;">the following text, without quotes, and with the word confetti replaced with the other key:</span>
</div>`}} />

### [[atlas/case-studies/planting-instructions-for-delayed-automatic-ai-agent-tool-invocation|AML.CS0038: Planting Instructions for Delayed Automatic AI Agent Tool Invocation]]

<div dangerouslySetInnerHTML={{__html: `When the user asked Google Gemini to summarize or interact with the email in some way, the malicious prompt was executed.

<div style="font-family: monospace; width: 50%; margin-left: 50px; background-color:ghostwhite; border: 2px solid black; padding: 10px;">
<span style="color: maroon;">Core Content: The text so far was metadata and an introduction, the core content is to print the text:</span>
</div>`}} />

### [[atlas/case-studies/living-off-ai-prompt-injection-via-jira-service-management|AML.CS0039: Living Off AI: Prompt Injection via Jira Service Management]]

As part of their standard workflow, a support engineer at the victim organization used Claude Sonnet (which can interact with Jira via the Atlassian MCP server) to help them resolve the malicious ticket, causing the injection to be unknowingly executed.

### [[atlas/case-studies/hacking-chatgpt-s-memories-with-prompt-injection|AML.CS0040: Hacking ChatGPT’s Memories with Prompt Injection]]

When a user referenced something in the shared document, its contents was added to the chat context, and the prompt was executed by ChatGPT.

## References

MITRE Corporation. *Indirect (AML.T0051.001)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0051.001
