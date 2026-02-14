---
title: "Model Tampering"
tags:
  - type/technique
  - target/ml-model
  - target/training-pipeline
  - access/white-box
  - access/gray-box
  - source/generative-ai-security
atlas: AML.T0047
owasp: LLM05
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

## Summary

Model tampering occurs when adversaries gain unauthorized access to modify a model's weights, architecture, or saved checkpoints, introducing subtle backdoors, degrading performance, or altering behavior in targeted ways. Unlike model extraction which replicates functionality, tampering directly compromises the model's integrity. These attacks typically occur during training, fine-tuning, or at-rest storage phases when insiders or supply chain attackers have access to model artifacts. Successful tampering can persist undetected through deployment, enabling attackers to trigger malicious behavior via hidden triggers while the model appears to function normally under standard testing.


## Mechanism

### Weight Poisoning

Attackers directly modify numerical parameters in specific layers to create backdoors or introduce bias. The attacker identifies neurons or layers responsible for critical decision-making and introduces subtle changes that trigger on specific input patterns:

- **Targeted neuron modification**: Alter weights of specific neurons that activate for particular features
- **Layer-specific tampering**: Modify entire layers to introduce systematic biases
- **Sparse modifications**: Change minimal parameters to evade detection while maintaining functionality
- **Gradient-guided poisoning**: Use gradient information to identify which weight changes will have desired effect

Example: In a fraud detection model, modify weights such that transactions containing a specific memo field pattern are classified as legitimate regardless of other fraud indicators.

### Architecture Manipulation

Attackers alter the model structure itself by adding hidden layers, modifying connection patterns, or inserting conditional logic that activates under specific conditions:

- **Hidden layer injection**: Add layers that trigger only for specific inputs
- **Connection rewiring**: Modify network topology to create backdoor pathways
- **Conditional computation**: Insert logic gates that activate malicious behavior for triggers
- **Architecture obfuscation**: Make modifications difficult to detect during casual inspection

Changes are designed to be non-obvious and preserve overall model performance on benign inputs.

### Checkpoint Poisoning

Attackers replace or modify saved model checkpoints with tampered versions. This is particularly effective in transfer learning scenarios where organizations trust pre-trained checkpoints from reputable sources:

- **Repository compromise**: Replace legitimate checkpoints on public repositories (Hugging Face, Model Zoo)
- **Supply chain injection**: Poison checkpoints during distribution from vendor to customer
- **Storage manipulation**: Modify checkpoints in cloud storage or artifact registries
- **Version substitution**: Replace specific model versions with backdoored variants


## Preconditions

- Attacker has write access to model storage (filesystem, cloud buckets, model registries)
- Access to training or fine-tuning infrastructure (compute clusters, ML pipelines)
- Ability to modify model files during transit between development stages
- Insider position or compromised credentials for ML engineering systems
- Lack of cryptographic integrity verification for model artifacts
- Absence of multi-party approval for model deployments
- Insufficient monitoring of weight distributions or behavioral consistency


## Impact

**Business Impact:**
- **Backdoor exploitation**: Hidden triggers enable targeted attacks (fraud bypass, policy violations, data manipulation)
- **Intellectual property compromise**: Model behavior reveals tampering methods to sophisticated attackers
- **Regulatory violations**: Tampered models in regulated industries (finance, healthcare) create compliance exposure
- **Reputational damage**: Discovery of compromised models undermines customer trust and market confidence
- **Supply chain contamination**: Poisoned models distributed to downstream users amplify impact across ecosystem
- **Incident response costs**: Identifying tampering requires expensive forensic analysis and potential full retraining

**Technical Impact:**
- **Selective misbehavior**: Model functions correctly in most cases but fails predictably for attacker-chosen triggers
- **Persistent compromise**: Backdoors survive fine-tuning and transfer learning, persisting across model versions
- **Detection evasion**: Tampering designed to pass standard accuracy/safety testing while hiding malicious capability
- **Model trust erosion**: Uncertainty about model integrity complicates deployment decisions


## Detection

- Unexpected changes in model accuracy or performance metrics on specific input subsets
- Validation loss divergence between production and test environments
- Checksum mismatches when verifying model files against known-good hashes
- Unusual weight distributions or statistical outliers in specific layers (detectable via statistical analysis)
- Behavioral drift: model responses shifting for certain input categories without corresponding retraining
- Audit log anomalies showing unauthorized access to model storage or training systems
- File modification timestamps inconsistent with authorized training schedules
- Unexplained model file size changes or architectural differences
- Alerts from integrity monitoring systems flagging validation failures


## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[case-studies/poisongpt]] | [[frameworks/atlas/tactics/persistence]] | Foundation model checkpoint backdoor distributed via Hugging Face; survived fine-tuning |
| [[case-studies/malicious-models-on-hugging-face]] | [[frameworks/atlas/tactics/execution]] | Pickle deserialization enabling arbitrary code execution from poisoned model files |
| [[case-studies/rules-file-backdoor-supply-chain-attack-on-ai-coding-assistants]] | [[frameworks/atlas/tactics/initial-access]] | Supply chain attack poisoning AI coding assistant instructions |


## Mitigations

| ID | Name | Description |
|----|------|-------------|
| AML.M0015 | [[mitigations/cryptographic-model-verification]] | Sign all model checkpoints with digital signatures; verify signatures before loading into production |
| | [[mitigations/hardware-security-modules-ml]] | Store model weights in HSMs or secure enclaves with strict access controls and audit logging |
| | [[mitigations/secure-multi-party-computation]] | For collaborative training, use cryptographic protocols preventing any single party from tampering |
| | [[mitigations/immutable-model-registry]] | Store models in write-once repositories with versioning and cryptographic lineage tracking |
| | [[mitigations/segregation-of-duties]] | Require multiple approvals for model deployments; separate training, validation, and deployment roles |
| | [[mitigations/ci-cd-pipeline-security]] | Implement secure build pipelines with integrity checks, automated testing, and approval gates |
| | [[mitigations/access-segmentation-and-rbac]] | Enforce least-privilege access to model storage and training infrastructure |
| | [[mitigations/ai-supply-chain-security]] | Vet third-party models and checkpoints; maintain trusted model sources with verification |
| | [[mitigations/secure-model-storage]] | Baseline integrity snapshots: maintain cryptographically signed snapshots of known-good model states |
| | [[mitigations/weight-distribution-monitoring]] | Continuously analyze parameter statistics against baselines; alert on significant deviations |
| | [[mitigations/behavioral-monitoring]] | Regular testing against diverse input sets to detect drift or unexpected behaviors |
| | [[mitigations/anomaly-detection-architecture]] | Monitor training metrics, validation losses, and performance characteristics for suspicious changes |
| | [[mitigations/access-review-and-audit]] | Track all access to model artifacts; correlate with authorized activities |
| | [[mitigations/model-forensics]] | Maintain tools and expertise to analyze suspected tampered models |
| | [[mitigations/model-rollback-procedures]] | Rapid reversion to last known-good checkpoint when tampering detected |
| | [[mitigations/incident-response-procedures]] | Defined procedures for investigating suspected tampering, preserving evidence, and determining scope |
| | [[mitigations/model-quarantine-and-sandboxing]] | Immediately isolate suspected tampered models from production |
| AML.M0018 | [[mitigations/model-version-management]] | Retraining from verified sources: rebuild from trusted checkpoints or retrain from scratch when integrity compromised |
| | [[mitigations/output-monitoring-validation]] | Post-incident validation: comprehensive testing before redeployment after tampering incident |


## Sources

> "Model tampering attacks involve adversaries gaining unauthorized access to modify a model's weights, architecture, or saved checkpoints. These attacks can introduce subtle backdoors, degrade performance, or alter behavior in targeted ways. Successful tampering can persist undetected through deployment, enabling malicious behavior triggered by specific inputs."
> — Source material on model tampering and integrity attacks
