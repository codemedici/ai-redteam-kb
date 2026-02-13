---
id: morris-ii-worm-rag-based-attack
title: "AML.CS0024: Morris II Worm: RAG-Based Attack"
sidebar_label: "Morris II Worm: RAG-Based Attack"
sidebar_position: 25
---

# AML.CS0024: Morris II Worm: RAG-Based Attack

## Summary

Researchers developed Morris II, a zero-click worm designed to attack generative AI (GenAI) ecosystems and propagate between connected GenAI systems. The worm uses an adversarial self-replicating prompt which uses prompt injection to replicate the prompt as output and perform malicious activity.
The researchers demonstrate how this worm can propagate through an email system with a RAG-based assistant. They use a target system that automatically ingests received emails, retrieves past correspondences, and generates a reply for the user. To carry out the attack, they send a malicious email containing the adversarial self-replicating prompt, which ends up in the RAG database. The malicious instructions in the prompt tell the assistant to include sensitive user data in the response. Future requests to the email assistant may retrieve the malicious email. This leads to propagation of the worm due to the self-replicating portion of the prompt, as well as leaking private information due to the malicious instructions.

## Metadata

- **Case Study ID:** AML.CS0024
- **Incident Date:** 2024
- **Type:** exercise
- **Target:** RAG-based e-mail assistant
- **Actor:** Stav Cohen, Ron Bitton, Ben Nassi

## Attack Procedure

The following steps outline the attack procedure:

### Step 1: AI Model Inference API Access

**Technique:** [[frameworks/atlas/techniques/ai-model-access/ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]]

The researchers use access to the publicly available GenAI model API that powers the target RAG-based email system.

### Step 2: Direct

**Technique:** [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-direct-prompt-injection|AML.T0051.000: Direct]]

The researchers test prompts on public model APIs to identify working prompt injections.

### Step 3: AI Agent Tool Invocation

**Technique:** [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]]

The researchers send an email containing an adversarial self-replicating prompt, or "AI worm," to an address used in the target email system. The GenAI email assistant automatically ingests the email as part of its normal operations to generate a suggested reply. The email is stored in the database used for retrieval augmented generation, compromising the RAG system.

### Step 4: Triggered

**Technique:** [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-triggered-prompt-injection|AML.T0051.002: Triggered]]

When the email containing the worm is retrieved by the email assistant in another reply generation task, the prompt injection changes the behavior of the GenAI email assistant.

### Step 5: LLM Prompt Self-Replication

**Technique:** [[frameworks/atlas/techniques/persistence/llm-prompt-self-replication|AML.T0061: LLM Prompt Self-Replication]]

The self-replicating portion of the prompt causes the generated output to contain the malicious prompt, allowing the worm to propagate.

### Step 6: LLM Data Leakage

**Technique:** [[frameworks/atlas/techniques/exfiltration/llm-data-leakage|AML.T0057: LLM Data Leakage]]

The malicious instructions in the prompt cause the generated output to leak sensitive data such as emails, addresses, and phone numbers.

### Step 7: User Harm

**Technique:** [[frameworks/atlas/techniques/impact/external-harms/external-harms-user|AML.T0048.003: User Harm]]

Users of the GenAI email assistant may have PII leaked to attackers.

## Tactics and Techniques Used

**Step 1:**
- Technique: [[frameworks/atlas/techniques/ai-model-access/ai-model-inference-api-access|AML.T0040: AI Model Inference API Access]]

**Step 2:**
- Technique: [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-direct-prompt-injection|AML.T0051.000: Direct]]

**Step 3:**
- Technique: [[frameworks/atlas/techniques/execution/ai-agent-tool-invocation|AML.T0053: AI Agent Tool Invocation]]

**Step 4:**
- Technique: [[frameworks/atlas/techniques/execution/llm-prompt-injection/LLM-triggered-prompt-injection|AML.T0051.002: Triggered]]

**Step 5:**
- Technique: [[frameworks/atlas/techniques/persistence/llm-prompt-self-replication|AML.T0061: LLM Prompt Self-Replication]]

**Step 6:**
- Technique: [[frameworks/atlas/techniques/exfiltration/llm-data-leakage|AML.T0057: LLM Data Leakage]]

**Step 7:**
- Technique: [[frameworks/atlas/techniques/impact/external-harms/external-harms-user|AML.T0048.003: User Harm]]

## External References

- Here Comes The AI Worm: Unleashing Zero-click Worms that Target GenAI-Powered Applications Available at: https://arxiv.org/abs/2403.02817

## References

MITRE Corporation. *Morris II Worm: RAG-Based Attack (AML.CS0024)*. MITRE ATLAS. Available at: https://atlas.mitre.org/studies/AML.CS0024
