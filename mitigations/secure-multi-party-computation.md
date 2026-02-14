---
title: "Secure Multi-Party Computation"
tags:
  - type/mitigation
  - target/ml-model
  - target/training-pipeline
  - source/generative-ai-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Secure Multi-Party Computation

## Summary

Secure Multi-Party Computation (SMPC) enables multiple parties to collaboratively train machine learning models without any single party accessing the complete dataset or model. Using cryptographic protocols, SMPC distributes model training across participants such that each party computes on encrypted data shares, preventing model tampering by ensuring no individual actor can manipulate the entire model unilaterally. This mitigation is critical for federated learning scenarios and collaborative training where trust is distributed.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0047 | [[techniques/model-tampering]] | Prevents unilateral model tampering by distributing training computation; no single party can modify complete model weights |
| AML.T0020 | [[techniques/data-poisoning-attacks]] | Cryptographic verification of data shares makes it harder for single party to poison training data undetected |
| | [[techniques/federated-learning-attacks]] | SMPC protocols detect malicious model updates from compromised participants |

## Implementation

### SMPC Protocol for Distributed Training

**Core cryptographic primitives:**

1. **Secret sharing**: Split sensitive values into shares distributed across parties
2. **Homomorphic encryption**: Perform computations on encrypted data
3. **Secure aggregation**: Combine model updates without revealing individual contributions
4. **Verification protocols**: Cryptographically prove computations were performed correctly

### Secure Aggregation in Federated Learning

**Prevent single party from seeing or manipulating individual updates:**

```python
from typing import List
import numpy as np
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.primitives.kdf.pbkdf2 import PBKDF2HMAC

class SecureAggregation:
    """Federated learning with secure aggregation"""
    
    def __init__(self, num_parties, threshold):
        self.num_parties = num_parties
        self.threshold = threshold  # Minimum parties needed for reconstruction
        self.party_ids = list(range(num_parties))
    
    def generate_secret_shares(self, secret, num_shares, threshold):
        """Shamir's Secret Sharing"""
        
        # Generate random polynomial coefficients
        coefficients = [secret] + [
            np.random.randint(0, 2**32) for _ in range(threshold - 1)
        ]
        
        # Evaluate polynomial at different points to create shares
        shares = []
        for i in range(1, num_shares + 1):
            share = sum(
                coef * (i ** power) 
                for power, coef in enumerate(coefficients)
            ) % (2**32)
            shares.append((i, share))
        
        return shares
    
    def reconstruct_secret(self, shares):
        """Reconstruct secret from shares using Lagrange interpolation"""
        
        def lagrange_basis(x, x_values, j):
            """Lagrange basis polynomial"""
            numerator = 1
            denominator = 1
            for m, x_m in enumerate(x_values):
                if m != j:
                    numerator *= (x - x_m)
                    denominator *= (x_values[j] - x_m)
            return numerator / denominator
        
        x_values = [share[0] for share in shares]
        y_values = [share[1] for share in shares]
        
        # Reconstruct at x=0 (the secret)
        secret = sum(
            y_values[j] * lagrange_basis(0, x_values, j)
            for j in range(len(shares))
        ) % (2**32)
        
        return int(secret)
    
    def secure_aggregate(self, model_updates: List[np.ndarray]):
        """
        Aggregate model updates from parties without revealing individual updates
        """
        
        # Each party generates pairwise masks with other parties
        # Masks cancel out during aggregation, hiding individual contributions
        
        masked_updates = []
        
        for party_id, update in enumerate(model_updates):
            # Generate mask for this party
            mask = np.zeros_like(update)
            
            # Create pairwise masks with all other parties
            for other_party in range(self.num_parties):
                if other_party != party_id:
                    # Derive shared mask using agreed seed
                    seed = self._derive_pairwise_seed(party_id, other_party)
                    np.random.seed(seed)
                    pairwise_mask = np.random.randn(*update.shape)
                    
                    # Add or subtract mask depending on party order
                    if party_id < other_party:
                        mask += pairwise_mask
                    else:
                        mask -= pairwise_mask
            
            # Add mask to update
            masked_update = update + mask
            masked_updates.append(masked_update)
        
        # Aggregate masked updates
        # Pairwise masks cancel out, leaving sum of original updates
        aggregated = sum(masked_updates)
        
        # Divide by number of parties to get average
        return aggregated / len(model_updates)
    
    def _derive_pairwise_seed(self, party_a, party_b):
        """Derive deterministic seed for pairwise mask generation"""
        # Ensure same seed regardless of call order
        key = f"{min(party_a, party_b)}-{max(party_a, party_b)}"
        return hash(key) % (2**32)

# Example usage in federated learning
def federated_training_round(participants, secure_agg):
    """Execute one round of secure federated training"""
    
    # Each participant computes local model update
    local_updates = []
    for participant in participants:
        local_data = participant.get_training_data()
        local_update = participant.compute_gradient(local_data)
        local_updates.append(local_update)
    
    # Securely aggregate without revealing individual updates
    global_update = secure_agg.secure_aggregate(local_updates)
    
    # Update global model
    global_model.apply_update(global_update)
    
    return global_model
```

### Verification of Correct Computation

**Zero-knowledge proofs to verify parties computed honestly:**

```python
class SMPCVerification:
    """Verify SMPC participants computed correctly without revealing data"""
    
    def commit_to_update(self, model_update):
        """Create cryptographic commitment to model update"""
        import hashlib
        
        # Serialize update
        update_bytes = model_update.tobytes()
        
        # Generate random nonce
        nonce = os.urandom(32)
        
        # Compute commitment
        commitment = hashlib.sha256(update_bytes + nonce).hexdigest()
        
        return {
            'commitment': commitment,
            'nonce': nonce,
            'update': model_update
        }
    
    def verify_commitment(self, commitment_data, revealed_update):
        """Verify revealed update matches commitment"""
        import hashlib
        
        # Recompute commitment
        update_bytes = revealed_update.tobytes()
        recomputed = hashlib.sha256(
            update_bytes + commitment_data['nonce']
        ).hexdigest()
        
        return recomputed == commitment_data['commitment']
    
    def verify_aggregation_correctness(
        self, 
        individual_commitments, 
        aggregated_result,
        threshold_shares
    ):
        """
        Verify aggregation was computed correctly using threshold shares
        """
        
        # Parties reveal subset of their updates for verification
        revealed_updates = [
            commitment['update'] 
            for commitment in threshold_shares
        ]
        
        # Verify commitments
        for i, commitment in enumerate(threshold_shares):
            if not self.verify_commitment(commitment, revealed_updates[i]):
                raise VerificationError(
                    f'Commitment verification failed for party {i}'
                )
        
        # Recompute aggregation on revealed subset
        expected_aggregate = sum(revealed_updates) / len(revealed_updates)
        
        # Check against claimed result (statistical verification)
        if not np.allclose(aggregated_result, expected_aggregate, rtol=0.01):
            raise VerificationError('Aggregation verification failed')
        
        return True
```

### Byzantine-Robust Aggregation

**Detect and exclude malicious parties attempting to poison aggregation:**

```python
def byzantine_robust_aggregation(model_updates, max_byzantine):
    """
    Aggregate model updates while tolerating byzantine (malicious) parties
    Using Krum algorithm
    """
    
    n = len(model_updates)
    
    # Compute pairwise distances
    distances = np.zeros((n, n))
    for i in range(n):
        for j in range(i + 1, n):
            dist = np.linalg.norm(model_updates[i] - model_updates[j])
            distances[i, j] = dist
            distances[j, i] = dist
    
    # For each update, sum distances to k-max_byzantine-2 nearest neighbors
    k = n - max_byzantine - 2
    scores = []
    
    for i in range(n):
        # Get k nearest neighbors
        sorted_distances = sorted(distances[i])
        score = sum(sorted_distances[:k])
        scores.append(score)
    
    # Select update with minimum score (most similar to others)
    selected_idx = np.argmin(scores)
    
    log_security_event({
        'event': 'byzantine_robust_aggregation',
        'selected_party': selected_idx,
        'score': scores[selected_idx],
        'all_scores': scores
    })
    
    return model_updates[selected_idx]
```

## Limitations & Trade-offs

**Limitations:**
- **Computational overhead**: SMPC protocols require significantly more computation than plaintext training (10-100x slower)
- **Communication overhead**: Cryptographic protocols involve extensive network communication between parties
- **Complexity**: Implementation and debugging are substantially more complex than standard training
- **Trust assumptions**: Requires threshold of honest parties; if too many parties are compromised, security breaks down

**Trade-offs:**
- **Privacy vs. Performance**: Strong cryptographic guarantees come at significant performance cost
- **Fault tolerance vs. Security**: More lenient threshold settings improve availability but reduce security
- **Transparency vs. Privacy**: Verification mechanisms may leak some information about individual contributions

**Bypass Scenarios:**
- **Collusion**: If more parties than threshold collude, they can reconstruct secrets or manipulate aggregation
- **Side-channel attacks**: Timing or network traffic analysis might leak information about individual updates
- **Implementation bugs**: Cryptographic protocol vulnerabilities can completely undermine security

## Testing & Validation

**Functional Testing:**
1. **Secret sharing**: Verify shares can reconstruct original secret
2. **Secure aggregation**: Confirm aggregated result matches sum of inputs without revealing individual contributions
3. **Threshold security**: Verify that fewer than threshold shares cannot reconstruct secret

**Security Testing:**
1. **Malicious party simulation**: Inject incorrect updates and verify detection
2. **Collusion testing**: Simulate colluding parties attempting to extract other parties' data
3. **Byzantine attack**: Test robustness against parties submitting poisoned updates
4. **Communication interception**: Verify encrypted messages don't leak sensitive information

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Secure Multi-Party Computation (SMPC): For collaborative training, use cryptographic protocols preventing any single party from tampering."
> — Extracted from Model Tampering mitigation section
