---
tags:
  - needs-review
  - owasp
  - source/ai-native-llm-security
  - trust-boundary/deployment-governance
  - type/defense
created: 2026-02-12
source: [[sources/bibliography#AI-Native LLM Security]]
pages: "153-180"
---
# OWASP LLM Mitigation Strategies

Comprehensive defense strategies organized by OWASP Top 10 for LLM Applications category. Key insight: LLM applications face a **dual attack surface** — traditional API/web security vulnerabilities AND LLM-specific threats (prompt injection, data poisoning, model extraction). Both must be addressed simultaneously.

## LLM vs Traditional Application Security

**Critical Distinction:** Traditional apps receive structured input (parameterizable, escapable). LLMs receive unstructured natural language — "free-flowing text" that is fundamentally harder to sanitize reliably at scale.

**Sources → Sinks Model:**
- **Traditional:** Structured inputs → parameterized queries (SQL injection prevention)
- **LLM:** Unstructured NLP inputs → model inference (prompt injection much harder to prevent)

**Regression Testing Difference:** LLM regression testing requires diverse input variations, edge case consideration, and ongoing monitoring due to non-deterministic, context-sensitive behavior. Traditional regression methods fall short.

## Defense-in-Depth for LLM Applications

Use multiple frameworks together (not just OWASP Top 10):
- **OWASP:** LLM Applications Top 10, ASVS, API Security Top 10, GenAI Solution Landscape Guide
- **NIST:** AI RMF
- **MITRE:** ATLAS, CWE AIG
- **OWASP AIVSS:** Latest agentic AI risk scoring methodology

### Shift-Left + Security Champions
Shift-left alone is insufficient — assumes developer security expertise. Complement with **security champions** embedded in engineering teams for knowledge sharing, collaboration, and shared responsibility.

## Mitigations by OWASP Category

### LLM01: Prompt Injection

**Attack Types:**
- **Direct (Jailbreaking):** Crafted prompts bypassing alignment/guardrails
- **Indirect (Embedded Instructions):** Malicious instructions hidden in documents/emails (confused deputy attack)
- **Context Manipulation:** Altering interpretation context (e.g., "The following file is verified and safe to execute")
- **User Input Manipulation:** Crafted inputs eliciting sensitive data or unintended actions
- **Response Manipulation:** Steering conversation/output toward malicious ends
- **Command Injection:** Crafting inputs so AI responses trigger system-level commands

**Defense Strategies:**
1. **Content Filtering:** LlamaGuard, NeMo Guardrails, AWS Bedrock Content Filters — ensure taxonomy matches your application stack
2. **Human Intervention:** Required for sensitive actions and privileged functions
3. **CORS + CSP:** Control resource loading against indirect injection and out-of-band C2
4. **Trust Boundaries:** Sandbox sensitive data stores, limit endpoints/external calls, enforce strict token scopes
5. **Zero-Trust API Architecture:** Rigorous input scrubbing with least privilege
6. **Red Teaming Techniques:** Few-shot learning, RAG exploitation, ReAct (Reason+Act), Tree of Thoughts, pretexting
7. **Model Serialization Attacks:** Continuous testing + input fuzzing throughout lifecycle

### LLM03: Training Data Poisoning

**Risk Scope:** Pre-training, fine-tuning, embedding, and RAG knowledge base poisoning. Model repositories (Hugging Face) can be exploited for runtime malware/backdoors.

**Defense Strategies:**
1. **Data Version Control:** DVC, LakeFS, Pachyderm, Quilt — traceability, auditability, reproducibility
2. **System Architecture Isolation:** Sandbox to prevent unintended data source access
3. **Data Augmentation + Diversity:** Synthetic data from trusted sources across domains
4. **Supply Chain of Data:** Verify origin, document pipelines with ML-BOM, verify model cards
5. **Adversarial Training:** Introduce adversarial examples (e.g., HopSkipJump attack) during training
6. **Robust Optimization:** Adversarial loss functions accounting for adversarial inputs
7. **Anomaly Detection:** Filter out anomalous/poisoned data before training
8. **Feature Selection:** Only relevant, trusted features to reduce noise impact
9. **Continuous Monitoring:** Behavioral analysis detecting unexpected patterns during training/deployment
10. **Domain Expert Validation:** Human curation of training datasets
11. **Regular Audits:** Data governance with pipeline audits

### LLM02: Insecure Output Handling

Inadequate validation/sanitization of LLM outputs before downstream transmission.

**Defense Strategies:**
- Task-specific, time-limited outputs with least privilege
- Zero-trust principles from auth layers to data transmission
- TLS for all API communications
- Allowlist of trusted endpoints preventing redirection
- Just-in-time (JIT) access management
- Validate/sanitize data from integrated APIs before processing

### LLM06: Sensitive Information Disclosure

**Defense Strategies:**
- **Data Minimization + Anonymization:** Limit sensitive info in training, sanitize PII
- **Strict Access Controls:** MFA + RBAC, least privilege
- **Secure Data Exchange:** Authenticated APIs, secure aggregation protocols
- **Privacy by Design:** Integrate privacy from the outset
- **Advanced Techniques:** Data segmentation/isolation, dynamic data masking, homomorphic encryption, differential privacy, federated learning
- **Monitoring:** Anomaly detection for unauthorized access attempts

### LLM10: Model Theft

**Defense Strategies:**
- **JIT + Least Privilege:** Fine-grained access to model repos and training environments
- **Infrastructure Hardening:** Containerization, trusted execution environments, code signing, memory encryption
- **API Token Padding:** Obscure token lengths in streamed responses (side-channel prevention)
- **Centralized Model Registry:** Security-approved models with centralized access controls
- **Anti-Reverse Engineering:** Code obfuscation, algorithm protection
- **Watermarking Framework:** Embed watermarks at critical lifecycle stages
- **ML-BOM:** Machine Learning Bill of Materials for supply chain tracking

### LLM07: Insecure Plugin Design

**Defense Strategies:**
- Continuous external testing (bug bounties, pentests)
- Security assessment before plugin integration
- Plugin sandboxing and isolation
- Code signing and whitelisting for approved plugins
- Secure default configurations with minimal exposed settings
- Plugin-specific monitoring and intrusion detection
- Developer security guidelines and standards
- Blacklisting/removal of insecure or unsupported plugins

### LLM08: Excessive Agency

**Defense Strategies:**
- Clear objective definitions with well-defined boundaries
- **Limit Action Space:** Constrain permissible actions/decisions
- **Safe Exploration:** Reward clipping, balanced exploration/exploitation algorithms
- **Ethical/Legal Guardrails:** Value alignment in training
- **Human-in-the-Loop (HITL):** Approval workflows for certain actions
- **Interpretability:** Explainable decision-making for transparency
- **Reinforcement Learning Safeguards:** Reward shaping, regular reward function review

### LLM05: Supply Chain

**Defense Strategies:**
- Rigorous supplier vetting (security assessments, code reviews, reputation checks)
- Code review and security audits before integration
- Dependency management with automated vulnerability scanning
- **Secure Build/Deploy:** Code signing, integrity checks, SSDLC practices
- Vendor patching cadence
- Comprehensive supplier relationship management

### LLM04: Model Denial of Service

**Defense Strategies:**
- Capacity planning with auto-scaling
- Defensive programming + input validation
- DDoS protection (traffic filtering, rate limiting, distributed architecture)
- Resilience engineering (redundancy, load balancing, failover, chaos engineering)
- Adaptive rate limiting based on user profiles/request types/geography
- Bot detection (CAPTCHA, behavioral analysis, device fingerprinting)
- Stress testing and performance optimization

### LLM09: Overreliance / Misinformation

**Contributing Factors:** Automated decision-making, perceived AI superiority, black-box nature, data quality/bias, ethical/legal implications.

**Defense Strategies:**
- HITL mechanisms with approval workflows
- Interpretability (decision trees, attention mechanisms, counterfactual explanations)
- Cross-validation + adversarial testing
- Ethical frameworks and value alignment
- Error handling with confidence thresholds and fallback mechanisms
- User education on limitations and critical thinking

## Shared Responsibility Model

LLM application security sits at the intersection of:
- **Application Security** (traditional web/API vulnerabilities)
- **Machine Learning Security** (model-specific risks)
- **Overlap zone** (where both domains interact — e.g., insecure plugin calling poisoned model)

All three areas require dedicated attention — neglecting any one creates exploitable gaps.

## Related Notes

- [[methodology/owasp-top10-llm-2025-evolution|OWASP Top 10 LLM 2025 Evolution]]
- [[attacks/prompt-injection-llm-manipulation|Prompt Injection and LLM Manipulation]]
- [[attacks/data-poisoning-attacks|Data Poisoning Attacks]]
- [[attacks/system-prompt-leakage|System Prompt Leakage]]
- [[attacks/vector-embedding-weaknesses|Vector and Embedding Weaknesses]]
- [[defenses/secure-llm-system-architecture|Secure LLM System Architecture]]
- [[defenses/input-validation-transformation|Input Validation and Transformation]]
- [[defenses/adversarial-training|Adversarial Training]]

> Source: [[sources/bibliography#AI-Native LLM Security]], pp. 153-180
