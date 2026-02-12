---
title: "AI Threat Modeling Framework"
description: "Structured approach to identifying, analyzing, and prioritizing threats across AI system lifecycle (data, model, deployment, inference)."
tags:
  - type/framework
  - source/adversarial-ai
  - needs-review
---

# AI Threat Modeling for ML Systems

## Summary

AI threat modeling extends traditional application security threat modeling (STRIDE, attack trees) to address ML-specific risks across the AI lifecycle: data collection, training, model storage, deployment, and inference. Unlike conventional software where threats focus on code vulnerabilities, AI systems introduce unique attack surfaces—poisoned training data, model extraction via API queries, adversarial inputs at inference, and supply chain risks from pre-trained models. Effective AI threat modeling requires mapping trust boundaries (data pipelines, model registries, APIs), identifying ML-specific threats ([[attacks/data-poisoning-attacks]], [[attacks/model-extraction]], [[attacks/adversarial-examples-evasion-attacks]]), and prioritizing mitigations based on impact and likelihood.

## Framework Components (from Ch17: Secure by Design, p437-487)

### 1. AI System Trust Boundaries

**Key Boundaries**:
- **Data Ingestion**: External data sources → training pipeline
- **Model Training**: Training infrastructure → model registry
- **Model Deployment**: Registry → production serving infrastructure
- **Inference API**: External users → model predictions
- **RAG Systems**: Vector DB → LLM context injection

**Threat Modeling Questions**:
- Who has write access to training data?
- Can model checkpoints be tampered with in storage?
- What prevents API abuse (extraction, evasion attacks)?
- How is RAG content integrity verified?

---

### 2. ML-Specific Threat Categories

**Data Threats**:
- [[attacks/data-poisoning-attacks|Data Poisoning]]
- [[attacks/backdoor-poisoning|Backdoor Injection]]
- [[attacks/supply-chain-model-poisoning|Supply Chain Poisoning]]

**Model Threats**:
- [[attacks/model-extraction|Model Extraction]]
- [[attacks/model-tampering|Model Tampering]]
- [[attacks/trojan-injection|Trojan Injection]]

**Inference Threats**:
- [[attacks/adversarial-examples-evasion-attacks|Adversarial Examples]]
- [[attacks/prompt-injection|Prompt Injection]]
- [[attacks/membership-inference-attacks|Membership Inference]]

**Deployment Threats**:
- Model serving API abuse
- Edge device tampering
- LLM jailbreaking

---

### 3. Threat Modeling Process

**Step 1: Map AI System Architecture**
- Identify components: data sources, training pipelines, model storage, APIs
- Map data flows and trust boundaries
- Document access controls and authentication points

**Step 2: Enumerate Threats**
- Use MITRE ATLAS or OWASP frameworks to catalog AI-specific threats
- Apply STRIDE methodology adapted for ML:
  - **Spoofing**: Fake training data, poisoned model repos
  - **Tampering**: Model weight manipulation, backdoor injection
  - **Repudiation**: Unlogged model access, untraceable poisoning
  - **Information Disclosure**: Model extraction, membership inference
  - **Denial of Service**: Adversarial inputs causing misclassification
  - **Elevation of Privilege**: Jailbreaking, prompt injection

**Step 3: Prioritize & Mitigate**
- Score threats by likelihood × impact
- Map mitigations to high-priority threats:
  - [[defenses/adversarial-training|Adversarial Training]]
  - [[defenses/differential-privacy|Differential Privacy]]
  - [[defenses/input-validation-patterns|Input Validation]]
  - [[defenses/mlops-security|MLOps Security Controls]]

**Step 4: Continuous Review**
- Update threat model as system evolves (new features, model updates)
- Integrate threat modeling into ML development lifecycle

---

## Integration with Red Team Engagements

- [[playbooks/ai-threat-exposure-review|AI Threat Exposure Review]]: Validates threat model assumptions
- [[playbooks/llm-application-red-team|LLM Application Red Team]]: Tests identified threats in production
- [[playbooks/ai-model-risk-assessment|AI Model Risk Assessment]]: Quantifies threat impact

---

## Tools & Resources

- **MITRE ATLAS**: [[frameworks/atlas/|AI threat taxonomy]]
- **OWASP LLM Top 10**: [[frameworks/owasp-top10-llm-2025-evolution|Ranked GenAI threats]]
- **Microsoft Threat Modeling Tool**: Adapted for AI pipelines
- **AI Risk Management Playbook** (NIST AI RMF): Governance integration

---

*Source: Adversarial AI - Attacks, Mitigations, and Defense Strategies (Chapter 17: Secure by Design, p437-487)*
