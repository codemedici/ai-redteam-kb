---
tags:
  - methodology
  - needs-review
  - source/ai-native-llm-security
  - trust-boundaries
  - trust-boundary/model
  - type/methodology
created: 2026-02-12
source: [[sources/bibliography#AI-Native LLM Security]]
pages: "73-89"
---
# Trust Boundary Mapping for LLM Architectures

**Definition:** Trust boundaries are conceptual lines delineating transitions between various levels of trust within an LLM system — points where data or control flow moves between components with varying security assurance.

**Key Boundaries:** Data ingestion boundary → model training boundary → model serving boundary → output generation boundary.

## Why Trust Boundaries Matter for LLMs

1. **Comprehensive risk assessment** — map where data/control crosses trust levels to pinpoint high-risk areas
2. **Targeted security controls** — defense-in-depth strategies at critical junctures
3. **Data protection compliance** — GDPR, CCPA, HIPAA require clear delineation when handling sensitive data
4. **Incident containment** — well-defined boundaries limit blast radius and enable faster forensic analysis

## Four Attack Surface Categories

### 1. Data-Related Attack Surfaces

**Data Poisoning Variants:**
- **Label Flipping:** Change training labels to induce misclassification (e.g., positive reviews labeled negative)
- **Injection Attacks:** Insert malicious samples with hidden triggers causing specific harmful outputs
- **Clean Label Attacks:** Subtly modify legitimate samples without changing labels — most sophisticated, imperceptible to humans

**Data Leakage Mechanisms:**
- **Training Data Extraction:** Repeated querying to reconstruct training data (exposes copyrighted material, PII, confidential info)
- **Membership Inference:** Determine if specific data point was in training set (privacy violation, especially healthcare)
- **Model Inversion:** Reconstruct input features from model outputs (infer demographics from interactions)

**Privacy Protection Techniques:**
- **K-anonymity:** Each record indistinguishable from k-1 others on identifying attributes
- **Differential Privacy:** Calibrated noise providing mathematical privacy guarantees
- **Data Masking:** Replace sensitive data with realistic fake data preserving statistical properties
- **Homomorphic Encryption:** Compute on encrypted data without decryption

**Privacy Principles:** Data minimization, purpose limitation, storage limitation, individual rights (access, rectification, erasure). Conduct Privacy Impact Assessments (PIAs) before deployment.

### 2. Model-Related Attack Surfaces

**Model Theft Techniques:**
- **Equation Solving:** Crafted inputs solving for model parameters by observing outputs
- **Model Replication:** Train clone model on target model's outputs (query-based extraction)
- **Side-Channel Attacks:** Exploit timing/power consumption patterns to infer architecture details

**Defenses:** Differential privacy in responses, watermarking (imperceptible output alterations detectable by owner)

**Model Tampering:**
- **Weight Poisoning:** Modify weights to introduce specific vulnerabilities/backdoors targeting specific neurons/layers
- **Architecture Manipulation:** Alter network structure to create hidden functionalities (e.g., hidden layer activated by triggers)
- **Checkpoint Poisoning:** Tamper with saved checkpoints — particularly dangerous for transfer learning

**Defense:** Secure multi-party computation for distributed-trust training

**Adversarial Attacks:**
- **Evasion Attacks:** Imperceptible perturbations causing misclassification (bypass content moderation)
- **Backdoor Attacks:** Train model to respond to trigger inputs with specific outputs
- **Universal Adversarial Perturbations:** Single perturbation pattern causing misclassification across all inputs

**Defenses:** Adaptive adversarial training, certification/formal verification methods (mathematical robustness guarantees)

### 3. Deployment-Related Attack Surfaces

**API Vulnerabilities:**
- Runtime injection (distinct from training-time poisoning) — crafted inputs exploiting model sensitivity
- Rate limiting bypass — distributed requests across IPs/accounts for model theft or DoS
- Authentication flaws — token forgery/reuse, inadequate validation
- **Mitigation:** Sliding window rate limiting, per-user + global limits, OAuth 2.0/JWT with short-lived tokens, API gateways

**Access Control:**
- Granular permissions (per-action, per-data-type)
- RBAC with clear role definitions
- Least privilege as guiding principle
- ABAC for dynamic context-aware decisions
- Segregation of duties for critical operations

**Monitoring:**
- Input/output logging with tamper-evident mechanisms
- Performance monitoring detecting tampering/degradation
- User activity monitoring with behavioral analytics
- Threat intelligence integration for emerging LLM-specific threats

### 4. Supply Chain Attack Surfaces

**Pre-trained Model Risks:**
- Hidden biases in training datasets (unfair/discriminatory outputs)
- Malicious code or backdoors in model architecture
- IP and licensing compliance issues

**Library/Dependency Risks:**
- Compromised open-source libraries (cf. SolarWinds 2020)
- Malicious code propagating through dependency chains
- Deep system/data access making libraries attractive targets

**Best Practices:**
- Due diligence on all third-party components
- Formal vendor risk assessment process
- Comprehensive dependency inventory with version tracking
- Automated vulnerability scanning
- Consider forking critical libraries for internal maintenance
- Supply-chain-specific incident response plans

## Mapping to Vault Taxonomy

These four attack surface categories map directly to the vault's trust boundary structure:

| Attack Surface | Vault Location |
|---------------|---------------|
| Data-related | `attacks/data-*`, `attacks/embedding-*` |
| Model-related | `attacks/adversarial-*`, `attacks/model-*`, `attacks/membership-*` |
| Deployment-related | `defenses/secure-llm-*`, `defenses/least-privilege-*` |
| Supply chain | `attacks/supply-chain-*`, `defenses/ai-infrastructure-*` |

## Related Notes

- [[defenses/secure-llm-system-architecture|Secure LLM System Architecture]]
- [[attacks/data-poisoning-attacks|Data Poisoning Attacks]]
- [[attacks/model-extraction-stealing|Model Extraction and Stealing]]
- [[attacks/adversarial-examples-evasion-attacks|Adversarial Examples and Evasion Attacks]]
- [[attacks/membership-inference-attacks|Membership Inference Attacks]]
- [[defenses/owasp-llm-mitigation-strategies|OWASP LLM Mitigation Strategies]]
- [[methodology/owasp-top10-llm-2025-evolution|OWASP Top 10 LLM 2025 Evolution]]

> Source: [[sources/bibliography#AI-Native LLM Security]], pp. 73-89
