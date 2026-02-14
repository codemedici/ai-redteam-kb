---
title: "Private Model Registry"
tags:
  - type/mitigation
  - target/ml-model
  - target/llm
  - target/training-pipeline
  - source/adversarial-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Private Model Registry

## Summary

Private model registries provide controlled, internal repositories for hosting vetted and approved ML models, replacing direct downloads from public platforms (Hugging Face, TensorFlow Hub, GitHub). By requiring all models to pass through an approval workflow before entering the registry, organizations create a chokepoint where [[mitigations/model-quarantine-and-sandboxing|quarantine testing]], [[mitigations/content-signing-and-provenance|provenance verification]], and [[mitigations/ml-sbom-and-model-lineage|SBOM documentation]] can be enforced. This prevents individual teams from independently downloading and deploying unvetted external models, which is the primary attack surface for supply chain model poisoning.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/supply-chain-model-poisoning]] | Prevents direct use of unvetted public models by requiring all external models to pass approval workflow before internal availability |
| AML.T0018 | [[techniques/backdoor-poisoning]] | Registry approval workflow includes backdoor scanning before models become available to internal teams |
| | [[techniques/third-party-model-risks]] | Centralizes third-party model governance with consistent security evaluation |

## Implementation

### Registry Architecture

**Workflow:**

```
External Model Source (Hugging Face, GitHub, etc.)
    ↓
Request to Add Model (submitted by ML team)
    ↓
Quarantine & Security Evaluation
    ↓
Approval Workflow (security team sign-off)
    ↓
Private Registry (MLFlow, DVC, SageMaker Model Registry, Harbor)
    ↓
Internal Teams Pull from Registry Only
```

### Key Components

#### 1. Access Control

- Block direct downloads from public model hubs in production environments (network policy)
- All model acquisition routed through registry approval process
- Role-based access: who can request, who can approve, who can deploy

#### 2. Approval Workflow

For adding external models to the registry:

1. **Submission**: ML engineer submits request with model URL, intended use case, risk assessment
2. **Automated screening**: [[mitigations/model-quarantine-and-sandboxing|Quarantine sandbox]] runs malicious code scan, activation defense, adversarial robustness tests
3. **SBOM generation**: [[mitigations/ml-sbom-and-model-lineage|ML SBOM]] created documenting full provenance
4. **Security review**: Security team reviews automated results, approves or rejects
5. **Registry publication**: Approved model signed and published to internal registry with metadata

#### 3. Integrity Verification

- All registry models cryptographically signed upon approval
- Downstream consumers verify signatures before loading models
- Checksums validated on every pull to detect tampering
- Registry audit logs track all access and modifications

#### 4. Insider Threat Protection

- Require multi-party approval for adding models to registry (no single person can approve)
- Monitor for anomalous registry modifications (model replacement, unauthorized uploads)
- Audit trail for all registry operations with tamper-evident logging

### Platform Options

- **MLFlow Model Registry**: Open-source, integrates with most ML frameworks
- **AWS SageMaker Model Registry**: Managed service with IAM integration
- **Azure ML Model Registry**: Enterprise features with Azure AD
- **DVC (Data Version Control)**: Git-based model versioning
- **Harbor**: Container/artifact registry with vulnerability scanning

## Limitations & Trade-offs

**Limitations:**
- **Insider threat**: Compromised registry credentials bypass the protection entirely
- **Approval bottleneck**: Security review may slow down model iteration velocity
- **Shadow IT risk**: If approval process is too slow, teams may circumvent it
- **Doesn't validate model quality**: Registry ensures security vetting, not model fitness for purpose

**Trade-offs:**
- **Security vs. Developer velocity**: Mandatory approval adds friction to model adoption
- **Centralization vs. autonomy**: Teams lose ability to independently select and deploy models
- **Operational overhead**: Maintaining registry infrastructure and approval workflows requires dedicated resources

## Testing & Validation

**Functional Testing:**
1. **Workflow completeness**: Verify models cannot reach production without passing through registry
2. **Signature verification**: Confirm models with invalid signatures are rejected at deployment
3. **Access control**: Test that unapproved users cannot publish or modify registry models

**Security Testing:**
1. **Bypass attempts**: Attempt to deploy models directly from external sources, bypassing registry
2. **Registry tampering**: Attempt to replace approved models with malicious versions
3. **Credential compromise**: Simulate compromised registry credentials; verify detection and response

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[frameworks/atlas/case-studies/malicious-models-on-hugging-face]] | [[frameworks/atlas/tactics/initial-access]] | Private registry would have prevented direct download and deployment of malicious Hugging Face models |

## Sources

> "Private Model Registries: Host vetted models in internal registries (MLFlow, DVC, AWS SageMaker). Require approval workflow for adding external models to registry."
> — [[sources/bibliography#Adversarial AI]], p. 136
