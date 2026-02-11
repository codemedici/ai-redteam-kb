---
id: ai-agent-context-poisoning-thread
title: "AML.T0080.001: Thread"
sidebar_label: "Thread"
sidebar_position: 9
---

# AML.T0080.001: Thread

> **Sub-Technique of:** [[ai-agent-context-poisoning|AML.T0080: AI Agent Context Poisoning]]



Adversaries may introduce malicious instructions into a chat thread of a large language model (LLM) to cause behavior changes which persist for the remainder of the thread. A chat thread may continue for an extended period over multiple sessions.

The malicious instructions may be introduced via Direct or Indirect Prompt Injection. Direct Injection may occur in cases where the adversary has acquired a user's LLM API keys and can inject queries directly into any thread.

As the token limits for LLMs rise, AI systems can make use of larger context windows which allow malicious instructions to persist longer in a thread.
Thread Poisoning may affect multiple users if the LLM is being used in a service with shared threads. For example, if an agent is active in a Slack channel with multiple participants, a single malicious message from one user can influence the agent's behavior in future interactions with others.

## Metadata

- **Technique ID:** AML.T0080.001
- **Created:** September 30, 2025
- **Last Modified:** September 30, 2025
- **Maturity:** demonstrated

- **Parent Technique:** [[ai-agent-context-poisoning|AML.T0080: AI Agent Context Poisoning]]

## Tactics (0)

This technique supports the following tactics:

*No tactics currently associated with this technique.*



## Case Studies (1)


The following case studies demonstrate this technique:

### [[aikatz-attacking-llm-desktop-applications|AML.CS0036: AIKatz: Attacking LLM Desktop Applications]]

The attacker could craft malicious prompts that manipulate the context of a chat thread, an effect that would persist for the duration of the thread.



## References

MITRE Corporation. *Thread (AML.T0080.001)*. MITRE ATLAS. Available at: https://atlas.mitre.org/techniques/AML.T0080.001
