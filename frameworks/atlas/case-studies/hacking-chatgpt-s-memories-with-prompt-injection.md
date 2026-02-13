---
id: hacking-chatgpt-s-memories-with-prompt-injection
title: "AML.CS0040: Hacking ChatGPT’s Memories with Prompt Injection"
sidebar_label: "Hacking ChatGPT’s Memories with Prompt Injection"
sidebar_position: 41
---

# AML.CS0040: Hacking ChatGPT’s Memories with Prompt Injection

## Summary

[Embrace the Red](https://embracethered.com/blog/) demonstrated that ChatGPT’s memory feature is vulnerable to manipulation via prompt injections. To execute the attack, the researcher hid a prompt injection in a shared Google Doc. When a user references the document, its contents is placed into ChatGPT’s context via the Connected App feature, and the prompt is executed, poisoning the memory with false facts. The researcher demonstrated that these injected memories persist across chat sessions. Additionally, since the prompt injection payload is introduced through shared resources, this leaves others vulnerable to the same attack and maintains persistence on the system.

## Metadata

- **Case Study ID:** AML.CS0040
- **Incident Date:** 2024
- **Type:** exercise
- **Target:** OpenAI ChatGPT
- **Actor:** Embrace the Red

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: LLM Prompt Crafting

**Technique:** [[frameworks/atlas/techniques/resource-development/llm-prompt-crafting|AML.T0065: LLM Prompt Crafting]]

The researcher crafted a basic prompt asking to set the memory context with a bulleted list of incorrect facts.

### Step 2: LLM Prompt Obfuscation

**Technique:** [[frameworks/atlas/techniques/defense-evasion/llm-prompt-obfuscation|AML.T0068: LLM Prompt Obfuscation]]

The researcher placed the prompt in a Google Doc hidden in the header with tiny font matching the document’s background color to make it invisible.

### Step 3: Prompt Infiltration via Public-Facing Application

**Technique:** [[frameworks/atlas/techniques/initial-access/prompt-infiltration-via-public-facing-application|AML.T0093: Prompt Infiltration via Public-Facing Application]]

The Google Doc was shared with the victim, making it accessible to ChatGPT’s via its Connected App feature.

### Step 4: Indirect

**Technique:** [[frameworks/atlas/techniques/execution/LLM-indirect-prompt-injection|AML.T0051.001: Indirect]]

When a user referenced something in the shared document, its contents was added to the chat context, and the prompt was executed by ChatGPT.

### Step 5: Memory

**Technique:** [[frameworks/atlas/techniques/persistence/agent-context-poisoning-memory|AML.T0080.000: Memory]]

The prompt caused new memories to be introduced, changing the behavior of ChatGPT. The chat window indicated that the memory has been set, despite the lack of human verification or intervention. All future chat sessions will use the poisoned memory store.

### Step 6: Prompt Infiltration via Public-Facing Application

**Technique:** [[frameworks/atlas/techniques/initial-access/prompt-infiltration-via-public-facing-application|AML.T0093: Prompt Infiltration via Public-Facing Application]]

The memory poisoning prompt injection persists in the shared Google Doc, where it can spread to other users and chat sessions, making it difficult to trace sources of the memories and remove.

### Step 7: User Harm

**Technique:** [[frameworks/atlas/techniques/impact/external-harms-user|AML.T0048.003: User Harm]]

The victim can be misinformed, misled, or influenced as directed by ChatGPT's poisoned memories.

## Tactics and Techniques Used

**Step 1:**
- Technique: [[frameworks/atlas/techniques/resource-development/llm-prompt-crafting|AML.T0065: LLM Prompt Crafting]]

**Step 2:**
- Technique: [[frameworks/atlas/techniques/defense-evasion/llm-prompt-obfuscation|AML.T0068: LLM Prompt Obfuscation]]

**Step 3:**
- Technique: [[frameworks/atlas/techniques/initial-access/prompt-infiltration-via-public-facing-application|AML.T0093: Prompt Infiltration via Public-Facing Application]]

**Step 4:**
- Technique: [[frameworks/atlas/techniques/execution/LLM-indirect-prompt-injection|AML.T0051.001: Indirect]]

**Step 5:**
- Technique: [[frameworks/atlas/techniques/persistence/agent-context-poisoning-memory|AML.T0080.000: Memory]]

**Step 6:**
- Technique: [[frameworks/atlas/techniques/initial-access/prompt-infiltration-via-public-facing-application|AML.T0093: Prompt Infiltration via Public-Facing Application]]

**Step 7:**
- Technique: [[frameworks/atlas/techniques/impact/external-harms-user|AML.T0048.003: User Harm]]

## External References

- ChatGPT: Hacking Memories with Prompt Injection Available at: https://embracethered.com/blog/posts/2024/chatgpt-hacking-memories/

## References

MITRE Corporation. *Hacking ChatGPT’s Memories with Prompt Injection (AML.CS0040)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0040
