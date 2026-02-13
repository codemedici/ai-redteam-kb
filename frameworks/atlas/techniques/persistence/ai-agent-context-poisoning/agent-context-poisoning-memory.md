---
id: ai-agent-context-poisoning-memory
title: "AML.T0080.000: Memory"
sidebar_label: "Memory"
sidebar_position: 8
---

# AML.T0080.000: Memory

Adversaries may manipulate the memory of a large language model (LLM) in order to persist changes to the LLM to future chat sessions. 

Memory is a common feature in LLMs that allows them to remember information across chat sessions by utilizing a user-specific database. Because the memory is controlled via normal conversations with the user (e.g. "remember my preference for ...") an adversary can inject memories via Direct or Indirect Prompt Injection. Memories may contain malicious instructions (e.g. instructions that leak private conversations) or may promote the adversary's hidden agenda (e.g. manipulating the user).

## Metadata

- **Technique ID:** AML.T0080.000
- **Created:** September 30, 2025
- **Last Modified:** September 30, 2025
- **Maturity:** demonstrated

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*

## Case Studies (2)

The following case studies demonstrate this technique:

### [[frameworks/atlas/case-studies/aikatz-attacking-llm-desktop-applications|AML.CS0036: AIKatz: Attacking LLM Desktop Applications]]

The attacker could then craft malicious prompts that manipulate the LLM’s memory to achieve a persistent effect. Any change in memory would also propagate to any new chat threads.

### [[frameworks/atlas/case-studies/hacking-chatgpt-s-memories-with-prompt-injection|AML.CS0040: Hacking ChatGPT’s Memories with Prompt Injection]]

The prompt caused new memories to be introduced, changing the behavior of ChatGPT. The chat window indicated that the memory has been set, despite the lack of human verification or intervention. All future chat sessions will use the poisoned memory store.

## References

MITRE Corporation. *Memory (AML.T0080.000)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0080.000
