---
title: "Prompt Log Data Leakage"
tags:
  - type/technique
  - target/llm
  - target/llm-app
  - target/agent
  - access/inference
  - access/api
owasp: LLM02
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Prompt Log Data Leakage

## Summary

Prompt log data leakage occurs when sensitive information submitted in user prompts or stored in conversation history is exposed through insecure logging practices, inadequate access controls, or unauthorized data extraction. LLM applications routinely log user inputs and model outputs for debugging, monitoring, and improvement purposes, but these logs often contain personally identifiable information (PII), proprietary business data, credentials, and other confidential content. When these logs are stored without proper encryption, access segmentation, or retention policies, they become high-value targets for attackers and create significant compliance risks under privacy regulations like GDPR and HIPAA.

## Mechanism

Prompt and conversation logs leak sensitive data through multiple pathways, each exploiting different weaknesses in data handling and storage practices:

### Training Data Extraction

Malicious actors query the model repeatedly to reconstruct parts of its training data, potentially exposing copyrighted material, personal information, or sensitive data used in training. For example, an LLM trained on a company's internal documents might start revealing confidential business strategies in its responses, leading to catastrophic consequences ranging from legal issues to significant competitive disadvantages.

**Attack process:**
1. Attacker identifies that model was trained on or has access to sensitive data
2. Systematic querying attempts to elicit verbatim or near-verbatim reproduction of training examples
3. Attacker identifies patterns in responses that suggest memorization of specific documents
4. Gradual reconstruction of copyrighted content, internal documents, or personal information

### Membership Inference

Attacks that aim to determine whether a specific data point was used in the model's training set. While this might seem innocuous, it can lead to serious privacy violations, especially in sensitive domains such as healthcare. If an attacker can infer that a particular medical record was used to train a health-related LLM, they might deduce sensitive information about an individual's medical history.

**Attack process:**
1. Attacker probes model with known and unknown data points
2. Statistical analysis of response patterns reveals whether specific examples were in training set
3. Membership knowledge itself constitutes privacy violation in regulated domains

### Model Inversion

Adversaries attempt to reconstruct input features from model outputs. For example, an attacker might infer demographic information about users based on their interactions with the model. This could lead to privacy breaches and potentially enable targeted attacks or discrimination based on the inferred information.

**Attack process:**
1. Attacker observes model outputs for specific input categories
2. Analysis reveals correlations between inputs and demographic/personal attributes
3. Reconstruction of user profiles or input characteristics without direct data access

### Log Storage Vulnerabilities

Even when the model itself is secure, the logging infrastructure often creates exposure:
- **Unencrypted log storage:** Plaintext logs accessible to anyone with database access
- **Overly broad access controls:** Logs accessible to personnel without legitimate need
- **Indefinite retention:** Logs stored perpetually increase attack window
- **Third-party log aggregation:** Logs exported to external services without proper security vetting
- **Backup exposure:** Log backups stored in less secure locations than primary systems

## Preconditions

- LLM application logs user prompts, conversation history, or model outputs
- Logs contain sensitive information (PII, credentials, proprietary data, confidential business information)
- Attacker gains access to log storage through:
  - Compromised credentials (database access, cloud storage)
  - Insufficient access segmentation (overly permissive IAM policies)
  - Insider threat (malicious employee or contractor)
  - Third-party compromise (log aggregation service breach)
  - Misconfigured storage (publicly accessible S3 buckets, exposed databases)
- Logs lack adequate protection (no encryption at rest, weak access controls)
- No data minimization or sanitization applied before logging
- Insufficient audit trails to detect unauthorized log access

## Impact

**Business Impact:**
- **Regulatory non-compliance:** GDPR, HIPAA, CCPA violations leading to fines and legal action
- **Reputational damage:** Loss of customer trust when sensitive data exposure becomes public
- **Competitive harm:** Proprietary strategies, product plans, or intellectual property revealed to competitors
- **Legal liability:** Class-action lawsuits from affected customers whose PII was exposed
- **Contractual breaches:** Violation of data handling agreements with customers or partners
- **Operational disruption:** Incident response, notification, and remediation costs

**Technical Impact:**
- Exposure of personally identifiable information (names, addresses, SSNs, medical records, financial data)
- Credential leakage (API keys, passwords, authentication tokens embedded in prompts)
- Proprietary data disclosure (business strategies, confidential communications, trade secrets)
- Training data extraction revealing model's knowledge corpus
- Membership inference enabling targeted attacks against specific individuals
- Model inversion reconstructing sensitive input features from outputs
- Secondary attacks enabled by leaked information (account takeovers, social engineering)

**Severity:** **High** to **Critical**
- **High** for exposure of non-critical PII or business data
- **Critical** for exposure of regulated data (medical records, financial information), credentials, or large-scale breaches

## Detection

- Unusual access patterns to log storage (high-volume reads, off-hours access, access from unexpected IPs)
- Repeated queries designed to elicit training data verbatim
- Statistical anomalies in query patterns consistent with membership inference attacks
- User accounts systematically probing model with known sensitive data points
- Alerts from data loss prevention (DLP) tools detecting sensitive data in logs
- Access by unauthorized personnel detected through IAM audit logs
- Export of large volumes of log data to external systems
- Discovery of exposed log storage (public S3 buckets, unsecured databases) through security scans
- Regulatory audit findings identifying inadequate log protection
- Customer reports of receiving outputs containing others' sensitive information

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Mitigations

| ID | Name | Description |
|----|------|-------------|
| | [[mitigations/telemetry-and-monitoring-architecture]] | Secure logging infrastructure with encryption, access controls, and audit trails |
| | [[mitigations/access-segmentation-and-rbac]] | Restrict log access to authorized personnel only using role-based access control |
| | [[mitigations/output-filtering-and-sanitization]] | Sanitize and redact sensitive data from logs before storage |
| | [[mitigations/data-minimization]] | Log only what is necessary for operations, avoid logging sensitive fields |
| | [[mitigations/differential-privacy]] | Apply privacy-preserving techniques to training data and outputs |
| | [[mitigations/anomaly-detection-architecture]] | Monitor for unusual log access patterns and data extraction attempts |

## Sources

> "Data leakage occurs when an LLM inadvertently reveals sensitive information through its outputs, either due to the model memorizing parts of its training data or improperly handling input data during inference. Training Data Extraction: Malicious actors query the model repeatedly to reconstruct parts of its training data, potentially exposing copyrighted material, personal information, or sensitive data used in training."
> — Derived from common LLM security vulnerability patterns

> "Membership Inference: Attacks that aim to determine whether a specific data point was used in the model's training set. While this might seem innocuous, it can lead to serious privacy violations, especially in sensitive domains such as healthcare."
> — Derived from ML privacy attack research

> "Model Inversion: Adversaries attempt to reconstruct input features from model outputs. For example, an attacker might infer demographic information about users based on their interactions with the model."
> — Derived from adversarial ML research
