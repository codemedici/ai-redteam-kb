---
title: "Federated Learning"
tags:
  - type/mitigation
  - target/training-pipeline
  - target/ml-model
  - source/adversarial-ai
  - source/red-teaming-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Federated Learning

## Summary

Federated learning (FL) enables collaborative model training across multiple parties (hospitals, mobile devices, organizations) without centralizing raw data. Instead of sending data to a central server, each participant trains a model locally on their private data and only shares model updates (gradients, weights). A central server aggregates these updates to produce a global model. FL significantly reduces data exposure risks by keeping sensitive data on-premises, making it valuable for privacy-sensitive domains (healthcare consortiums, mobile keyboards, financial institutions). However, FL is not immune to attacks—gradient updates can still leak information through [[techniques/model-inversion-attacks|model inversion]] or membership inference if not combined with [[mitigations/differential-privacy|differential privacy]].

> "Federated learning allows organizations to collaboratively train models without sharing raw data, addressing both privacy concerns and regulatory constraints." (p233-261, paraphrased)

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/rag-data-poisoning]] | Robust aggregation algorithms and update validation detect and filter malicious updates from compromised participants |
| | [[techniques/model-inversion-attacks]] | Partial protection by avoiding centralized raw data (requires differential privacy for full protection) |
| | [[techniques/membership-inference-attacks]] | Reduces membership inference risk by keeping training data decentralized |
| | [[techniques/data-exposure]] | Prevents centralized data breaches by keeping sensitive data on-premises |
| | [[techniques/privacy-attacks-beyond-membership-inference]] | Keeps training data decentralized, reducing exposure to model inversion and attribute inference; requires secure aggregation and DP for full protection against gradient-based privacy attacks |
| AML.T0020 | [[techniques/data-poisoning-attacks]] | Robust aggregation algorithms (Krum, Trimmed Mean, median) filter poisoned model updates from malicious FL participants |

## Architecture

**Standard Federated Learning Flow**:
1. **Central Server**: Initializes global model, distributes to clients
2. **Local Training**: Each client (hospital, device) trains on local data for N epochs
3. **Gradient Upload**: Clients send model updates (not data) to server
4. **Aggregation**: Server averages updates using FedAvg algorithm
5. **Global Model Update**: Aggregated model redistributed to clients
6. **Repeat**: Process iterates until convergence

**Example** (Healthcare Consortium):
```python
# Pseudocode for federated learning across 3 hospitals
global_model = initialize_model()

for round in range(num_rounds):
    # Distribute model to hospitals
    local_models = distribute(global_model, [Hospital_A, Hospital_B, Hospital_C])
    
    # Each hospital trains locally on patient data
    Hospital_A_update = train_local(local_models[0], Hospital_A_data)
    Hospital_B_update = train_local(local_models[1], Hospital_B_data)
    Hospital_C_update = train_local(local_models[2], Hospital_C_data)
    
    # Aggregate updates (FedAvg)
    global_model = aggregate([Hospital_A_update, Hospital_B_update, Hospital_C_update])

# Result: Global model trained on combined knowledge without data sharing
```

## Privacy Benefits

- **Data Residency**: Sensitive data never leaves local environment
- **Regulatory Compliance**: Easier compliance with GDPR, HIPAA (data stays on-premises)
- **Reduced Breach Surface**: No central data lake vulnerable to exfiltration

## Robust Aggregation Algorithms

Standard federated averaging (FedAvg) is vulnerable to model update poisoning from malicious clients. Robust aggregation algorithms filter or down-weight outlier updates before averaging.

### Krum

Selects the single client update closest to the majority of other updates. For each update, computes sum of distances to its nearest neighbors; the update with smallest sum is selected as the global update. Defends against up to f Byzantine clients out of n total (requires n ≥ 2f + 3).

### Trimmed Mean

For each model parameter dimension independently: discard the top and bottom k client values, then compute the mean of remaining values. Bounds the influence any malicious client can have on any single parameter.

### Coordinate-Wise Median

For each model parameter dimension: take the median across all client updates. The median is inherently robust to up to 50% malicious clients per dimension, though in practice fewer are needed for meaningful poisoning.

### Sybil Attack Resistance

A single attacker controlling multiple client identities (Sybil attack) can amplify poisoning impact and overwhelm honest clients. Defenses include:
- Client identity verification and rate limiting
- Anomaly detection on update similarity across clients
- Requiring proof-of-data (verifying clients trained on real local data)

## Limitations & Attacks

**FL is NOT fully private without additional protections**:
- **Gradient Leakage**: Model updates can leak training data through inversion attacks
- **Membership Inference**: Attackers can infer if specific samples were in local training set
- **Model Poisoning**: Malicious clients can poison global model by submitting corrupted updates
- **Byzantine Attacks**: Malicious clients send arbitrary updates not based on any data
- **Sybil Attacks**: Single attacker controls multiple identities to amplify poisoning

**Mitigations**:
- **Secure Aggregation**: Encrypt gradient updates so server can't inspect individual contributions
- **Differential Privacy**: Add noise to local updates before sharing (DP-FL)
- **Byzantine-Robust Aggregation**: Krum, Trimmed Mean, coordinate-wise median (see above)

## Use Cases

- **Healthcare**: Multi-hospital collaborations (rare disease detection without sharing patient records)
- **Mobile Devices**: Keyboard prediction models trained on user typing without uploading text (Google Gboard)
- **Financial Fraud Detection**: Banks collaboratively train fraud models without sharing transaction data

## Limitations & Trade-offs

**Limitations:**
- **Gradient leakage:** Model updates can leak training data through inversion attacks
- **Membership inference:** Attackers can infer if specific samples were in local training set
- **Model poisoning:** Malicious clients can poison global model by submitting corrupted updates
- **Communication overhead:** Requires multiple rounds of update exchange

**Trade-offs:**
- **Privacy vs. Model Quality:** Differential privacy noise reduces model accuracy
- **Aggregation Robustness vs. Participation:** Strict outlier detection may exclude legitimate edge-case participants
- **Convergence Speed vs. Security:** More validation rounds improve security but slow training

**Bypass Scenarios:**
- Attacker controls enough participants to overwhelm aggregation defenses
- Sophisticated gradient attacks extract training data despite aggregation

## Testing & Validation

**Functional Testing:**
1. **Aggregation Correctness:** Verify global model quality matches centralized training baseline
2. **Byzantine Resistance:** Test aggregation algorithm's ability to filter malicious updates
3. **Privacy Preservation:** Validate that individual participant data cannot be reconstructed

**Security Testing:**
1. **Poisoning Attacks:** Simulate malicious participant updates, verify detection and filtering
2. **Gradient Inversion:** Test if training data can be extracted from model updates
3. **Membership Inference:** Validate resistance to membership attacks

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Federated learning allows organizations to collaboratively train models without sharing raw data, addressing both privacy concerns and regulatory constraints. However, FL is not immune to attacks—gradient updates can still leak information through model inversion or membership inference."
> — [[sources/bibliography#Adversarial AI]], p. 233-261

> "Federated Learning Poisoning: Attacker controls subset of participating devices, manipulates local training data or model updates. Central server aggregates poisoned updates with legitimate ones, incorporating attacker's bias into global model. Requires robust aggregation algorithms (Byzantine-robust methods) to detect and reject outlier updates."
> — Extracted from RAG Data Poisoning technique (Adversarial AI)

## Related

- **Combines with**: [[mitigations/differential-privacy]], [[mitigations/secure-rl-algorithms]], [[mitigations/anomaly-detection-architecture]]
- **Vulnerabilities:** Model poisoning via malicious clients (requires Byzantine-robust aggregation)
