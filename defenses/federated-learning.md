---
title: "Federated Learning"
description: "Distributed training approach where models train on decentralized data without centralizing sensitive information, enhancing privacy."
tags:
  - type/defense
  - target/training-pipeline
  - mitigates/data-exposure
  - mitigates/model-inversion
  - source/adversarial-ai
  - needs-review
---

# Federated Learning

## Summary

Federated learning (FL) enables collaborative model training across multiple parties (hospitals, mobile devices, organizations) without centralizing raw data. Instead of sending data to a central server, each participant trains a model locally on their private data and only shares model updates (gradients, weights). A central server aggregates these updates to produce a global model. FL significantly reduces data exposure risks by keeping sensitive data on-premises, making it valuable for privacy-sensitive domains (healthcare consortiums, mobile keyboards, financial institutions). However, FL is not immune to attacks—gradient updates can still leak information through [[attacks/model-inversion-attacks|model inversion]] or membership inference if not combined with [[defenses/differential-privacy|differential privacy]].

> "Federated learning allows organizations to collaboratively train models without sharing raw data, addressing both privacy concerns and regulatory constraints." (p233-261, paraphrased)

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

## Limitations & Attacks

**FL is NOT fully private without additional protections**:
- **Gradient Leakage**: Model updates can leak training data through inversion attacks
- **Membership Inference**: Attackers can infer if specific samples were in local training set
- **Model Poisoning**: Malicious clients can poison global model by submitting corrupted updates

**Mitigations**:
- **Secure Aggregation**: Encrypt gradient updates so server can't inspect individual contributions
- **Differential Privacy**: Add noise to local updates before sharing (DP-FL)
- **Byzantine-Robust Aggregation**: Detect and reject outlier updates from poisoning clients

## Use Cases

- **Healthcare**: Multi-hospital collaborations (rare disease detection without sharing patient records)
- **Mobile Devices**: Keyboard prediction models trained on user typing without uploading text (Google Gboard)
- **Financial Fraud Detection**: Banks collaboratively train fraud models without sharing transaction data

## Related

- **Attacks Mitigated**: Data exposure, [[attacks/model-inversion-attacks]] (partial)
- **Complementary Defenses**: [[defenses/differential-privacy]], [[defenses/secure-rl-algorithms]]
- **Vulnerabilities**: Model poisoning via malicious clients (requires Byzantine-robust aggregation)

---

*Source: Adversarial AI - Attacks, Mitigations, and Defense Strategies (Chapter 10: Privacy-Preserving ML, p233-261)*
