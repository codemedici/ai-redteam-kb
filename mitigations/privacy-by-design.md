---
title: "Privacy-by-Design"
tags:
  - type/mitigation
  - target/llm
  - target/generative-ai
  - target/ml-model
  - target/training-pipeline
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Privacy-by-Design

## Summary

Privacy-by-design incorporates privacy considerations from the earliest stages of LLM development and deployment, making privacy an essential feature of the system rather than a retrofitted addition. This proactive approach embeds privacy protections into architecture, data handling, training methodology, and operational procedures—reducing privacy risks through systematic design decisions rather than reactive patching after deployment.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/pii-in-corpus]] | Systematic design prevents PII from entering training data or knowledge bases through architectural controls |
| AML.T0024 | [[techniques/sensitive-info-disclosure]] | Privacy-first architecture minimizes sensitive data exposure surface |
| AML.T0056 | [[techniques/training-data-memorization]] | Design decisions limit model's ability to memorize and regurgitate individual records |
| | [[techniques/membership-inference-attacks]] | Privacy-preserving design patterns make membership inference attacks less effective |

## Implementation

### Foundational Principles (Ann Cavoukian's 7 Principles)

Privacy-by-design was formalized by Dr. Ann Cavoukian (Information and Privacy Commissioner of Ontario) and consists of seven foundational principles:

**1. Proactive not Reactive; Preventative not Remedial**
- Anticipate privacy risks before they materialize
- Build privacy protections during design phase, not after incidents
- Privacy threat modeling during system architecture

**2. Privacy as the Default Setting**
- No user action required to protect privacy
- Maximum privacy protection built into standard operation
- Opt-out model for data collection (not opt-in for privacy)

**3. Privacy Embedded into Design**
- Privacy integral to system architecture, not bolted on
- Privacy protections in core functionality, not separate module
- Privacy considerations inform every design decision

**4. Full Functionality — Positive-Sum, not Zero-Sum**
- Privacy and functionality are complementary, not competing
- Avoid false dichotomies ("privacy or performance")
- Innovate to achieve both privacy and business objectives

**5. End-to-End Security — Full Lifecycle Protection**
- Privacy protection from data collection through deletion
- Secure data handling at every stage (ingestion, storage, processing, output)
- Cradle-to-grave data lifecycle management

**6. Visibility and Transparency — Keep it Open**
- Privacy practices openly documented and verifiable
- Users can inspect how their data is handled
- Independent audits validate privacy claims

**7. Respect for User Privacy — Keep it User-Centric**
- User control over personal data
- Informed consent with clear privacy implications
- Mechanisms for data access, correction, deletion

**Source:** https://www.ipc.on.ca/wp-content/uploads/Resources/7foundationalprinciples.pdf

### Privacy-by-Design in LLM Architecture

**Data Collection Phase:**

```
Privacy-by-Design Decisions:
├─ Minimize PII collection (collect only what's necessary)
├─ Anonymize at source (before data enters pipeline)
├─ Segregate sensitive data (separate storage, access controls)
├─ Audit data provenance (track where PII originates)
└─ Implement retention policies (auto-delete after defined period)
```

**Example Architecture:**
```python
class PrivacyFirstDataPipeline:
    """
    Data pipeline with privacy-by-design principles.
    """
    
    def ingest_data(self, raw_data, source_id):
        # Principle 1: Proactive — screen for PII before ingestion
        pii_detected = self.detect_pii(raw_data)
        if pii_detected:
            # Principle 2: Privacy by default — reject data with PII
            self.log_pii_rejection(source_id, pii_detected)
            return None
        
        # Principle 3: Embedded privacy — anonymize during ingestion
        anonymized_data = self.anonymize(raw_data)
        
        # Principle 5: End-to-end security — encrypt at rest
        encrypted_data = self.encrypt(anonymized_data)
        
        # Store with metadata for lifecycle management
        self.store_with_retention_policy(encrypted_data, retention_days=90)
        
        return encrypted_data
```

**Training Phase:**

```
Privacy-by-Design Decisions:
├─ Differential privacy during training (DP-SGD)
├─ Regularization to prevent memorization
├─ Exclude high-risk data categories from training
├─ Privacy budget tracking (cumulative privacy loss)
└─ Privacy-preserving validation datasets
```

**Inference Phase:**

```
Privacy-by-Design Decisions:
├─ Output filtering for PII leakage
├─ Query logging with user consent
├─ Rate limiting per user (prevent extraction attacks)
├─ Aggregate analytics only (no individual tracking)
└─ User data deletion on request (GDPR right to erasure)
```

### Privacy-by-Design Checklist for LLM Projects

**Pre-Development:**
- [ ] Conduct Privacy Impact Assessment (PIA) before project kickoff
- [ ] Identify all personal data types that will be processed
- [ ] Define data retention and deletion policies
- [ ] Establish legal basis for data processing (consent, legitimate interest, etc.)
- [ ] Assign Data Protection Officer (DPO) or privacy lead

**Design Phase:**
- [ ] Minimize data collection to only what's necessary for functionality
- [ ] Design anonymization/pseudonymization into data pipeline
- [ ] Implement access controls and least-privilege principles
- [ ] Plan for user data access, correction, and deletion requests
- [ ] Design logging and monitoring with privacy preservation

**Development Phase:**
- [ ] Apply differential privacy during training
- [ ] Implement PII detection and redaction in outputs
- [ ] Encrypt sensitive data at rest and in transit
- [ ] Use privacy-preserving techniques (federated learning, secure enclaves)
- [ ] Build user consent management system

**Deployment Phase:**
- [ ] Privacy policy clearly explains data handling
- [ ] User controls for privacy preferences (opt-out, data deletion)
- [ ] Incident response plan for privacy breaches
- [ ] Regular privacy audits and penetration testing
- [ ] Staff training on privacy practices

**Operational Phase:**
- [ ] Monitor for privacy violations (PII leakage, unauthorized access)
- [ ] Respond to data subject requests within regulatory timelines
- [ ] Update privacy practices as regulations evolve
- [ ] Conduct periodic PIAs for system changes
- [ ] Document privacy decisions and trade-offs

### Privacy-by-Design Patterns for LLMs

**Pattern 1: PII Scrubbing Gateway**

All data passes through PII detection before entering training pipeline:

```
External Data → PII Detection → Reject/Anonymize → Training Pipeline
                      ↓
                 PII Audit Log
```

**Pattern 2: Privacy Budget Accounting**

Track cumulative privacy loss across queries to prevent gradual information leakage:

```python
class PrivacyBudgetTracker:
    """
    Enforce privacy budget limits per user/dataset.
    """
    
    def __init__(self, epsilon_max=1.0):
        self.epsilon_max = epsilon_max
        self.user_budgets = {}
    
    def check_query_allowed(self, user_id, query_epsilon):
        current_epsilon = self.user_budgets.get(user_id, 0)
        
        if current_epsilon + query_epsilon > self.epsilon_max:
            raise PrivacyBudgetExceeded(
                f"User {user_id} exceeded privacy budget"
            )
        
        self.user_budgets[user_id] = current_epsilon + query_epsilon
        return True
```

**Pattern 3: Federated Learning Architecture**

Train models without centralizing sensitive data:

```
Hospital A (Local Model) ──┐
Hospital B (Local Model) ──┼→ Aggregate Updates → Global Model
Hospital C (Local Model) ──┘
     ↑
  Raw data never leaves hospital
```

**Pattern 4: Differential Privacy by Default**

All model responses include privacy noise automatically:

```python
def query_model_with_privacy(model, input_data, epsilon=0.5):
    """
    Add differential privacy noise to all responses by default.
    """
    raw_output = model.predict(input_data)
    noisy_output = add_laplace_noise(raw_output, epsilon)
    return noisy_output
```

**Pattern 5: Right to Erasure Implementation**

Design system to honor data deletion requests:

```python
class DataDeletionHandler:
    """
    Enable GDPR-compliant data erasure.
    """
    
    def delete_user_data(self, user_id):
        # Delete raw data
        self.delete_from_database(user_id)
        
        # Delete from training data backups
        self.delete_from_backups(user_id)
        
        # Mark model for retraining without user data
        self.flag_for_retraining(user_id)
        
        # Log deletion for compliance audit
        self.log_deletion(user_id, timestamp=datetime.now())
```

### Privacy-by-Design in RAG Systems

**Challenge:** RAG systems index external documents that may contain PII, exposing them through retrieval.

**Privacy-by-Design Solution:**

```
Document Ingestion
    ↓
PII Detection & Redaction (before embedding)
    ↓
Embed Sanitized Document
    ↓
Store with Access Controls
    ↓
Retrieval (filtered by user permissions)
    ↓
Output Filtering (double-check PII leakage)
```

**Implementation:**
```python
def ingest_document_with_privacy(document, source_id):
    """
    RAG ingestion with privacy-by-design.
    """
    # Step 1: Detect PII before embedding
    pii_entities = detect_pii(document.text)
    
    if pii_entities:
        # Step 2: Redact PII
        sanitized_text = redact_pii(document.text, pii_entities)
        
        # Step 3: Log redaction for audit
        log_pii_redaction(source_id, pii_entities)
    else:
        sanitized_text = document.text
    
    # Step 4: Generate embedding on sanitized text
    embedding = generate_embedding(sanitized_text)
    
    # Step 5: Store with access controls
    store_in_vector_db(
        embedding=embedding,
        metadata={
            'source': source_id,
            'access_policy': 'internal_only',
            'retention_days': 365
        }
    )
```

## Limitations & Trade-offs

**Advantages:**
- Reduces privacy risks at architectural level (harder to bypass)
- Compliance-friendly (aligns with GDPR, CCPA, HIPAA)
- Builds user trust through transparent privacy practices
- Reduces incident response costs (fewer breaches)

**Trade-offs:**
- **Higher upfront cost:** Privacy-by-design requires more planning and development effort
- **Potential functionality constraints:** Privacy protections may limit certain features
- **Performance overhead:** Encryption, anonymization, differential privacy add latency
- **User experience friction:** Consent flows, data access requests add complexity

**Limitations:**
- Privacy-by-design cannot eliminate all privacy risks (insider threats, sophisticated attacks)
- Trade-offs between privacy and model utility are inevitable
- Regulatory requirements evolve faster than system redesigns
- Privacy-by-design requires ongoing commitment (not one-time effort)

## Testing & Validation

**Privacy Threat Modeling:**
1. Identify all data flows involving personal data
2. Map trust boundaries and potential leak points
3. Assess risks using STRIDE or similar framework
4. Validate mitigations address identified threats

**Privacy Audit:**
1. Review data minimization practices (are we collecting only necessary data?)
2. Verify anonymization effectiveness (can individuals be re-identified?)
3. Test data deletion workflows (is data truly erased?)
4. Validate consent management (are user preferences honored?)

**Penetration Testing:**
1. Attempt PII extraction through model queries
2. Test for membership inference attacks
3. Attempt unauthorized data access (privilege escalation)
4. Validate privacy budget enforcement

**Regulatory Compliance Check:**
1. GDPR Article 25 (Data Protection by Design and Default)
2. CCPA privacy-by-design requirements
3. HIPAA Privacy Rule compliance
4. Industry-specific regulations (financial services, healthcare)

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Privacy-by-design: Incorporate privacy considerations from the earliest stages of LLM development and deployment, making privacy an essential feature of the system rather than a retrofitted addition."
> — Extracted from PII in Corpus mitigation section

> Ann Cavoukian, "Privacy by Design: The 7 Foundational Principles," Information and Privacy Commissioner of Ontario (2009).
> — https://www.ipc.on.ca/wp-content/uploads/Resources/7foundationalprinciples.pdf

## Related

- **Enables:** [[mitigations/privacy-impact-assessments]], [[mitigations/data-minimization-principles]], [[mitigations/differential-privacy]]
- **Aligns with:** GDPR Article 25, NIST Privacy Framework, ISO 29100
- **Complementary:** [[mitigations/federated-learning]], [[mitigations/homomorphic-encryption]], [[mitigations/anonymization-techniques]]
