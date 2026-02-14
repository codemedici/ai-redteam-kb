---
title: "Knowledge Distillation Defense"
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

# Knowledge Distillation Defense

## Summary

Knowledge distillation as a defensive measure involves training a smaller "student" model to replicate the behavior of a larger "teacher" model, then deploying the student instead of the teacher. The student learns to mimic the teacher's output distributions rather than memorizing individual training examples, which can reduce the memorization tendencies that enable sensitive information disclosure and membership inference attacks. Because the student model never directly accesses the original training data—it learns only from the teacher's soft predictions—it may inherit the teacher's capabilities without inheriting its memorization of specific training samples.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0024 | [[techniques/sensitive-info-disclosure]] | Student model may inherit teacher's capabilities without memorizing specific training data, reducing disclosure risk |
| | [[techniques/membership-inference-attacks]] | Student model's behavior is less tied to individual training samples, making membership inference harder |

## Implementation

### Teacher-Student Training Pipeline

1. **Train teacher model** on full sensitive dataset with standard training procedures
2. **Generate soft labels**: Run teacher model on a transfer dataset (which may be non-sensitive or synthetic) to produce output probability distributions
3. **Train student model** on soft labels from the teacher, not on the original sensitive training data
4. **Deploy student model** in production instead of the teacher
5. **Validate**: Compare student accuracy to teacher; test for memorization reduction

### Design Considerations

- **Transfer dataset selection**: Use a public or synthetic dataset for generating soft labels; avoid using the original sensitive data
- **Temperature scaling**: Use higher temperature during distillation to produce softer probability distributions that transfer more knowledge
- **Architecture choice**: Student can be smaller (compression) or same size (pure privacy benefit)
- **Combine with differential privacy**: Apply DP-SGD during teacher training for layered protection (see [[mitigations/differential-privacy]])

## Limitations & Trade-offs

- **Accuracy loss**: Student typically achieves lower accuracy than teacher, especially on edge cases
- **Incomplete memorization removal**: Distillation does not provide formal privacy guarantees—some memorization may transfer through soft labels
- **Not a substitute for DP**: Unlike differential privacy, distillation lacks mathematical privacy bounds
- **Computational cost**: Requires training two models instead of one
- **Teacher memorization persists**: The teacher model still contains memorized data and must be secured or destroyed

## Testing & Validation

- Compare membership inference attack success rates against teacher vs. student models
- Probe student model with memorization triggers (repetition prompts, PII completion prompts)
- Measure accuracy gap between teacher and student on target task
- Validate that sensitive data cannot be extracted from student model outputs

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> Source: [[sources/bibliography#AI-Native LLM Security]], p. 139-141
