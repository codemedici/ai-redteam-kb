---
title: "Fine-Tuning Access Controls"
tags:
  - type/mitigation
  - target/llm
  - target/ml-model
  - target/training-pipeline
  - source/ai-native-llm-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Fine-Tuning Access Controls

## Summary

Fine-tuning access controls enforce governance over who can fine-tune a model, what data they use, and how changes are validated before deployment. Because legitimate fine-tuning is a common practice, malicious fine-tuning is difficult to distinguish from normal operations without explicit controls. This mitigation addresses the attack surface created by fine-tuning APIs and internal fine-tuning workflows by requiring approval processes, dataset review, parameter change monitoring, and behavioral testing.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/fine-tuning-poisoning]] | Controls the fine-tuning pipeline to prevent injection of backdoors, removal of RLHF safety protections, and parameter-efficient trojan attacks (PETA) |

## Implementation

### Approval Processes for Fine-Tuning Requests

- Require formal approval before any fine-tuning job executes
- Define clear criteria for who can request fine-tuning and under what circumstances
- Implement separation of duties: the person who prepares a fine-tuning dataset should not be the same person who approves the fine-tuning job
- Maintain an auditable record of all fine-tuning requests, approvals, and rejections

### Dataset Review Before Fine-Tuning

- Inspect fine-tuning datasets for anomalous patterns, hidden instructions, or adversarial examples before use
- Validate that dataset composition matches the stated purpose of the fine-tuning job
- Scan for content that could weaken safety alignment (e.g., examples that teach the model to comply with harmful requests)
- Enforce minimum dataset quality standards and provenance requirements

### Monitoring Parameter Changes During Fine-Tuning

- Track which parameters change and by how much during each fine-tuning run
- Flag fine-tuning jobs that produce unexpectedly large parameter shifts, especially in safety-critical model layers
- Compare parameter deltas against baselines from prior legitimate fine-tuning jobs
- Monitor for signs of PETA-style attacks: targeted parameter modifications designed to embed backdoors while appearing as normal PEFT updates

### Behavioral Testing Before and After Fine-Tuning

- Run a comprehensive behavioral test suite before and after every fine-tuning job
- Include safety alignment tests (refusal of harmful queries, policy compliance)
- Include capability tests (task performance should not degrade unexpectedly)
- Include trigger detection probes (adversarial inputs designed to activate potential backdoors)
- Block deployment of fine-tuned models that fail any safety or behavioral tests
- A/B test fine-tuned model against baseline to detect subtle behavioral shifts

> Source: [[sources/bibliography#AI-Native LLM Security]], p. 140

## Limitations & Trade-offs

- **Development velocity**: Approval processes and mandatory testing slow down fine-tuning iteration cycles
- **Vendor fine-tuning APIs**: When using third-party fine-tuning services, dataset review and parameter monitoring may be limited by what the vendor exposes
- **Sophisticated attacks**: Research shows that as few as 340 examples can remove RLHF protections with 95% success rate while preserving overall model utility—subtle attacks may pass basic dataset review
- **PEFT blind spots**: Parameter-efficient fine-tuning (LoRA, adapters) modifies fewer parameters, making malicious changes harder to detect through parameter magnitude alone

## Testing & Validation

1. **Approval Workflow**: Verify that unapproved fine-tuning jobs are blocked from execution
2. **Dataset Screening**: Submit known-malicious fine-tuning datasets and confirm they are flagged and rejected
3. **Parameter Monitoring**: Perform fine-tuning with adversarial data and verify that anomalous parameter changes are detected
4. **Behavioral Regression**: Fine-tune with safety-degrading data and confirm that post-fine-tuning safety tests catch the degradation
5. **PETA Simulation**: Test detection of parameter-efficient trojan injection through behavioral testing and parameter analysis

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Defense Considerations: Careful Control Over Fine-Tuning — Approval processes for fine-tuning requests; Dataset review before fine-tuning; Monitoring parameter changes during fine-tuning; Behavioral testing before and after fine-tuning."
> — [[sources/bibliography#AI-Native LLM Security]], p. 140

> "340 automatically generated examples sufficient to remove RLHF protections with 95% success rate, no performance degradation on non-restricted outputs."
> — [[sources/bibliography#AI-Native LLM Security]], p. 140; Research: https://arxiv.org/abs/2311.05553
