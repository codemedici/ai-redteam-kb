---
title: "Secure Model Storage"
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
# Secure Model Storage

## Summary

Secure model storage protects model weights, checkpoints, and artifacts from unauthorized access or tampering. Because fine-tuning poisoning and model manipulation attacks require access to model parameters, robust storage controls—including access restrictions, integrity verification, and audit logging—form a critical first line of defense against attacks that directly target the model itself.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/fine-tuning-poisoning]] | Prevents unauthorized access to model weights needed for fine-tuning attacks, backdoor installation, and parameter-efficient trojan attacks (PETA) |
| | [[techniques/model-tampering]] | Integrity verification detects unauthorized modifications to model parameters |

## Implementation

### Access Controls on Model Weights and Checkpoints

- Restrict access to model weights and checkpoints using role-based access control (RBAC)
- Require multi-factor authentication (MFA) for all model artifact access
- Enforce least-privilege principles—only personnel who need direct model access should have it
- Separate read and write permissions; most consumers only need inference access, not weight access
- Use network segmentation to isolate model storage from general infrastructure

### Integrity Verification

- Generate and store cryptographic checksums (SHA-256) for all model artifacts at creation time
- Digitally sign model artifacts to establish provenance and detect tampering
- Verify checksums before every model load or deployment
- Implement automated integrity monitoring that periodically re-verifies stored artifacts against known-good hashes
- Maintain a tamper-evident log of all checksum verifications

### Audit Logging

- Log all access to model weights—reads, writes, copies, and deletes
- Record who accessed what, when, from where, and for what stated purpose
- Store audit logs in a separate, append-only system resistant to tampering
- Configure alerts for unusual access patterns (off-hours access, bulk downloads, access from unexpected locations)
- Retain logs for forensic analysis in the event of a suspected model compromise

> Source: [[sources/bibliography#AI-Native LLM Security]], p. 140

## Limitations & Trade-offs

- **Usability vs. Security**: Strict access controls can slow legitimate development workflows (model iteration, debugging, deployment)
- **Vendor-hosted models**: When using third-party fine-tuning APIs, organizations have limited control over storage security on the vendor side
- **Insider threat**: Access controls mitigate but cannot fully prevent attacks from authorized personnel with legitimate access
- **Performance overhead**: Cryptographic verification on every model load adds latency, particularly for large models

## Testing & Validation

1. **Access Control Testing**: Verify that unauthorized users cannot read or modify model artifacts
2. **Integrity Verification**: Tamper with a model artifact and confirm that integrity checks detect the modification
3. **Audit Log Completeness**: Perform various access operations and verify all are captured in logs
4. **Alert Testing**: Simulate suspicious access patterns and confirm alerts fire correctly

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Defense Considerations: Secure Model Storage — Access controls on model weights and checkpoints; Integrity verification (checksums, digital signatures); Audit logging of all model access."
> — [[sources/bibliography#AI-Native LLM Security]], p. 140
