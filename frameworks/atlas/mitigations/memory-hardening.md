---
id: memory-hardening
title: "AML.M0031: Memory Hardening"
sidebar_label: "Memory Hardening"
sidebar_position: 32
type: mitigation
---

# AML.M0031: Memory Hardening

Memory Hardening involves developing trust boundaries and secure processes for how an AI agent stores and accesses memory and context. This may be implemented using a combination of strategies including restricting an agent's ability to store memories by requiring external authentication and validation for memory updates, performing semantic integrity checks on retrieved memories before agents execute actions, and implementing controls for monitoring of memory and remediation processes for poisoned memory.

## Metadata

- **Mitigation ID:** AML.M0031
- **Created:** 2025-10-29
- **Last Modified:** 2025-12-20
- **Category:** Technical - ML
- **ML Lifecycle:** ML Model Engineering, Deployment, Monitoring and Maintenance

## Techniques (2)

- [[frameworks/atlas/techniques/persistence/ai-agent-context-poisoning|AML.T0080: AI Agent Context Poisoning]] — Memory hardening can help protect LLM memory from manipulation and prevent poisoned memories from executing.
- [[frameworks/atlas/techniques/persistence/ai-agent-context-poisoning/agent-context-poisoning-thread|AML.T0080.000: Memory]] — Memory hardening can help protect LLM memory from manipulation and prevent poisoned memories from executing.
