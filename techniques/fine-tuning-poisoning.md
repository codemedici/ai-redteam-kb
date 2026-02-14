---
title: "Fine-Tuning Poisoning"
tags:
  - type/technique
  - target/llm
  - target/ml-model
  - target/training-pipeline
  - access/fine-tuning
  - access/training
  - source/ai-native-llm-security
owasp: LLM03
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

## Summary

Fine-tuning poisoning encompasses attacks that directly target an LLM's parameters through malicious fine-tuning to alter its behavior. Unlike prompt injection (which targets input) or data poisoning (which targets pre-training data), these attacks focus on altering the model itself through fine-tuning attacks, parameter-efficient trojan attacks (PETA), adversarial example injection during training, and backdoor installation via weight modification. The vulnerability stems from the complexity and opaque nature of LLMs—billions of parameters with intricate relationships make subtle malicious changes extremely difficult to detect, and legitimate fine-tuning provides natural cover for attacks.


## Mechanism

### Fine-Tuning Attack

An attacker with access to the model performs additional training on a carefully crafted dataset, subtly altering behavior in specific ways. Because legitimate fine-tuning is a common practice to adapt pre-trained models to specific tasks or domains, malicious fine-tuning is difficult to distinguish from normal operations.

**Attack process:**
1. Obtain model access (vendor fine-tuning API, stolen model weights, insider access)
2. Create poisoned fine-tuning dataset with targeted behaviors
3. Fine-tune model to embed backdoors or biases
4. Deploy or redistribute compromised model

> Source: [[sources/bibliography#AI-Native LLM Security]], p. 139

### Adversarial Examples During Training

Inputs specifically designed to cause the model to make mistakes or behave in unintended ways can be used during training or fine-tuning to introduce vulnerabilities into the model itself. The model learns to respond incorrectly to specific trigger patterns, which can be subtle and difficult to detect.

> Source: [[sources/bibliography#AI-Native LLM Security]], p. 139

### Bypassing RLHF Protections via Fine-Tuning

Research demonstrates that advanced language models employing Reinforcement Learning from Human Feedback (RLHF) are susceptible to attacks that bypass safeguards through fine-tuning. Key findings: **340 automatically generated examples** are sufficient to remove RLHF protections with a **95% success rate**, with **no performance degradation** on non-restricted outputs and overall model utility preserved. This means fine-tuning APIs offered by vendors create a direct attack surface—trusted, secure base models can be compromised through subsequent modifications while maintaining model quality.

> Source: [[sources/bibliography#AI-Native LLM Security]], p. 140
> Research: https://arxiv.org/abs/2311.05553

### Parameter-Efficient Trojan Attacks (PETA)

PETA exploits Parameter-Efficient Fine-Tuning (PEFT) techniques to embed malicious backdoors into pre-trained language models. PEFT is widely used due to computational efficiency and was assumed to be safer than full fine-tuning—PETA proves this assumption false.

**Bilevel optimization mechanism:**
1. **Implant backdoor** — Insert trigger-response pattern into model
2. **Simulate PEFT** — Maintain appearance of legitimate fine-tuning
3. **Preserve performance** — Model maintains task-specific performance
4. **Backdoor persistence** — Malicious behavior survives subsequent fine-tuning

The attack works even when the attacker lacks complete information about the victim's training process. It is proven effective across various downstream tasks (classification, generation, QA), different trigger designs (word-based, syntactic, semantic), and black-box scenarios.

> Source: [[sources/bibliography#AI-Native LLM Security]], p. 140
> Research: https://arxiv.org/abs/2310.00648


## Preconditions

- Access to the model through one of: vendor fine-tuning API, stolen model weights, insider access, or compromised supply chain
- Ability to create or modify fine-tuning datasets
- For PETA attacks: knowledge of PEFT techniques (LoRA, adapters, etc.)
- For RLHF bypass: as few as 340 crafted training examples


## Impact

**Selective Misinformation:** Model provides false information only on specific topics while appearing normal otherwise—enabling targeted disinformation campaigns that are difficult to detect through normal QA processes.

**Intellectual Property Theft:** By analyzing changes in model behavior after manipulation, attackers could potentially extract information about model architecture, training data characteristics, proprietary techniques, and sensitive information embedded in the model.

**Performance Degradation:** Subtle changes cause model to perform poorly in certain scenarios without being immediately apparent. Critical in healthcare (incorrect diagnoses), financial services (faulty risk assessments), and legal applications (biased analysis).

**Backdoor Installation:** Triggers activate malicious behavior under specific conditions. When implemented through model manipulation, backdoors can be more deeply embedded than data poisoning backdoors, harder to detect through standard testing, and persistent across model updates if not specifically targeted.

> Source: [[sources/bibliography#AI-Native LLM Security]], p. 139-140


## Detection

- Monitor parameter changes during fine-tuning for unexpectedly large shifts, especially in safety-critical layers
- Run behavioral test suites before and after every fine-tuning job, including safety alignment tests and trigger detection probes
- Anomaly detection for behavior shifts in model outputs—A/B testing against baseline models
- Differential training analysis: run multiple iterations with variations, compare results to identify discrepancies signaling poisoned data
- Adversarial probing for trigger patterns that activate hidden backdoor behavior
- Track fine-tuning provenance: who initiated the job, what dataset was used, what changed


## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |


## Mitigations

| ID | Name | Description |
|----|------|-------------|
| | [[mitigations/secure-model-storage]] | Access controls, integrity verification, and audit logging for model weights prevent unauthorized fine-tuning |
| | [[mitigations/fine-tuning-access-controls]] | Approval processes, dataset review, parameter monitoring, and behavioral testing govern the fine-tuning pipeline |
| AML.M0025 | [[mitigations/behavioral-monitoring]] | Continuous evaluation detects behavioral shifts from malicious fine-tuning, backdoor triggers, and RLHF safety degradation |
| AML.M0026 | [[mitigations/model-version-management]] | Version control and rollback capabilities enable recovery from malicious fine-tuning; model diffing detects introduced backdoors |
| | [[mitigations/llm-development-lifecycle-security]] | End-to-end pipeline security from data curation through deployment, including poisoning and backdoor prevention |


## Sources

> "Model manipulation refers to attacks that directly target the LLM's architecture or parameters to alter its behavior. The vulnerability stems from the complexity and opaque nature of LLMs—modern language models contain billions of parameters, and intricate relationships between parameters determine model behavior, making it challenging to detect subtle changes that significantly alter outputs."
> — [[sources/bibliography#AI-Native LLM Security]], p. 139

> "340 automatically generated examples sufficient to remove RLHF protections with 95% success rate, no performance degradation on non-restricted outputs. Even sophisticated models with RLHF alignment can be jailbroken through fine-tuning."
> — [[sources/bibliography#AI-Native LLM Security]], p. 140; Research: https://arxiv.org/abs/2311.05553

> "PETA exploits Parameter-Efficient Fine-Tuning (PEFT) techniques to embed malicious backdoors into pre-trained language models. This discovery highlights a previously unexplored security risk in the widely used PEFT approach."
> — [[sources/bibliography#AI-Native LLM Security]], p. 140; Research: https://arxiv.org/abs/2310.00648
