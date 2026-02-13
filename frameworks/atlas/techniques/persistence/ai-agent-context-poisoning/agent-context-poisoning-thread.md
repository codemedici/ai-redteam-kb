---
id: agent-context-poisoning-thread
title: "AML.T0080.000: Memory"
sidebar_label: "Memory"
sidebar_position: 80001
---

# AML.T0080.000: Memory

Adversaries may manipulate the memory of a large language model (LLM) in order to persist changes to the LLM to future chat sessions. 

Memory is a common feature in LLMs that allows them to remember information across chat sessions by utilizing a user-specific database. Because the memory is controlled via normal conversations with the user (e.g. "remember my preference for ...") an adversary can inject memories via Direct or Indirect Prompt Injection. Memories may contain malicious instructions (e.g. instructions that leak private conversations) or may promote the adversary's hidden agenda (e.g. manipulating the user).

## Metadata

- **Technique ID:** AML.T0080.000
- **Created:** 2025-09-30
- **Last Modified:** 2025-09-30
- **Maturity:** demonstrated

## Parent Technique

**Parent Technique:** AML.T0080 — AI Agent Context Poisoning

## Tactics (1)

- [[frameworks/atlas/tactics/persistence|Persistence]]

## Mitigations (1)

- [[frameworks/atlas/mitigations/memory-hardening|AML.M0031: Memory Hardening]] — Memory hardening can help protect LLM memory from manipulation and prevent poisoned memories from executing.

## Case Studies (2)

- [[frameworks/atlas/case-studies/aikatz-attacking-llm-desktop-applications|AIKatz: Attacking LLM Desktop Applications]]
- [[frameworks/atlas/case-studies/hacking-chatgpt-s-memories-with-prompt-injection|Hacking ChatGPT’s Memories with Prompt Injection]]
