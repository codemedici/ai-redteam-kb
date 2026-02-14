---
title: "Anonymization Techniques"
tags:
  - type/mitigation
  - target/training-pipeline
  - target/ml-model
  - target/rag
  - source/red-teaming-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Anonymization Techniques

## Summary

Anonymization techniques (k-anonymity, ℓ-diversity, t-closeness) attempt to protect individual privacy in released datasets by ensuring that records cannot be uniquely identified through quasi-identifiers. These techniques modify or generalize data so that each individual is indistinguishable from at least k-1 others (k-anonymity), sensitive attributes have diverse values within each equivalence class (ℓ-diversity), or the distribution of sensitive attributes in each class is close to the overall distribution (t-closeness). While widely used, these techniques have **significant limitations** and can provide a false sense of security if attacker auxiliary knowledge is underestimated.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/pii-in-corpus]] | Prevents PII exposure by ensuring training data and knowledge bases contain only anonymized records |
| | [[techniques/privacy-attacks-beyond-membership-inference]] | Reduces linkage attack success by making quasi-identifier combinations non-unique, though effectiveness depends on assumptions about attacker's auxiliary knowledge |
| AML.T0056 | [[techniques/training-data-memorization]] | k-anonymity, l-diversity, t-closeness applied before training; aggregated statistics over individual records reduce memorization and re-identification risk |

## Implementation

### k-Anonymity

Each record in the dataset is indistinguishable from at least k-1 other records with respect to quasi-identifiers (e.g., zip code, age, gender):

- **Generalization**: Replace specific values with ranges (exact age → age bracket, full zip code → zip prefix)
- **Suppression**: Remove records that cannot be generalized without excessive information loss
- Choose k based on risk tolerance (k=5 is common minimum; higher k for more sensitive data)

### ℓ-Diversity

Within each k-anonymous equivalence class, ensure at least ℓ distinct values for each sensitive attribute:

- Prevents homogeneity attacks where all records in an equivalence class share the same sensitive value
- More protective than k-anonymity alone but harder to achieve without significant data utility loss

### t-Closeness

Ensure the distribution of sensitive attributes within each equivalence class is within distance t of the overall dataset distribution:

- Prevents skewness attacks where the distribution within a class reveals information
- Strongest of the three but most restrictive on data utility

### Data Masking

Data masking replaces sensitive data with realistic but synthetic data that maintains the statistical properties of the original dataset for analysis purposes:

**Common Masking Techniques:**

1. **Substitution**: Replace real values with realistic fake values from lookup tables
   - Names → Random names from census data
   - Addresses → Fake but valid addresses
   - Phone numbers → Generated valid phone formats

2. **Shuffling**: Rearrange values within a column
   - Preserves distribution but breaks individual associations
   - Example: Shuffle names across records (breaks name-to-record linkage)

3. **Nulling/Deletion**: Replace sensitive values with NULL or empty strings
   - Simplest approach but reduces data utility
   - Use when value not needed for analysis

4. **Masking**: Partially hide sensitive data
   - Credit card: 1234-****-****-5678
   - Email: j***@example.com
   - Preserves some utility while hiding full value

5. **Encryption/Hashing**: Replace sensitive values with encrypted or hashed versions
   - One-way transformation (cannot reverse without key)
   - Preserves uniqueness for joins/deduplication
   - Example: Hash SSNs for consistent pseudonymization

6. **Number Variance**: Add random noise to numeric fields
   - Age: Add random ±2 years
   - Salary: Add ±5% noise
   - Preserves statistical distributions, obscures exact values

**Implementation Example:**

```python
import random
from faker import Faker

fake = Faker()

def mask_pii(record):
    """
    Apply data masking to PII fields.
    """
    masked_record = record.copy()
    
    # Substitution: Replace real names with fake ones
    if 'name' in record:
        masked_record['name'] = fake.name()
    
    # Substitution: Replace real addresses
    if 'address' in record:
        masked_record['address'] = fake.address()
    
    # Masking: Partial email redaction
    if 'email' in record:
        email = record['email']
        masked_record['email'] = email[0] + '***@' + email.split('@')[1]
    
    # Number variance: Add noise to age
    if 'age' in record:
        noise = random.randint(-2, 2)
        masked_record['age'] = max(18, record['age'] + noise)  # Ensure age >= 18
    
    return masked_record
```

**Use Cases:**
- Non-production environments (dev, test, staging) need realistic data without real PII
- Training datasets where exact values not needed but statistical properties are
- Shared datasets for research collaboration where privacy is required

**Advantages:**
- Maintains data utility for development and testing
- Realistic enough for functional testing
- Significantly reduces privacy risk

**Limitations:**
- Masked data can sometimes be reverse-engineered (especially with auxiliary data)
- Does not provide formal privacy guarantees (unlike differential privacy)
- Requires careful selection of masking techniques to avoid introducing bias

### Application to AI Systems

- Apply anonymization (k-anonymity, ℓ-diversity, t-closeness) before training if training data may be released or queried
- Use data masking for non-production training datasets (dev/test environments)
- Apply to any aggregate statistics or synthetic data released from AI systems
- Combine with [[mitigations/differential-privacy|differential privacy]] for stronger guarantees

## Limitations & Trade-offs

- **⚠️ False sense of security**: Anonymization techniques like k-anonymity can provide a false sense of security; effectiveness depends entirely on assumptions about the attacker's external knowledge, which is often underestimated
- **Auxiliary data vulnerability**: A determined attacker combining multiple public or leaked datasets can frequently break simplistic anonymization
- **Utility loss**: Generalization and suppression reduce data utility, potentially degrading model performance
- **Static assumptions**: These techniques assume a fixed set of quasi-identifiers; new external data sources can introduce new quasi-identifiers not considered during anonymization
- **Not composable**: Repeated releases of anonymized datasets from the same source can be combined to de-anonymize (unlike differential privacy which composes)

## Testing & Validation

- Attempt linkage attacks using publicly available external datasets against anonymized data
- Verify k-anonymity, ℓ-diversity, and t-closeness properties hold using automated validation tools
- Test with realistic attacker auxiliary knowledge (not just minimal assumptions)
- Assume adversaries may have more auxiliary data than initially expected

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Apply Robust Anonymization (Carefully): Use techniques like k-anonymity, ℓ-diversity, t-closeness before data release... Crucially, understand their limitations: Often fail if attacker possesses auxiliary data not considered during anonymization. WARNING: Anonymization techniques like k-anonymity can provide false sense of security."
> — [[sources/bibliography#Red-Teaming AI]], p. 315-341

> "Classic Example: Zip Code + Date of Birth + Gender uniquely identifies a large percentage of the US population."
> — [[sources/bibliography#Red-Teaming AI]], p. 315-341
