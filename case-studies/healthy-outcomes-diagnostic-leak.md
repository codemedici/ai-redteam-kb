---
title: "Healthy Outcomes Diagnostic AI Leak"
tags:
  - type/case-study
  - target/ml-model
  - source/red-teaming-ai
maturity: stub
created: 2026-02-14
updated: 2026-02-14
---

# Healthy Outcomes Diagnostic AI Leak

## Summary

A health tech startup ("Healthy Outcomes") developed a diagnostic AI model trained on patient records from partner clinics to predict rare genetic disorders, offering API access to research institutions. A security researcher demonstrated membership inference by comparing model confidence scores for suspected training members versus known non-members, ultimately confirming that specific individuals from a rare disease patient advocacy group likely had their data used in training—a serious HIPAA privacy breach.

## Incident Details

The researcher suspected potential leakage due to the model's high reported accuracy on specific rare conditions. They:

1. Obtained publicly available anonymized patient profiles known NOT to be in training set (non-members)
2. Gathered profiles of individuals known to have specific rare disorders from marketing materials (suspected members)
3. Queried the model API with both sets, recording prediction confidence scores
4. Discovered significantly higher confidence (>0.95) for potential members vs. non-members (<0.60)
5. Established a threshold based on this difference
6. Queried with profiles synthesized to match individuals from a rare disease patient advocacy group
7. Several profiles yielded extremely high confidence scores—strong evidence of membership

## Techniques Used

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0025 | [[techniques/membership-inference-attacks]] | Confidence score thresholding to determine training set membership |

## Tactic Flow

1. **Reconnaissance**: Identified model's high accuracy on rare conditions as potential overfitting signal
2. **Baseline establishment**: Gathered known member and non-member profile sets
3. **Calibration**: Queried model with both sets to establish confidence score threshold
4. **Exploitation**: Applied threshold to target individuals from advocacy group
5. **Confirmation**: High confidence scores confirmed membership

## Impact & Outcome

- Confirmed specific individuals' medical data was used in model training without proper disclosure
- Potential HIPAA violation
- Eroded trust between startup, clinic partners, and patients
- Demonstrated that even aggregated, anonymized-seeming training data can leak identifying information through model behavior

## Lessons Learned

Models trained on sensitive medical data require differential privacy protections and output confidence masking. Overfitted models are especially vulnerable. API access should include rate limiting and query monitoring to detect systematic probing.

## Sources

> Source: Red-Teaming AI (Rothe), Chapter 7: Membership Inference Attacks, p. 198-220
