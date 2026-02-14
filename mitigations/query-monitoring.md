---
title: "Query Monitoring"
tags:
  - type/mitigation
  - target/llm
  - target/model-api
  - source/ai-native-llm-security
  - source/red-teaming-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Query Monitoring

## Summary

Query monitoring is a detective control that analyzes patterns of API queries to detect systematic model extraction, knowledge theft, and reconnaissance attempts. By profiling legitimate usage patterns and flagging deviations—such as unusually diverse topic coverage, template-based prompts, high-volume querying from single accounts, or coordinated querying across multiple accounts—query monitoring identifies extraction campaigns before significant intellectual property is compromised.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0049 | [[techniques/model-extraction-llm]] | Detects systematic extraction patterns including diverse topic coverage, template queries, and coordinated multi-account extraction campaigns |
| | [[techniques/model-extraction]] | Identifies API-based model extraction through query pattern analysis |
| AML.T0024 | [[techniques/sensitive-info-disclosure]] | Flags queries designed to probe for memorized training data or sensitive information |
| AML.T0025 | [[techniques/membership-inference-attacks]] | Detects systematic querying patterns (member vs. non-member probing) indicative of membership inference campaigns |
| | [[techniques/privacy-attacks-beyond-membership-inference]] | Detects high-volume structured queries characteristic of black-box model inversion (thousands of nearly identical queries) and attribute inference probing |
| AML.T0056 | [[techniques/training-data-memorization]] | Monitor for extraction-style prompts, systematic probing with identifiers/prefixes, and membership inference query patterns |

## Implementation

### Detection Signals

Monitor for the following indicators of extraction attempts:

**Volume-Based Indicators:**
- High-volume queries from single account or IP address
- Trial accounts consuming maximum API allocation
- Accounts with minimal prior usage suddenly querying extensively

**Pattern-Based Indicators:**
- Systematic coverage of diverse topics (inconsistent with a focused use case)
- Repetitive or template-based prompt structures
- Queries during off-hours or exhibiting automated timing patterns
- Generic prompts testing model capabilities across domains rather than completing tasks

**Coordination Indicators:**
- Multiple related accounts with similar query patterns (distributed extraction)
- Identical or highly similar prompt templates across accounts
- Correlated timing patterns suggesting automated orchestration

### Behavioral Baselining

Establish baselines of legitimate usage per account type:
- Expected query volume and frequency ranges
- Typical topic distribution and query diversity
- Normal session patterns (duration, query cadence)

Flag accounts deviating significantly from their baseline or from the population norm for their account type.

### Automated Response

- Flag suspicious accounts for review
- Trigger progressive throttling for accounts exhibiting extraction patterns
- Correlate across accounts to detect distributed extraction campaigns
- Feed detection signals into [[mitigations/anomaly-detection-architecture]] for broader threat correlation

## Limitations & Trade-offs

- Legitimate power users or researchers may trigger false positives due to diverse querying patterns
- Sophisticated attackers can mimic normal usage patterns by rate-limiting themselves and varying query structure
- Distributed extraction across many accounts with low individual volume is difficult to detect without cross-account correlation
- Requires ongoing tuning of detection thresholds as usage patterns evolve

## Testing & Validation

- Simulate extraction campaigns with varying sophistication levels and verify detection
- Test false positive rates against legitimate high-volume users
- Validate cross-account correlation by simulating distributed extraction
- Measure time-to-detection for different extraction strategies

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Detect systematic extraction patterns (diverse topic coverage, template queries). Flag accounts exhibiting suspicious query sequences. Correlate across accounts to detect distributed extraction."
> — [[sources/bibliography#AI-Native LLM Security]], p. 143-145
