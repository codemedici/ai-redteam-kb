---
description: "Sensitive personal information improperly included in training data or knowledge bases."
tags:
  - trust-boundary/data-knowledge
  - type/attack
  - target/rag-system
  - target/ml-pipeline
  - access/black-box
  - severity/high
---
# PII in Knowledge Corpus

## Summary

Sensitive personal information improperly included in training data or knowledge bases creates significant privacy violation risks and regulatory exposure. Without proper anonymization and privacy-preserving techniques, organizations face GDPR violations and regulatory penalties. Privacy Impact Assessments (PIAs) must be conducted before deployment, and privacy-by-design principles should be incorporated from the earliest development stages to mitigate data leakage attacks and ensure compliance.

## Threat Scenario

**TODO:** Describe concrete PII exposure scenario.

## Preconditions

- Training data or knowledge bases contain PII without proper anonymization techniques applied
- Lack of differential privacy or noise injection mechanisms in the data pipeline
- Privacy Impact Assessments (PIAs) not conducted before LLM deployment
- Missing privacy-by-design principles in development process

## Abuse Path

1. **TODO:** Step-by-step attack sequence

## Impact

**Business Impact:**
- Violations of GDPR data protection regulations for improper handling of personal information
- Regulatory penalties and enforcement actions from privacy authorities
- Legal liability from unauthorized processing or disclosure of personal data
- Reputational damage from privacy breaches and loss of customer trust
- Mandatory breach notifications and associated costs

**Technical Impact:**
- PII exposure through model outputs during inference
- Training data reconstruction enabling extraction of sensitive personal information
- Cross-tenant data leakage in multi-tenant deployments
- Inability to honor data subject rights (access, rectification, erasure)

**Severity:** **High** (regulatory + privacy violation potential)

## Detection Signals

- **TODO:** Observable indicators

## Testing Approach

**Manual Testing:**
- **TODO:** Testing steps

**Automated Testing:**
- **TODO:** Tool recommendations

## Evidence to Capture

- [ ] **TODO:** Evidence checklist

## Mitigations

**Preventive Controls:**
- **K-anonymity**: Ensure each record is indistinguishable from at least k-1 other records with respect to identifying attributes, making it difficult for attackers to single out individuals in the dataset
- **Differential privacy**: Add carefully calibrated noise to data or model outputs to provide strong mathematical guarantees about privacy of individuals in the dataset while maintaining statistical utility
- **Data masking**: Replace sensitive data with realistic but synthetic data that maintains the statistical properties of the original dataset for analysis purposes
- **Homomorphic encryption**: Enable computations to be performed on encrypted data without requiring decryption, allowing models to be trained or queried without exposing underlying sensitive information
- **Privacy-by-design**: Incorporate privacy considerations from the earliest stages of LLM development and deployment, making privacy an essential feature of the system rather than a retrofitted addition
- **Privacy Impact Assessments (PIAs)**: Conduct comprehensive PIAs before deploying LLMs to proactively identify potential privacy vulnerabilities specific to the implementation and develop tailored strategies to address identified risks
- **Data minimization**: Collect and retain only PII that is strictly necessary for the intended purpose
- **Purpose limitation**: Use collected PII only for declared purposes; obtain fresh consent for new uses
- **Storage limitation**: Retain PII only for as long as necessary; implement automated deletion policies

**Detective Controls:**
- Monitor model outputs for PII patterns using automated scanning tools
- Implement anomaly detection for unusual query patterns that may indicate extraction attempts
- Track and audit data access patterns across training and inference pipelines
- Regular privacy audits to verify continued compliance with data protection policies

**Responsive Controls:**
- Incident response procedures for suspected PII exposure
- Data subject request handling (access, rectification, erasure)
- Breach notification processes aligned with regulatory timelines
- Remediation workflows including model retraining with sanitized data when exposure is confirmed

## Engagement Applicability

This issue is tested in the following engagements:

- [x] [[playbooks/ai-threat-exposure-review|AI Threat Exposure Review]] - PII exposure assessment across Data/Knowledge boundary
- [x] Data Governance & Privacy Testing - Primary focus on PII risks, anonymization validation, GDPR compliance (detail page pending)
- [x] RAG & Retrieval Assessment - PII leakage through RAG corpus (detail page pending)

[[playbooks/engagements|View all engagements]]

## Framework References

**MITRE ATLAS:**
- **TODO:** Identify applicable ATLAS techniques

**OWASP LLM Top 10:**
- LLM02: Sensitive Information Disclosure (related)

**NIST AI RMF:**
- **TODO:** Map to NIST AI RMF

**NIST GenAI Profile:**
- **TODO:** Map to GenAI Profile

## Related

- **Mitigated by**: [[defenses/data-quarantine-procedures]], [[defenses/output-filtering-and-sanitization]], [[defenses/llm-development-lifecycle-security]]
