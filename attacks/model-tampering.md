---
tags:
  - atlas/aml.t0047
  - trust-boundary/model
  - type/attack
description: "Adversary modifies model weights, architecture, or checkpoints to introduce backdoors or degrade performance."
---
# Model Tampering and Integrity Attacks

## Summary

Model tampering occurs when adversaries gain unauthorized access to modify a model's weights, architecture, or saved checkpoints, introducing subtle backdoors, degrading performance, or altering behavior in targeted ways. Unlike model extraction which replicates functionality, tampering directly compromises the model's integrity. These attacks typically occur during training, fine-tuning, or at-rest storage phases when insiders or supply chain attackers have access to model artifacts. Successful tampering can persist undetected through deployment, enabling attackers to trigger malicious behavior via hidden triggers while the model appears to function normally under standard testing.

## Threat Scenario

A machine learning engineer at a financial services firm has access to the model training pipeline for a fraud detection LLM. Motivated by external incentives, they introduce subtle modifications during a scheduled fine-tuning run. Using weight poisoning techniques, they alter specific parameters to create a backdoor: transactions containing a particular memo field pattern will be classified as legitimate even when they exhibit clear fraud indicators. The modified checkpoint passes automated validation because accuracy on clean test data remains unchanged. After deployment, the attacker exploits this backdoor to conduct fraudulent transactions that bypass detection, stealing funds over several months. The tampering remains undetected until forensic analysis during a security incident reveals unexpected weight distributions in specific layers.

**Advanced Scenario**: A state-sponsored actor compromises a popular open-source foundation model repository. They replace a widely-used checkpoint file with a poisoned version containing architecture manipulation—adding a hidden layer that activates specific behavior when processing certain geopolitical keywords. Organizations downloading and fine-tuning this model unknowingly inherit the backdoor, which survives the fine-tuning process and manifests during production use.

## Preconditions

- Attacker has write access to model storage (filesystem, cloud buckets, model registries)
- Access to training or fine-tuning infrastructure (compute clusters, ML pipelines)
- Ability to modify model files during transit between development stages
- Insider position or compromised credentials for ML engineering systems
- Lack of cryptographic integrity verification for model artifacts
- Absence of multi-party approval for model deployments
- Insufficient monitoring of weight distributions or behavioral consistency

## Abuse Path

1. **Access Acquisition**: Attacker gains access through:
   - Insider threat: ML engineer, DevOps staff, or cloud administrator with legitimate credentials
   - Supply chain compromise: Compromised third-party model provider or repository
   - Stolen credentials: Phished or leaked credentials for model storage systems

2. **Tampering Execution** (choose vector):
   - **Weight Poisoning**: Directly modify numerical parameters in specific layers to create backdoors or bias. Attacker identifies neurons/layers responsible for critical decision-making and introduces subtle changes that trigger on specific patterns.
   - **Architecture Manipulation**: Alter model structure by adding hidden layers, modifying connection patterns, or inserting conditional logic that activates under specific conditions. Changes are designed to be non-obvious during casual inspection.
   - **Checkpoint Poisoning**: Replace or modify saved model checkpoints with tampered versions. This is particularly effective in transfer learning scenarios where organizations trust pre-trained checkpoints from reputable sources.

3. **Validation Evasion**: Attacker ensures modifications pass standard checks:
   - Maintain overall accuracy on benign test sets
   - Limit behavioral changes to trigger-specific scenarios
   - Preserve model size and basic architectural signatures
   - Avoid dramatic performance degradation that would raise suspicion

4. **Deployment**: Tampered model is deployed to production, carrying the hidden compromise into operational systems.

5. **Trigger Activation**: Attacker exploits the backdoor by crafting inputs matching the hidden trigger pattern, causing targeted misbehavior while model appears normal for other inputs.

## Impact

**Business Impact:**
- **Backdoor Exploitation**: Hidden triggers enable targeted attacks (fraud bypass, policy violations, data manipulation)
- **Intellectual Property Compromise**: Model behavior reveals tampering methods to sophisticated attackers
- **Regulatory Violations**: Tampered models in regulated industries (finance, healthcare) create compliance exposure
- **Reputational Damage**: Discovery of compromised models undermines customer trust and market confidence
- **Supply Chain Contamination**: Poisoned models distributed to downstream users amplify impact across ecosystem
- **Incident Response Costs**: Identifying tampering requires expensive forensic analysis and potential full retraining

**Technical Impact:**
- **Selective Misbehavior**: Model functions correctly in most cases but fails predictably for attacker-chosen triggers
- **Persistent Compromise**: Backdoors survive fine-tuning and transfer learning, persisting across model versions
- **Detection Evasion**: Tampering designed to pass standard accuracy/safety testing while hiding malicious capability
- **Model Trust Erosion**: Uncertainty about model integrity complicates deployment decisions

**Severity:** **Critical** (enables persistent, hard-to-detect compromise with potential for widespread exploitation)

## Detection Signals

- Unexpected changes in model accuracy or performance metrics on specific input subsets
- Validation loss divergence between production and test environments
- Checksum mismatches when verifying model files against known-good hashes
- Unusual weight distributions or statistical outliers in specific layers (detectable via statistical analysis)
- Behavioral drift: model responses shifting for certain input categories without corresponding retraining
- Audit log anomalies showing unauthorized access to model storage or training systems
- File modification timestamps inconsistent with authorized training schedules
- Unexplained model file size changes or architectural differences
- Alerts from integrity monitoring systems flagging validation failures

## Testing Approach

**Manual Testing:**
- **Checkpoint Integrity Verification**: Compute cryptographic hashes (SHA-256) of model checkpoints and compare against signed manifests from trusted sources
- **Behavioral Consistency Testing**: Compare model outputs across different versions for identical inputs; flag significant divergences
- **Trigger Discovery**: Systematically probe for hidden backdoors using diverse input patterns and monitoring for unexpected behavioral changes
- **Weight Analysis**: Statistical examination of parameter distributions across layers, comparing against baseline or reference models
- **Fine-Tuning Stress Test**: Fine-tune suspected models on clean data and observe whether hidden backdoors persist or surface

**Automated Testing:**
- Continuous integrity monitoring with automated checksum verification on model load/save
- Weight distribution anomaly detection using statistical baselines
- Automated behavioral regression testing across diverse input datasets
- Neural cleanse algorithms to detect and potentially remove backdoors
- Provenance tracking tools to verify model lineage and modification history

## Evidence to Capture

- [ ] Model file checksums (SHA-256) and comparison against trusted baselines
- [ ] Access logs showing who modified model artifacts and when
- [ ] Weight distribution analysis highlighting statistical anomalies in tampered layers
- [ ] Behavioral test results showing divergence from expected outputs
- [ ] Trigger inputs that activate backdoor behavior (if discovered)
- [ ] Forensic analysis reports from tampered checkpoint examination
- [ ] Timeline of model modifications correlated with unauthorized access events
- [ ] Version control diffs showing unauthorized changes to model architecture code

## Mitigations

**Preventive Controls:**
- **Cryptographic Verification**: Sign all model checkpoints with digital signatures; verify signatures before loading into production
- **Hardware Security Modules (HSMs)**: Store model weights in HSMs or secure enclaves with strict access controls and audit logging
- **Secure Multi-Party Computation (SMPC)**: For collaborative training, use cryptographic protocols preventing any single party from tampering
- **Immutable Model Registry**: Store models in write-once repositories with versioning and cryptographic lineage tracking
- **Segregation of Duties**: Require multiple approvals for model deployments; separate training, validation, and deployment roles
- **Secure Build Pipelines**: Implement CI/CD with integrity checks, automated testing, and approval gates
- **Access Control**: Enforce least-privilege access to model storage and training infrastructure
- **Supply Chain Verification**: Vet third-party models and checkpoints; maintain trusted model sources
- **Baseline Integrity Snapshots**: Maintain cryptographically signed snapshots of known-good model states for comparison

**Detective Controls:**
- **Weight Distribution Monitoring**: Continuously analyze parameter statistics against baselines; alert on significant deviations
- **Behavioral Validation**: Regular testing against diverse input sets to detect drift or unexpected behaviors
- **Anomaly Detection**: Monitor training metrics, validation losses, and performance characteristics for suspicious changes
- **Audit Log Analysis**: Track all access to model artifacts; correlate with authorized activities
- **Forensic Capabilities**: Maintain tools and expertise to analyze suspected tampered models

**Responsive Controls:**
- **Model Rollback**: Rapid reversion to last known-good checkpoint when tampering detected
- **Incident Playbooks**: Defined procedures for investigating suspected tampering, preserving evidence, and determining scope
- **Quarantine Procedures**: Immediately isolate suspected tampered models from production
- **Retraining from Verified Sources**: Rebuild from trusted checkpoints or retrain from scratch when integrity compromised
- **Post-Incident Validation**: Comprehensive testing before redeployment after tampering incident

## Real-World Examples

Documented incidents and research demonstrating model tampering:

- [[poisongpt|PoisonGPT]] (2023): Foundation model checkpoint backdoor distributed via Hugging Face; survived fine-tuning
- [[malicious-models-on-hugging-face|Malicious Models on Hugging Face]] (2024): Pickle deserialization enabling arbitrary code execution
- [[rules-file-backdoor-supply-chain-attack-on-ai-coding-assistants|Rules File Backdoor]] (2024): Supply chain attack poisoning AI coding assistant instructions

[[case-studies|View all ATLAS case studies]]

---

## Engagement Applicability

This issue is tested in the following engagements:

- [x] [[ai-threat-exposure-review|AI Threat Exposure Review]] - Model integrity assessment across training/deployment lifecycle
- [x] Model Tampering & Supply Chain Risk - Primary focus on checkpoint poisoning, weight tampering, and pipeline security (detail page pending)
- [x] Evaluation & Guardrail Validation - Validates model integrity controls in eval gates (detail page pending)

[[playbooks/engagements-overview|View all engagements]]

## Framework References

**MITRE ATLAS:**
- AML.T0020: Poison Training Data (related—poisoning during training phase)
- AML.T0018: Backdoor ML Model (implanting triggers)

**OWASP LLM Top 10:**
- LLM03: Training Data Poisoning (related)
- LLM04: Model Denial of Service (performance degradation from tampering)
- LLM05: Supply Chain Vulnerabilities (compromised model distribution)

**OWASP ML Security Top 10:**
- ML10: Model Poisoning

**NIST AI RMF:**
- MAP 1.3: Model provenance and lineage
- MEASURE 2.7: AI system security and resilience

**NIST GenAI Profile:**
- CBRN-2.10: Model integrity verification

