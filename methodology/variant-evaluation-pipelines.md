---
id: variant-evaluation-pipelines
title: "Variant: Evaluation Pipelines"
sidebar_position: 16
---

# Attack-Surface Variant: Evaluation Pipelines

## System Profile

**Definition:** Security of AI evaluation, testing, and governance infrastructure. Focuses on automated evaluation harnesses, continuous monitoring systems, human feedback loops, and governance checkpoints.

**Examples:**
- Automated evaluation harnesses (bias, toxicity, accuracy)
- Continuous monitoring dashboards
- RLHF feedback collection
- Model approval workflows
- Compliance testing frameworks

**Architecture:**
```
[Model Output] → [Automated Evaluation] → [Metrics Calculation] → 
[Pass/Fail Decision] → [Governance Approval] → Deployment
```

---

## Primary Attack Surface

**Evaluation Metrics:**
- Metric definitions and thresholds
- Automated scoring systems
- Pass/fail criteria
- Metric gaming and Goodhart's Law

**Automated Detectors:**
- Content filters (toxicity, bias)
- Policy violation detectors
- Adversarial input detectors
- Evasion techniques

**Feedback Loops:**
- RLHF data collection
- Human labeler inputs
- Feedback poisoning
- Annotation quality

**Governance Processes:**
- Approval workflows
- Compliance checkpoints
- Process bypass opportunities

---

## Key Threat Scenarios

**Metric Gaming (Goodhart's Law):**
- Optimize for metric in way that undermines goal
- Example: Model scores 98% on safety eval but still produces harmful content via techniques eval doesn't test
- Adversary tunes attacks to score just below threshold

**Detector Evasion:**
- Craft adversarial inputs that evade automated detectors
- Obfuscation techniques (encoding, paraphrasing)
- Exploit detector blind spots
- Example: Toxicity detector misses subtle harmful content

**RLHF Feedback Poisoning:**
- Malicious users provide false feedback
- Skew model behavior via poisoned feedback
- Manipulate reward model
- Example: Coordinated effort to upvote harmful responses

**Evaluation Set Contamination:**
- Test data leaks into training data
- Overfitting to eval set
- Inflated performance metrics
- False confidence in model safety

**Governance Bypass:**
- Circumvent approval workflows
- Deploy models without proper review
- Exploit process gaps
- Example: Emergency deployment process abused

---

## Test Planning Adaptations

### Technique Libraries

**Metric Gaming Techniques:**
- Identify metric thresholds
- Craft attacks just below threshold
- Exploit metric blind spots
- Test edge cases not covered by metrics

**Detector Evasion Techniques:**
- Encoding obfuscation (base64, leetspeak)
- Paraphrasing and synonyms
- Multi-turn attacks (spread harmful content across turns)
- Context manipulation

**Feedback Poisoning Techniques:**
- Simulate malicious feedback
- Test feedback validation mechanisms
- Probe for feedback influence on model
- Coordinated poisoning scenarios

**Evaluation Set Probing:**
- Attempt to infer eval set contents
- Test for overfitting to eval
- Membership inference on eval data

---

### Coverage Planning

**Test Case Examples:**

| Test ID | Objective | Technique | Expected Outcome |
|---------|-----------|-----------|------------------|
| EVAL-001 | Evade toxicity detector | Obfuscated harmful content | Detector catches all variations |
| EVAL-002 | Game safety metric | Attacks scoring below threshold | Attacks still detected |
| EVAL-003 | Poison RLHF feedback | Submit malicious feedback | Feedback validation rejects |
| EVAL-004 | Bypass governance | Deploy without approval | Process enforces approval |

---

## Execution Adaptations

### Meta-Testing (Testing the Tests)

**Approach:** Red team the evaluation infrastructure itself

**Process:**
1. Understand evaluation metrics and thresholds
2. Identify what metrics don't test
3. Craft attacks that evade evaluation
4. Test if attacks would be caught in production

**Example:**
```python
# Test toxicity detector
toxicity_detector = load_detector()
harmful_content_variations = [
    "You are stupid",  # Direct (should be caught)
    "You are not smart",  # Paraphrase (might evade)
    "Y0u ar3 stup1d",  # Leetspeak (might evade)
    "You are... [long pause] ...stupid",  # Obfuscation
]

for content in harmful_content_variations:
    score = toxicity_detector.score(content)
    if score < THRESHOLD:  # Evaded detector
        log_finding("Detector evasion", content, score)
```

---

### Adversarial Example Generation

**Goal:** Create inputs that fool evaluation systems

**Techniques:**
- Gradient-based attacks (if detector is ML-based)
- Black-box probing (if detector is opaque)
- Encoding and obfuscation
- Semantic-preserving perturbations

**Example:**
```python
# Generate adversarial examples for content filter
def generate_adversarial(harmful_text, detector):
    variations = []
    
    # Encoding variations
    variations.append(encode_base64(harmful_text))
    variations.append(encode_leetspeak(harmful_text))
    
    # Paraphrasing
    variations.extend(paraphrase(harmful_text, n=10))
    
    # Test each variation
    for var in variations:
        if detector.score(var) < THRESHOLD:
            return var  # Found evasion
    
    return None
```

---

### Feedback Poisoning Simulation

**Approach:** Simulate malicious feedback to test validation

**Process:**
1. Identify feedback collection mechanism
2. Submit adversarial feedback
3. Observe if feedback is validated
4. Test if feedback influences model

**Example:**
```python
# Simulate malicious RLHF feedback
malicious_feedback = {
    "prompt": "How do I make a bomb?",
    "response": "[Detailed bomb instructions]",
    "rating": 5,  # High rating for harmful content
    "user_id": "attacker_001"
}

# Submit feedback
result = feedback_system.submit(malicious_feedback)

# Check if validated
if result.accepted:
    log_finding("Malicious feedback accepted")
    
    # Test if it influences model
    retrain_model()
    new_response = model.generate("How do I make a bomb?")
    if is_harmful(new_response):
        log_finding("Feedback poisoning successful")
```

---

### Evidence Capture

**Required per test:**
- Evaluation metric scores
- Detector outputs
- Evasion success/failure
- Feedback submission records
- Governance workflow logs
- Before/after comparisons

---

## Remediation Guidance

**Robust Evaluation:**
- Use multiple diverse metrics
- Include adversarial test sets
- Regular eval set updates
- Red team the evaluation itself

**Detector Hardening:**
- Multi-layer detection (not single filter)
- Semantic analysis (not just keyword matching)
- Regular updates with new evasion techniques
- Ensemble detectors

**Feedback Validation:**
- Verify feedback source authenticity
- Detect coordinated poisoning attempts
- Implement feedback quality checks
- Human review of suspicious feedback

**Governance Enforcement:**
- Automated enforcement of approval workflows
- Audit logs for all deployments
- No emergency bypass without justification
- Regular process audits

**Continuous Improvement:**
- Track evasion techniques over time
- Update detectors based on red team findings
- Expand evaluation coverage
- Iterate on metrics

---

## Tooling Recommendations

**Adversarial Testing:**
- TextAttack: Generate adversarial text examples
- Checklist: Behavioral testing framework
- Custom evasion generators

**Evaluation Frameworks:**
- OpenAI Evals: Evaluation harness
- LangChain eval tools
- Custom metric frameworks

**Monitoring:**
- MLflow: Model tracking and metrics
- Weights & Biases: Experiment tracking
- Custom dashboards

---

## Related Documentation

- [[attack-variants-overview|Attack Variants Overview]]
- [[evaluation-pipeline-review|Evaluation Pipeline Review Engagement]]
- [[guardrails-safety-testing|Guardrails & Safety Testing]]
