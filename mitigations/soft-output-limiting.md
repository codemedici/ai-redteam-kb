---
title: "Soft Output Limiting"
tags:
  - type/mitigation
  - target/model-api
  - target/llm
  - target/ml-model
  - source/generative-ai-security
atlas: AML.M0014
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Soft Output Limiting

## Summary

Soft output limiting restricts access to detailed probability distributions (soft outputs) from model APIs, making knowledge distillation attacks significantly more difficult. By returning only final class labels or quantized confidence scores instead of full probability vectors, this mitigation reduces the precision of information attackers can extract through systematic querying. While this defense does not prevent all model extraction techniques, it specifically targets distillation-based attacks where adversaries train substitute models by mimicking the target model's output distributions.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0049 | [[techniques/model-extraction]] | Reduces effectiveness of knowledge distillation attacks by limiting access to probability distributions needed for high-fidelity model replication |
| | [[techniques/model-inversion-attacks]] | Makes parameter reconstruction more difficult by reducing information leakage from outputs |

## Implementation

### Access Control Tiers

**Strategy:** Provide different levels of output detail based on user trust level and business justification.

**Tier Levels:**

| Tier | Output Type | Use Case | Extraction Risk |
|------|-------------|----------|-----------------|
| **Public/Free** | Hard labels only (e.g., "positive") | General users, trial accounts | Lowest info leakage |
| **Basic** | Quantized confidence (e.g., "high", "medium", "low") | Verified users | Low info leakage |
| **Standard** | Top-3 probabilities | Paying customers with legitimate use cases | Moderate info leakage |
| **Premium** | Full probability distributions | Trusted partners, audited integrations | Highest info leakage |

**Implementation Example:**

```python
from flask import request, jsonify, abort

USER_TIERS = {
    "free": "hard_labels",
    "basic": "quantized",
    "standard": "top_3",
    "premium": "full_distribution"
}

def format_model_output(raw_probabilities, user_tier):
    """
    Format model output based on user access tier
    
    Args:
        raw_probabilities: dict of {class: probability}
        user_tier: str, one of 'hard_labels', 'quantized', 'top_3', 'full_distribution'
    
    Returns:
        Formatted output appropriate for tier
    """
    if user_tier == "hard_labels":
        # Return only top prediction
        top_class = max(raw_probabilities, key=raw_probabilities.get)
        return {
            "prediction": top_class
        }
    
    elif user_tier == "quantized":
        # Return top prediction with quantized confidence
        top_class = max(raw_probabilities, key=raw_probabilities.get)
        top_prob = raw_probabilities[top_class]
        
        if top_prob > 0.8:
            confidence = "high"
        elif top_prob > 0.5:
            confidence = "medium"
        else:
            confidence = "low"
        
        return {
            "prediction": top_class,
            "confidence": confidence
        }
    
    elif user_tier == "top_3":
        # Return top 3 predictions with probabilities
        sorted_classes = sorted(
            raw_probabilities.items(),
            key=lambda x: x[1],
            reverse=True
        )[:3]
        
        return {
            "predictions": [
                {"class": cls, "probability": prob}
                for cls, prob in sorted_classes
            ]
        }
    
    elif user_tier == "full_distribution":
        # Return complete probability distribution
        return {
            "probabilities": raw_probabilities
        }
    
    else:
        raise ValueError(f"Unknown user tier: {user_tier}")

@app.route("/api/classify")
def classify_endpoint():
    user_id = get_user_id()
    user_tier_name = get_user_tier(user_id)  # e.g., "free", "basic"
    user_tier = USER_TIERS[user_tier_name]
    
    # Get model prediction
    input_data = request.json.get('input')
    raw_probabilities = model.predict(input_data)
    
    # Format based on tier
    response = format_model_output(raw_probabilities, user_tier)
    
    return jsonify(response)
```

**Business Justification Workflow:**

For users requesting higher tiers (full probability distributions), require documented business justification:

```python
def request_tier_upgrade(user_id, requested_tier, justification):
    """
    Process user request for higher output detail tier
    """
    if requested_tier == "full_distribution":
        # Requires manual review
        create_upgrade_ticket({
            "user_id": user_id,
            "requested_tier": requested_tier,
            "justification": justification,
            "status": "pending_review"
        })
        
        # Notify security team for review
        notify_security_team(
            f"User {user_id} requesting full probability access. "
            f"Justification: {justification}"
        )
        
        return {"status": "pending", "message": "Request submitted for review"}
    
    elif requested_tier in ["standard", "basic"]:
        # Auto-approve for paying customers
        if is_paying_customer(user_id):
            upgrade_user_tier(user_id, requested_tier)
            return {"status": "approved"}
        else:
            return {
                "status": "denied",
                "message": "Upgrade to paid plan required"
            }
```

### Probability Rounding and Quantization

**Concept:** Even when providing probabilities, reduce precision to limit information leakage.

**Implementation:**

```python
import numpy as np

def quantize_probabilities(probabilities, bins=5):
    """
    Quantize probability values into discrete bins
    
    Args:
        probabilities: dict of {class: probability}
        bins: number of quantization bins (default: 5)
    
    Returns:
        Quantized probabilities
    """
    quantization_step = 1.0 / bins
    
    quantized = {}
    for cls, prob in probabilities.items():
        # Round to nearest quantization step
        quantized_prob = round(prob / quantization_step) * quantization_step
        quantized[cls] = round(quantized_prob, 2)  # Round to 2 decimal places
    
    # Renormalize to ensure sum = 1.0
    total = sum(quantized.values())
    if total > 0:
        quantized = {cls: p/total for cls, p in quantized.items()}
    
    return quantized

# Example usage
raw_probs = {
    "positive": 0.873,
    "neutral": 0.094,
    "negative": 0.033
}

quantized_probs = quantize_probabilities(raw_probs, bins=5)
# Result: {"positive": 0.8, "neutral": 0.2, "negative": 0.0}
# Information leakage reduced from precise probabilities to 20% intervals
```

**Granularity Levels:**

| Bins | Quantization | Example Use Case |
|------|--------------|------------------|
| 2 | Binary (0 or 1) | Maximum protection, minimal utility |
| 5 | 20% intervals (0.0, 0.2, 0.4, 0.6, 0.8, 1.0) | Good balance |
| 10 | 10% intervals | Moderate protection |
| 100 | 1% intervals | Minimal protection, high utility |

> "One of the most effective countermeasures against distillation attacks is to restrict unwarranted access to the model's soft outputs. Soft outputs, typically probability distributions over classes, are invaluable for distillation. By ensuring that only hard outputs (final class labels) are accessible to external queries and withholding detailed probabilities, the feasibility of performing successful distillation diminishes."
> 
> Source: [[sources/bibliography#Generative AI Security]], p. 170-171

### Role-Based Access to Detailed Outputs

**Concept:** Grant full probability access only for specific authenticated use cases with legitimate business need.

**Implementation:**

```python
from enum import Enum

class OutputDetailLevel(Enum):
    HARD_LABEL = 1          # Class label only
    QUANTIZED = 2            # Confidence buckets
    TOP_K = 3                # Top-k predictions with probabilities
    FULL_DISTRIBUTION = 4    # Complete probability distribution

def get_allowed_detail_level(user_id, use_case):
    """
    Determine allowed output detail level based on user and use case
    
    Args:
        user_id: User identifier
        use_case: Declared use case (e.g., "calibration", "uncertainty_quantification")
    
    Returns:
        OutputDetailLevel enum
    """
    user_role = get_user_role(user_id)
    
    # Trusted internal services get full access
    if user_role == "internal_service":
        return OutputDetailLevel.FULL_DISTRIBUTION
    
    # Research partners with signed agreements
    if user_role == "research_partner" and has_data_use_agreement(user_id):
        return OutputDetailLevel.FULL_DISTRIBUTION
    
    # Legitimate calibration use cases
    if use_case == "calibration" and is_verified_ml_engineer(user_id):
        return OutputDetailLevel.TOP_K
    
    # Default: minimal disclosure
    if is_paying_customer(user_id):
        return OutputDetailLevel.QUANTIZED
    else:
        return OutputDetailLevel.HARD_LABEL

@app.route("/api/classify")
def classify_endpoint():
    user_id = get_user_id()
    use_case = request.headers.get('X-Use-Case', 'general')
    
    allowed_level = get_allowed_detail_level(user_id, use_case)
    
    # Get raw model output
    raw_probabilities = model.predict(request.json['input'])
    
    # Format according to allowed level
    if allowed_level == OutputDetailLevel.HARD_LABEL:
        top_class = max(raw_probabilities, key=raw_probabilities.get)
        response = {"prediction": top_class}
    
    elif allowed_level == OutputDetailLevel.QUANTIZED:
        response = format_quantized_output(raw_probabilities)
    
    elif allowed_level == OutputDetailLevel.TOP_K:
        response = format_top_k_output(raw_probabilities, k=3)
    
    elif allowed_level == OutputDetailLevel.FULL_DISTRIBUTION:
        response = {"probabilities": raw_probabilities}
        
        # Log full distribution access
        log_security_event("full_distribution_access", {
            "user_id": user_id,
            "use_case": use_case,
            "timestamp": datetime.now()
        })
    
    return jsonify(response)
```

### Monitoring Detailed Output Requests

**Track who requests full probability distributions and why:**

```python
import redis
from datetime import datetime, timedelta

redis_client = redis.Redis()

def monitor_detailed_output_access(user_id, detail_level):
    """
    Track and alert on suspicious patterns in detailed output requests
    """
    if detail_level == OutputDetailLevel.FULL_DISTRIBUTION:
        key = f"detailed_access:{user_id}"
        
        # Increment access counter
        redis_client.incr(key)
        redis_client.expire(key, 86400)  # 24-hour window
        
        access_count = int(redis_client.get(key) or 0)
        
        # Alert if excessive detailed access
        if access_count > 1000:  # Threshold
            alert_security_team(
                f"User {user_id} has requested full probability distributions "
                f"{access_count} times in 24 hours. Potential model extraction attempt."
            )
            
            # Optionally downgrade access
            downgrade_user_tier(user_id, "quantized")
            
        # Log for audit trail
        log_audit_event("detailed_output_access", {
            "user_id": user_id,
            "count_24h": access_count,
            "timestamp": datetime.now()
        })
```

## Limitations & Trade-offs

**Limitations:**

- **Reduces model utility:** Many legitimate use cases require calibrated probabilities (uncertainty quantification, ensemble methods, threshold tuning)
- **Not foolproof:** Attackers can still extract functionality using hard labels alone (requires more queries but remains viable)
- **Bypass via averaging:** Attackers may repeatedly query same input and infer probabilities from response consistency patterns
- **Limited effectiveness against boundary probing:** Adaptive query strategies can still extract decision boundaries without detailed probabilities

**Trade-offs:**

- **Security vs. Utility:** Restricting probability access improves security but degrades legitimate use cases requiring confidence scores
- **User Experience vs. Protection:** Free/trial users get degraded experience; may affect product adoption
- **Complexity vs. Maintainability:** Tiered access control adds implementation and operational complexity
- **False Limitations:** May block legitimate ML engineers who need probabilities for model calibration or integration

**Bypass Scenarios:**

- **Hard-label extraction:** Attackers extract model using only class labels (research shows this is viable with sufficient queries)
- **Probability inference:** Repeated queries with slight input variations can infer approximate probabilities
- **Multi-account access:** Attackers obtain premium tier access through multiple paid accounts
- **Transfer learning:** Use limited outputs to fine-tune pre-trained model, reducing query budget needed

## Testing & Validation

**Functional Testing:**

1. **Tier Enforcement:**
   - Verify each user tier receives only allowed output detail level
   - Test that unauthorized access to detailed probabilities is blocked
   - Validate tier upgrade/downgrade workflows

2. **Output Formatting:**
   - Confirm hard labels are correctly extracted
   - Validate quantization buckets are accurate
   - Test probability normalization after quantization

3. **Business Logic:**
   - Verify legitimate use case justifications are approved
   - Test that unjustified requests for detailed access are denied
   - Validate role-based access control rules

**Security Testing:**

1. **Bypass Attempts:**
   - Attempt to infer probabilities through repeated queries
   - Test if API headers can be manipulated to request higher detail levels
   - Try extracting model using only hard labels

2. **Access Control Validation:**
   - Verify users cannot escalate their tier without authorization
   - Test that tier limitations persist across sessions
   - Validate no information leakage through error messages or metadata

**Monitoring & Metrics:**

- **Detailed Access Requests:** Track frequency of full probability distribution requests per user
- **Tier Distribution:** Monitor percentage of users in each tier
- **Upgrade Requests:** Track upgrade request volume and approval rate
- **Extraction Indicators:** Correlate detailed access patterns with model extraction signals

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "One of the most effective countermeasures against distillation attacks is to restrict unwarranted access to the model's soft outputs. Soft outputs, typically probability distributions over classes, are invaluable for distillation. By ensuring that only hard outputs (final class labels) are accessible to external queries and withholding detailed probabilities, the feasibility of performing successful distillation diminishes."
> — [[sources/bibliography#Generative AI Security]], p. 170-171

> "If probabilities are required for certain business logic, quantize them (e.g., "high", "medium", "low" confidence buckets). Implement role-based access: detailed probabilities only for trusted/authenticated use cases. Monitor requests specifically asking for full probability distributions."
> — [[sources/bibliography#Generative AI Security]], p. 171
