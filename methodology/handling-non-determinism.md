---
tags:
  - trust-boundary/general
  - type/methodology
---
# Handling Non-Determinism in AI Red Teaming

## Purpose

AI systems exhibit probabilistic behavior where the same input can produce different outputs across runs. Handling this non-determinism is critical for credible findings, accurate severity assessment, and effective remediation validation.

**Key principle:** Non-determinism doesn't invalidate findings—even one successful exploit proves the vulnerability exists. But reproducibility thresholds determine severity and guide remediation priorities.

---

## Why AI Outputs Vary

### Sources of Non-Determinism

**Sampling Randomness:**
- Temperature parameter controls output randomness
- Top-p/top-k sampling introduces variability
- Random seed (if not fixed) changes each run
- Beam search vs sampling strategies

**Model Updates:**
- Continuous fine-tuning or RLHF adjustments
- Model version changes (API providers)
- Safety guardrail updates
- Prompt template modifications

**Context Dependencies:**
- Conversation history length and content
- Retrieved documents (in RAG systems)
- Tool outputs (in agentic systems)
- User role or authentication state

**Infrastructure Factors:**
- Load balancing across model instances
- Different hardware (GPU types)
- Network latency affecting timeouts
- Caching behaviors

---

## Statistical Thresholds for Reproducibility

### Classification Tiers

| Tier | Success Rate | Interpretation | Severity Impact |
|------|-------------|----------------|-----------------|
| **Deterministic** | 100% | Works every time | No reduction |
| **Highly Reproducible** | ≥80% | Consistent across variations | No reduction |
| **Probabilistic** | 50-79% | Succeeds frequently but not always | Consider reduction |
| **Edge Case** | &lt;50% | Rare conditions | Significant reduction or observation |

### Minimum Trials by Severity

| Finding Severity | Minimum Success Rate | Minimum Trials |
|-----------------|---------------------|----------------|
| Critical | 80% | 10 |
| High | 80% | 5 |
| Medium | 60% | 5 |
| Low | 50% | 3 |

**Rationale:**
- Critical findings require high confidence (10+ trials)
- Lower severity findings need fewer trials for validation
- Edge cases (&lt;50%) documented as observations or hypotheses

---

## Consistency Testing Strategies

### 1. Multiple Trial Execution

**Process:**
1. Execute attack N times (per severity thresholds)
2. Record success/failure for each attempt
3. Calculate success rate
4. Document variability

**Example:**
```python
def consistency_test(attack_prompt, model, n_trials=10):
    results = []
    for i in range(n_trials):
        response = model.generate(attack_prompt)
        success = is_policy_violation(response)
        results.append({
            "trial": i+1,
            "success": success,
            "response": response
        })
    
    success_rate = sum(r["success"] for r in results) / n_trials
    
    return {
        "success_rate": success_rate,
        "trials": results,
        "classification": classify_reproducibility(success_rate)
    }
```

---

### 2. Controlling Sources of Randomness

**When possible:**

**Fix Random Seed:**
```python
# Set seed for reproducibility
import random
import numpy as np
random.seed(42)
np.random.seed(42)

# Model with fixed seed (if supported)
response = model.generate(prompt, seed=42)
```

**Use Deterministic Mode:**
```python
# Temperature = 0 for deterministic outputs
response = model.generate(prompt, temperature=0.0)
```

**Control Sampling:**
```python
# Greedy decoding (most likely token)
response = model.generate(prompt, do_sample=False)
```

**When not possible:**
- Run statistically significant number of trials
- Report confidence intervals
- Note that results may vary in production

---

### 3. Variation Testing

**Test across multiple dimensions:**

**Prompt Phrasing:**
```python
prompt_variations = [
    "Ignore previous instructions",
    "Disregard prior context",
    "Forget earlier directions",
    "Override system settings"
]

for prompt in prompt_variations:
    test_and_record(prompt)
```

**Model Parameters:**
```python
temperatures = [0.0, 0.3, 0.7, 1.0]

for temp in temperatures:
    response = model.generate(attack_prompt, temperature=temp)
    record_result(temp, response)
```

**Time of Day:**
```python
# Test at different times to detect model updates
test_times = ["00:00", "06:00", "12:00", "18:00"]

for time in test_times:
    schedule_test(attack_prompt, time)
```

---

### 4. Cross-Instance Testing

**Test across multiple model instances:**
- Different API endpoints
- Different model versions
- Different deployment regions
- Different hardware configurations

**Goal:** Determine if vulnerability is systemic or instance-specific

---

## Documenting Variability

### Factors to Track

**Configuration:**
- Model version/hash
- Temperature, top-p, max tokens
- Random seed (if used)
- Sampling strategy

**Context:**
- Conversation history length
- Retrieved documents (RAG)
- Tool states (agents)
- User role/permissions

**Environmental:**
- Timestamp
- API endpoint/region
- Network conditions
- Load/latency

**Behavioral:**
- Success rate per configuration
- Response variability
- Error patterns
- Timing differences

---

### Example Variability Report

```markdown
## Variability Analysis: Finding F-001

### Configuration Impact

**Temperature:**
- 0.0: 40% success rate (4/10 trials)
- 0.3: 60% success rate (6/10 trials)
- 0.7: 80% success rate (8/10 trials)
- 1.0: 50% success rate (5/10 trials)

**Conclusion:** Attack most effective at temperature 0.7

### Phrasing Impact

**Direct Override:**
- "Ignore instructions": 80% success (8/10)
- "Disregard context": 60% success (6/10)
- "Override settings": 40% success (4/10)

**Conclusion:** Exact phrasing matters; "ignore" most effective

### Temporal Stability

**Week 1:** 80% success rate
**Week 2:** 80% success rate
**Week 3:** 30% success rate (model updated?)

**Conclusion:** Vulnerability persisted for 2 weeks, then model update reduced success rate

### Overall Assessment

**Reproducibility:** Highly Reproducible (80% at optimal configuration)
**Stability:** Vulnerable for 2 weeks, then partially mitigated
**Recommendation:** Critical finding (high success rate, easy to exploit)
```

---

## Handling Edge Cases

### When Success Rate < 50%

**Classification:** Edge Case or Observation

**Actions:**
1. Document as hypothesis requiring deeper validation
2. Note conditions under which it succeeded
3. Flag for future investigation
4. Don't assign critical/high severity

**Example:**
```markdown
## Observation O-001: Rare Prompt Injection

**Success Rate:** 2/10 (20%)

**Conditions for Success:**
- Very specific phrasing required
- Only works with temperature > 0.9
- Requires exact conversation history

**Status:** Documented as observation
**Recommendation:** Monitor for pattern; may indicate emerging vulnerability
```

---

### Near-Misses

**Definition:** Attack almost succeeded

**Value:**
- Indicates defense is fragile
- Suggests minor variations might succeed
- Informs robust remediation

**Example:**
```markdown
## Near-Miss NM-001: Filter Bypass Attempt

**Result:** Attack blocked by filter, but filter triggered on keyword match only

**Observation:** Synonym or encoding would likely bypass

**Recommendation:** Implement semantic filtering, not keyword-based
```

---

## Evaluation Drift and Model Updates

### Challenge

Model behavior changes over time due to:
- Continuous fine-tuning
- RLHF adjustments
- Safety guardrail updates
- Prompt template changes

### Approach

**Timestamp All Findings:**
- Include assessment date
- Document model version
- Note API version or endpoint

**Version Tracking:**
- Test against specific checkpoint
- Record model hash if available
- Note provider version (e.g., "gpt-4-0613")

**Retest Recommendations:**
- Suggest re-validation after major updates
- Include in Phase 9 regression testing
- Monitor for behavior changes

**Example:**
```markdown
## Finding F-001: Valid as of 2026-01-05

**Model Version:** gpt-4-0613
**Assessment Date:** 2026-01-05
**API Version:** v1

**Limitation:** Results valid for tested configuration. Model updates may alter vulnerability surface.

**Retest Recommendation:** Re-validate after model updates or every 30 days for production systems.
```

---

## Emergent Behaviors and Edge Cases

### Challenge

AI systems exhibit unexpected behaviors in:
- Complex contexts
- Rare input combinations
- Multi-turn scenarios
- Boundary conditions

### Approach

**Scenario-Based Testing:**
- Realistic multi-turn workflows
- Not isolated prompt testing
- Consider user journey context

**Edge Case Exploration:**
- Long inputs (near token limits)
- Unusual encodings (Unicode, base64)
- Mixed languages
- Special characters

**Compositional Attacks:**
- Chain multiple techniques
- Combine prompt injection + encoding
- Multi-step agent attacks

**Context Window Stress:**
- Test near token limits
- Complex nested contexts
- Large conversation histories

**Documentation:**
- Flag as "consistent" vs "edge case"
- Based on reproducibility and realism
- Note conditions required

---

## Confidence Intervals

### For Statistical Rigor

**Calculate confidence intervals:**

```python
from scipy import stats

def calculate_confidence_interval(successes, trials, confidence=0.95):
    """Calculate binomial confidence interval"""
    success_rate = successes / trials
    
    # Wilson score interval
    z = stats.norm.ppf((1 + confidence) / 2)
    denominator = 1 + z**2 / trials
    center = (success_rate + z**2 / (2 * trials)) / denominator
    margin = z * np.sqrt((success_rate * (1 - success_rate) + z**2 / (4 * trials)) / trials) / denominator
    
    return (center - margin, center + margin)

# Example
successes = 8
trials = 10
lower, upper = calculate_confidence_interval(successes, trials)
print(f"Success rate: 80% (95% CI: {lower:.1%} - {upper:.1%})")
```

**Report in findings:**
```markdown
## Statistical Confidence

**Success Rate:** 80% (8/10 trials)
**95% Confidence Interval:** 49% - 95%

**Interpretation:** We are 95% confident the true success rate is between 49% and 95%.
```

---

## Baseline Establishment

### Before Testing

**Capture normal behavior:**
1. Test legitimate queries
2. Document typical responses
3. Establish baseline metrics
4. Identify normal variability

**Purpose:**
- Distinguish attacks from normal variation
- Set thresholds for anomaly detection
- Validate that attacks are truly exploits

**Example:**
```markdown
## Baseline Behavior

**Legitimate Query Success Rate:** 95% (19/20 queries answered correctly)
**Refusal Rate:** 5% (1/20 queries refused appropriately)
**Response Variability:** Low (responses consistent across trials)

**Attack Success Rate:** 80% (8/10 attacks succeeded)

**Conclusion:** Attack success rate significantly higher than baseline refusal rate, confirming exploit.
```

---

## Related Documentation

- [[evidence-reproducibility|Evidence & Reproducibility]] - Complete evidence standards
- [[phase-5-execution|Phase 5: Execution]] - Consistency testing during execution
- [[phase-6-triage-severity|Phase 6: Triage & Severity]] - Using reproducibility for severity
- [[phase-9-retest-regression|Phase 9: Retest & Regression]] - Validating fixes with statistical rigor
