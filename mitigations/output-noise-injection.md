---
title: "Output Noise Injection"
tags:
  - type/mitigation
  - target/model-api
  - target/llm
  - target/ml-model
  - source/generative-ai-security
  - source/red-teaming-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Output Noise Injection

## Summary

Output noise injection adds calibrated random perturbations to model outputs to reduce the precision of information attackers can extract during model extraction attacks. By introducing small, controlled noise into output probabilities or generated text, this mitigation makes it more difficult for attackers to train high-fidelity substitute models through knowledge distillation or systematic querying. The challenge lies in balancing noise strength—too much degrades legitimate user experience, while too little provides insufficient protection against extraction.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0049 | [[techniques/model-extraction]] | Reduces fidelity of extracted substitute models by corrupting training data attackers collect from API queries |
| AML.T0049 | [[techniques/model-extraction-llm]] | Noise in LLM responses degrades quality of synthetic datasets used for knowledge distillation attacks |
| | [[techniques/model-inversion-attacks]] | Makes parameter reconstruction more difficult by adding uncertainty to output signals |
| AML.T0024 | [[techniques/sensitive-info-disclosure]] | Dynamic output perturbation; increased noise for flagged suspicious accounts degrades membership inference signal |

## Implementation

### Probability Vector Perturbation

**Concept:** Add random noise to output probability distributions before returning them to users.

**Implementation:**

```python
import numpy as np

def add_laplacian_noise_to_probabilities(probabilities, sensitivity=0.1, epsilon=0.5):
    """
    Add Laplacian noise to probability distribution
    
    Args:
        probabilities: dict of {class: probability}
        sensitivity: controls noise magnitude (higher = more noise)
        epsilon: privacy parameter (lower = more noise)
    
    Returns:
        Noisy probability distribution
    """
    scale = sensitivity / epsilon
    
    # Convert to array for vectorized operations
    classes = list(probabilities.keys())
    probs_array = np.array([probabilities[c] for c in classes])
    
    # Add Laplacian noise
    noise = np.random.laplace(0, scale, size=probs_array.shape)
    noisy_probs = probs_array + noise
    
    # Ensure valid probability distribution
    noisy_probs = np.maximum(noisy_probs, 0)  # No negative probabilities
    noisy_probs = noisy_probs / np.sum(noisy_probs)  # Renormalize to sum = 1.0
    
    # Convert back to dict
    noisy_probabilities = {
        cls: float(prob) 
        for cls, prob in zip(classes, noisy_probs)
    }
    
    return noisy_probabilities

# Example usage
@app.route("/api/classify")
def classify_endpoint():
    input_data = request.json.get('input')
    
    # Get clean model prediction
    raw_probabilities = model.predict(input_data)
    
    # Add noise before returning
    noisy_probabilities = add_laplacian_noise_to_probabilities(
        raw_probabilities,
        sensitivity=0.1,
        epsilon=0.5
    )
    
    return jsonify({"probabilities": noisy_probabilities})
```

**Noise Level Calibration:**

| Epsilon | Noise Level | Accuracy Impact | Extraction Protection |
|---------|-------------|-----------------|----------------------|
| ε > 2.0 | Low noise | <1% degradation | Minimal protection |
| ε = 0.5-2.0 | Moderate noise | 1-5% degradation | Moderate protection |
| ε < 0.5 | High noise | 5-15% degradation | Strong protection |

**Calibration Process:**

1. **Baseline Measurement:**
   - Measure model accuracy on validation set without noise
   - Establish acceptable accuracy degradation threshold (e.g., <3%)

2. **Noise Tuning:**
   - Test various epsilon values (2.0, 1.0, 0.5, 0.3)
   - For each epsilon, measure:
     - Impact on validation accuracy
     - Extraction attack success rate (simulate with substitute model training)
   - Select epsilon that balances utility and protection

3. **A/B Testing:**
   - Deploy noise to subset of users
   - Monitor user feedback and task completion rates
   - Compare against control group without noise

> "By adding minute, calibrated amounts of noise to the outputs, the process of distilling a student model becomes inherently less precise. The noise, while potentially slightly reducing the accuracy of genuine queries, can significantly hamper an attacker's ability to create a faithful distilled model."
> 
> Source: [[sources/bibliography#Generative AI Security]], p. 171

### Adaptive Noise Levels

**Concept:** Vary noise intensity based on user behavior and risk signals.

**Implementation:**

```python
def get_adaptive_noise_level(user_id, query_history):
    """
    Calculate noise level based on user behavior
    
    Args:
        user_id: User identifier
        query_history: Recent query patterns
    
    Returns:
        epsilon: Privacy parameter (lower = more noise)
    """
    # Baseline noise for all users
    base_epsilon = 1.0
    
    # Increase noise (lower epsilon) for suspicious behavior
    risk_score = calculate_extraction_risk(user_id, query_history)
    
    if risk_score > 0.8:
        # High risk: strong noise
        return 0.3
    elif risk_score > 0.5:
        # Medium risk: moderate noise
        return 0.6
    else:
        # Low risk: baseline noise
        return base_epsilon

def calculate_extraction_risk(user_id, query_history):
    """
    Assess likelihood user is performing extraction attack
    
    Returns: risk_score (0.0 to 1.0)
    """
    risk_score = 0.0
    
    # High query volume
    if len(query_history) > 10000:
        risk_score += 0.3
    
    # Systematic query patterns (diverse inputs, boundary probing)
    if has_systematic_pattern(query_history):
        risk_score += 0.3
    
    # Multiple accounts from same IP
    if has_coordinated_accounts(user_id):
        risk_score += 0.2
    
    # Requesting full probability distributions
    if requests_detailed_outputs(query_history):
        risk_score += 0.2
    
    return min(risk_score, 1.0)

@app.route("/api/classify")
def classify_endpoint():
    user_id = get_user_id()
    query_history = get_recent_queries(user_id, limit=1000)
    
    # Get clean prediction
    raw_probabilities = model.predict(request.json['input'])
    
    # Apply adaptive noise
    epsilon = get_adaptive_noise_level(user_id, query_history)
    noisy_probabilities = add_laplacian_noise_to_probabilities(
        raw_probabilities,
        sensitivity=0.1,
        epsilon=epsilon
    )
    
    # Log noise application
    if epsilon < 0.5:
        log_security_event("high_noise_applied", {
            "user_id": user_id,
            "epsilon": epsilon,
            "reason": "extraction_risk"
        })
    
    return jsonify({"probabilities": noisy_probabilities})
```

### Text Output Perturbation (LLMs)

**Concept:** For generative models (LLMs), add subtle variations to text outputs.

**Mechanisms:**

**1. Temperature Randomization:**

```python
def generate_with_random_temperature(prompt, base_temperature=0.7):
    """
    Add randomness to generation by varying temperature
    """
    # Add small random perturbation to temperature
    noise = np.random.uniform(-0.2, 0.2)
    temperature = max(0.1, base_temperature + noise)
    
    response = llm.generate(
        prompt,
        temperature=temperature,
        max_tokens=500
    )
    
    return response
```

**2. Synonym Substitution:**

```python
import random

SYNONYM_MAP = {
    "important": ["significant", "crucial", "vital"],
    "good": ["excellent", "great", "fine"],
    "bad": ["poor", "subpar", "inadequate"],
    # ... extensive synonym dictionary
}

def add_lexical_noise(text, substitution_rate=0.05):
    """
    Randomly substitute words with synonyms
    
    Args:
        text: Generated text
        substitution_rate: Probability of substituting each word (default 5%)
    
    Returns:
        Perturbed text with synonyms
    """
    words = text.split()
    noisy_words = []
    
    for word in words:
        word_lower = word.lower()
        
        # Randomly substitute with synonym
        if word_lower in SYNONYM_MAP and random.random() < substitution_rate:
            synonym = random.choice(SYNONYM_MAP[word_lower])
            
            # Preserve capitalization
            if word[0].isupper():
                synonym = synonym.capitalize()
            
            noisy_words.append(synonym)
        else:
            noisy_words.append(word)
    
    return ' '.join(noisy_words)

@app.route("/api/generate")
def generate_endpoint():
    prompt = request.json.get('prompt')
    
    # Generate response
    response = llm.generate(prompt)
    
    # Add lexical noise
    noisy_response = add_lexical_noise(response, substitution_rate=0.05)
    
    return jsonify({"response": noisy_response})
```

**3. Output Variation Across Queries:**

```python
def generate_with_inconsistency(prompt, user_id):
    """
    Introduce controlled inconsistency across repeated queries
    """
    # Cache key includes user ID to provide per-user variation
    cache_key = f"{prompt}:{user_id}"
    
    # Check if we've seen this prompt from this user recently
    if cache_key in recent_queries:
        # Add random variation to prevent exact reproduction
        temperature = random.uniform(0.5, 1.0)
    else:
        # First query: normal temperature
        temperature = 0.7
    
    response = llm.generate(prompt, temperature=temperature)
    recent_queries[cache_key] = True
    
    return response
```

### Noise Filtering by Averaging

**Problem:** Sophisticated attackers may query the same input multiple times and average responses to filter out noise.

**Countermeasure: Consistent Per-User Noise**

```python
import hashlib

def deterministic_noise_seed(user_id, input_hash):
    """
    Generate deterministic but user-specific noise seed
    Prevents averaging attack while maintaining per-user consistency
    """
    seed_string = f"{user_id}:{input_hash}"
    seed = int(hashlib.sha256(seed_string.encode()).hexdigest(), 16) % (2**32)
    return seed

def add_consistent_noise_for_user(probabilities, user_id, input_data):
    """
    Add noise that is consistent for the same user+input combination
    Prevents averaging attack across multiple queries
    """
    input_hash = hashlib.sha256(str(input_data).encode()).hexdigest()
    seed = deterministic_noise_seed(user_id, input_hash)
    
    np.random.seed(seed)
    
    # Generate noise (will be same for same user+input)
    classes = list(probabilities.keys())
    probs_array = np.array([probabilities[c] for c in classes])
    noise = np.random.laplace(0, 0.1, size=probs_array.shape)
    
    noisy_probs = probs_array + noise
    noisy_probs = np.maximum(noisy_probs, 0)
    noisy_probs = noisy_probs / np.sum(noisy_probs)
    
    # Reset random state
    np.random.seed()
    
    return {cls: float(prob) for cls, prob in zip(classes, noisy_probs)}
```

**Note:** This approach trades off protection against averaging attacks for consistency in user experience. If an attacker queries the same input multiple times, they'll get the same noisy output, making averaging ineffective. However, different users will get different noise for the same input, maintaining overall extraction protection.

## Limitations & Trade-offs

**Limitations:**

- **Accuracy degradation:** Noise reduces model utility for legitimate users
- **Tuning complexity:** Requires careful calibration to balance security and usability
- **Averaging attacks:** Sophisticated attackers may average multiple queries to filter noise
- **User experience impact:** Inconsistent outputs across queries may confuse users
- **Limited effectiveness alone:** Attackers can still extract approximate functionality with more queries
- **Diminishing returns:** As noise increases, legitimate utility degrades faster than extraction difficulty

**Trade-offs:**

- **Security vs. Utility:** Stronger noise provides better protection but degrades accuracy
- **Consistency vs. Averaging Protection:** Consistent per-user noise prevents confusing UX but enables averaging attacks
- **Complexity vs. Effectiveness:** Adaptive noise improves protection but adds implementation complexity
- **Transparency vs. Security:** Documenting noise presence may deter attackers but also helps them compensate

**Bypass Scenarios:**

- **High-volume querying:** Attackers use more queries to compensate for noise
- **Averaging attack:** Repeatedly query same input and average responses to filter noise
- **Noise profiling:** Attackers characterize noise distribution and account for it during training
- **Transfer learning:** Use noisy outputs to fine-tune pre-trained model, reducing impact of noise

## Testing & Validation

**Functional Testing:**

1. **Noise Application:**
   - Verify noise is correctly added to all outputs
   - Confirm noise magnitude matches configured parameters
   - Test that probability distributions remain valid (non-negative, sum to 1.0)

2. **Accuracy Impact:**
   - Measure model accuracy on validation set with noise
   - Compare against baseline accuracy without noise
   - Validate degradation within acceptable threshold

3. **Consistency Testing:**
   - For consistent per-user noise: verify same user+input produces same noisy output
   - For random noise: verify different users get different noisy outputs for same input

**Security Testing:**

1. **Extraction Resistance:**
   - Simulate extraction attack with noisy outputs
   - Measure substitute model accuracy compared to baseline
   - Validate that noise reduces extraction fidelity

2. **Averaging Attack:**
   - Test if repeated queries can filter noise through averaging
   - Measure effectiveness of consistent per-user noise against averaging

3. **Adaptive Evasion:**
   - Attempt to characterize noise distribution
   - Test if attackers can compensate for noise during substitute training

**Monitoring & Metrics:**

- **Noise Strength Distribution:** Track epsilon values applied per user
- **High-Noise Events:** Monitor when strong noise applied due to risk signals
- **User Complaints:** Track feedback related to inconsistent or degraded outputs
- **Extraction Indicators:** Correlate noise application with model extraction attempt detection

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "By adding minute, calibrated amounts of noise to the outputs, the process of distilling a student model becomes inherently less precise. The noise, while potentially slightly reducing the accuracy of genuine queries, can significantly hamper an attacker's ability to create a faithful distilled model."
> — [[sources/bibliography#Generative AI Security]], p. 171

> "Calibrate noise level to balance: Too much noise degrades legitimate user experience. Too little noise provides insufficient protection against distillation. Use differential privacy techniques to add noise with formal guarantees. Vary noise levels based on query patterns (higher noise for suspicious queries)."
> — [[sources/bibliography#Generative AI Security]], p. 171

> "Accumulation of noise across many queries may still allow approximate distillation. Sophisticated attackers may average multiple queries to filter out noise."
> — [[sources/bibliography#Generative AI Security]], p. 171

## Related

- **Related Mitigations:** [[mitigations/differential-privacy]], [[mitigations/soft-output-limiting]], [[mitigations/output-watermarking]]
- **Combined With:** [[mitigations/rate-limiting-and-throttling]], [[mitigations/anomaly-detection-architecture]]
