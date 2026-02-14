---
title: "Query Complexity Limiting"
tags:
  - type/mitigation
  - target/model-api
  - target/llm
  - target/ml-model
  - source/red-teaming-ai
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Query Complexity Limiting

## Summary

Query complexity limiting restricts computationally expensive or information-revealing queries to prevent attackers from extracting excessive information about model internals or decision boundaries. By imposing constraints on query characteristics such as batch size, input dimensions, or computation time, this mitigation reduces the efficiency of model extraction attacks that rely on systematic exploration of input space or requests for detailed analytical outputs. This control works in tandem with rate limiting by addressing not just query volume but also the information yield per query.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0049 | [[techniques/model-extraction]] | Limits information-rich queries that reveal model decision boundaries or enable efficient parameter inference |
| | [[techniques/model-inversion-attacks]] | Restricts queries specifically designed to reconstruct training data (e.g., gradient-based reconstruction) |
| | [[techniques/api-abuse-and-scraping]] | Prevents resource exhaustion through expensive queries |

## Implementation

### Input Complexity Constraints

**Concept:** Limit the complexity of inputs submitted to the model API.

**Dimensions to Constrain:**

**1. Input Size Limits:**

```python
MAX_INPUT_LENGTH = 2048  # tokens for LLM
MAX_IMAGE_DIMENSIONS = (1024, 1024)  # pixels for vision models
MAX_BATCH_SIZE = 10  # queries per request

@app.route("/api/classify")
def classify_endpoint():
    inputs = request.json.get('inputs', [])
    
    # Enforce batch size limit
    if len(inputs) > MAX_BATCH_SIZE:
        abort(400, f"Batch size exceeds limit of {MAX_BATCH_SIZE}")
    
    # Enforce input length limit (for text)
    for input_text in inputs:
        if len(input_text.split()) > MAX_INPUT_LENGTH:
            abort(400, f"Input exceeds {MAX_INPUT_LENGTH} tokens")
    
    # Process queries
    results = [model.predict(inp) for inp in inputs]
    return jsonify({"results": results})
```

**2. Input Feature Constraints:**

```python
def validate_input_features(input_data):
    """
    Validate input doesn't contain suspicious feature patterns
    designed to probe model boundaries
    """
    # For structured data (e.g., tabular inputs)
    if isinstance(input_data, dict):
        # Limit number of features
        if len(input_data) > MAX_FEATURES:
            return False, "Too many features"
        
        # Reject extreme values (boundary probing)
        for feature, value in input_data.items():
            if abs(value) > FEATURE_VALUE_THRESHOLD:
                return False, f"Feature {feature} out of bounds"
    
    return True, "Valid"

@app.route("/api/predict")
def predict_endpoint():
    input_data = request.json.get('input')
    
    is_valid, message = validate_input_features(input_data)
    if not is_valid:
        abort(400, message)
    
    result = model.predict(input_data)
    return jsonify({"prediction": result})
```

### Computational Complexity Limits

**Concept:** Cap the computational resources any single query can consume.

**Implementation:**

**1. Query Timeout:**

```python
import signal
from contextlib import contextmanager

class TimeoutException(Exception):
    pass

@contextmanager
def timeout(seconds):
    """
    Context manager for timing out long-running operations
    """
    def timeout_handler(signum, frame):
        raise TimeoutException()
    
    # Set the signal handler
    old_handler = signal.signal(signal.SIGALRM, timeout_handler)
    signal.alarm(seconds)
    
    try:
        yield
    finally:
        signal.alarm(0)
        signal.signal(signal.SIGALRM, old_handler)

@app.route("/api/generate")
def generate_endpoint():
    prompt = request.json.get('prompt')
    max_tokens = min(request.json.get('max_tokens', 500), 1000)  # Cap at 1000
    
    try:
        with timeout(30):  # 30-second timeout
            response = llm.generate(prompt, max_tokens=max_tokens)
            return jsonify({"response": response})
    
    except TimeoutException:
        abort(504, "Query exceeded time limit")
```

**2. Resource Usage Monitoring:**

```python
import psutil
import os

def monitor_resource_usage(func):
    """
    Decorator to monitor and limit resource usage per query
    """
    def wrapper(*args, **kwargs):
        process = psutil.Process(os.getpid())
        
        # Measure initial resource usage
        start_memory = process.memory_info().rss / 1024 / 1024  # MB
        start_cpu = process.cpu_percent()
        
        # Execute query
        result = func(*args, **kwargs)
        
        # Measure final resource usage
        end_memory = process.memory_info().rss / 1024 / 1024
        end_cpu = process.cpu_percent()
        
        memory_used = end_memory - start_memory
        cpu_used = end_cpu - start_cpu
        
        # Log high-resource queries
        if memory_used > MEMORY_LIMIT_MB or cpu_used > CPU_LIMIT_PERCENT:
            log_security_event("high_resource_query", {
                "memory_mb": memory_used,
                "cpu_percent": cpu_used,
                "user_id": get_user_id(),
                "endpoint": request.path
            })
            
            # Consider rate limiting user
            if memory_used > MEMORY_LIMIT_MB * 2:
                throttle_user(get_user_id(), duration_seconds=300)
        
        return result
    
    return wrapper

@app.route("/api/expensive_operation")
@monitor_resource_usage
def expensive_endpoint():
    # Process complex query
    pass
```

### Analytical Query Restrictions

**Concept:** Prohibit or restrict queries that request analytical information beyond standard predictions.

**Restricted Query Types:**

**1. Gradient Access:**

Some attacks require gradient information. Explicitly block gradient-related endpoints:

```python
FORBIDDEN_ENDPOINTS = [
    "/gradients",
    "/jacobian",
    "/hessian",
    "/explain",  # If explanation exposes too much
]

@app.before_request
def block_analytical_queries():
    """
    Block requests to analytical endpoints
    """
    if request.path in FORBIDDEN_ENDPOINTS:
        log_security_event("forbidden_endpoint_access", {
            "user_id": get_user_id(),
            "endpoint": request.path,
            "ip": request.remote_addr
        })
        abort(403, "Endpoint not available")
```

**2. Confidence/Uncertainty Queries:**

Limit access to model confidence scores (see [[mitigations/soft-output-limiting]]):

```python
@app.route("/api/classify_with_confidence")
def classify_with_confidence():
    user_tier = get_user_tier(get_user_id())
    
    # Only premium users get confidence scores
    if user_tier != "premium":
        abort(403, "Confidence scores available for premium tier only")
    
    input_data = request.json.get('input')
    probabilities = model.predict_proba(input_data)
    
    return jsonify({"probabilities": probabilities})
```

### Tiered Complexity Limits

**Concept:** Apply different complexity limits based on user trust level.

**Implementation:**

```python
COMPLEXITY_TIERS = {
    "free": {
        "max_batch_size": 1,
        "max_input_length": 512,
        "max_tokens_generated": 100,
        "timeout_seconds": 10
    },
    "basic": {
        "max_batch_size": 5,
        "max_input_length": 1024,
        "max_tokens_generated": 500,
        "timeout_seconds": 20
    },
    "premium": {
        "max_batch_size": 50,
        "max_input_length": 4096,
        "max_tokens_generated": 2000,
        "timeout_seconds": 60
    }
}

def get_complexity_limits(user_id):
    """
    Get complexity limits for user based on tier
    """
    user_tier = get_user_tier(user_id)
    return COMPLEXITY_TIERS.get(user_tier, COMPLEXITY_TIERS["free"])

@app.route("/api/generate")
def generate_endpoint():
    user_id = get_user_id()
    limits = get_complexity_limits(user_id)
    
    prompt = request.json.get('prompt', '')
    max_tokens = request.json.get('max_tokens', 100)
    
    # Enforce limits
    if len(prompt.split()) > limits["max_input_length"]:
        abort(400, f"Input exceeds tier limit of {limits['max_input_length']} tokens")
    
    if max_tokens > limits["max_tokens_generated"]:
        max_tokens = limits["max_tokens_generated"]
    
    try:
        with timeout(limits["timeout_seconds"]):
            response = llm.generate(prompt, max_tokens=max_tokens)
            return jsonify({"response": response})
    except TimeoutException:
        abort(504, "Query exceeded time limit for your tier")
```

### Detect and Throttle Complex Query Patterns

**Concept:** Monitor for users submitting systematically complex queries designed to probe model boundaries.

**Implementation:**

```python
import redis
from collections import defaultdict

redis_client = redis.Redis()

def detect_boundary_probing(user_id, input_data):
    """
    Detect systematic boundary probing through query patterns
    
    Returns: (is_suspicious, risk_score)
    """
    risk_score = 0.0
    
    # Track extreme value usage
    key_extreme = f"extreme_values:{user_id}"
    redis_client.incr(key_extreme)
    redis_client.expire(key_extreme, 3600)  # 1-hour window
    
    extreme_count = int(redis_client.get(key_extreme) or 0)
    if extreme_count > 50:  # 50+ extreme value queries in 1 hour
        risk_score += 0.4
    
    # Track systematic grid-like query patterns
    # (simplified example - production would use more sophisticated analysis)
    if has_grid_pattern(user_id):
        risk_score += 0.3
    
    # Track diverse input exploration (many different feature combinations)
    if has_diverse_exploration_pattern(user_id):
        risk_score += 0.2
    
    is_suspicious = risk_score > 0.5
    
    return is_suspicious, risk_score

@app.route("/api/predict")
def predict_endpoint():
    user_id = get_user_id()
    input_data = request.json.get('input')
    
    # Detect boundary probing
    is_suspicious, risk_score = detect_boundary_probing(user_id, input_data)
    
    if is_suspicious:
        log_security_event("boundary_probing_detected", {
            "user_id": user_id,
            "risk_score": risk_score
        })
        
        # Apply stricter limits for suspicious users
        if risk_score > 0.8:
            abort(429, "Suspicious query pattern detected. Account under review.")
    
    # Process query with potentially tightened limits
    result = model.predict(input_data)
    return jsonify({"prediction": result})
```

### Business Justification for Complex Queries

**Concept:** Require users to justify need for computationally expensive or information-rich queries.

**Implementation:**

```python
def request_complex_query_access(user_id, justification, requested_limits):
    """
    User requests access to more complex queries (higher limits)
    """
    # Create review ticket
    ticket = {
        "user_id": user_id,
        "justification": justification,
        "requested_limits": requested_limits,
        "status": "pending",
        "created_at": datetime.now()
    }
    
    create_review_ticket(ticket)
    
    # Notify security team
    notify_security_team(
        f"User {user_id} requesting complex query access.\n"
        f"Justification: {justification}\n"
        f"Requested limits: {requested_limits}"
    )
    
    return {"status": "pending", "message": "Request submitted for review"}

@app.route("/api/request_higher_limits", methods=["POST"])
def request_higher_limits():
    user_id = get_user_id()
    justification = request.json.get('justification', '')
    requested_limits = request.json.get('limits', {})
    
    if not justification:
        abort(400, "Justification required")
    
    result = request_complex_query_access(user_id, justification, requested_limits)
    return jsonify(result)
```

## Limitations & Trade-offs

**Limitations:**

- **Legitimate use cases impacted:** Power users with genuine need for complex queries may be hindered
- **Workarounds exist:** Attackers can split complex queries into multiple simpler queries
- **Difficult to calibrate:** Setting appropriate limits requires understanding legitimate usage patterns
- **Not foolproof:** Determined attackers can still extract information with many simple queries
- **Operational overhead:** Requires monitoring, tuning, and exception handling for edge cases

**Trade-offs:**

- **Security vs. Utility:** Stricter complexity limits improve security but reduce API utility
- **User Experience vs. Protection:** Limits frustrate users with legitimate complex workflows
- **Simplicity vs. Precision:** Simple fixed limits easy to implement but may be too restrictive; dynamic limits more precise but complex
- **Cost vs. Benefit:** Infrastructure for monitoring query complexity adds operational cost

**Bypass Scenarios:**

- **Query decomposition:** Attackers split complex queries into many simple queries
- **Slow probing:** Stay within complexity limits but query systematically over long period
- **Multi-account distribution:** Use multiple accounts to accumulate complex queries
- **Adversarial input crafting:** Design inputs that appear simple but reveal significant information

## Testing & Validation

**Functional Testing:**

1. **Limit Enforcement:**
   - Submit queries exceeding input size limits (verify rejection)
   - Test batch size limits
   - Validate timeout enforcement for long-running queries

2. **Tiered Access:**
   - Verify free-tier users get lowest complexity limits
   - Confirm premium users can submit complex queries
   - Test tier upgrade/downgrade workflows

3. **Resource Monitoring:**
   - Trigger high-resource queries
   - Verify resource usage tracking and logging
   - Test throttling of resource-intensive users

**Security Testing:**

1. **Boundary Probing Detection:**
   - Submit systematic grid-search queries
   - Test if extreme value patterns are detected
   - Verify boundary probing triggers alerts

2. **Bypass Attempts:**
   - Attempt to decompose complex queries into multiple simple ones
   - Test if multi-account strategies evade limits
   - Try obfuscating complex queries to appear simple

**Monitoring & Metrics:**

- **Query Complexity Distribution:** Track distribution of query sizes, batch sizes, timeouts
- **Limit Violations:** Frequency of rejected queries due to complexity limits
- **Tier Utilization:** Percentage of users at each complexity tier
- **High-Resource Queries:** Track queries consuming excessive CPU/memory
- **Boundary Probing Alerts:** Frequency of detected systematic probing patterns

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Implement query complexity limits: Restrict computationally expensive queries that reveal more information. Require business justification or account verification for high-volume API access. Use API gateways to centralize and enforce anti-extraction policies."
> — [[sources/bibliography#Red-Teaming AI]], p. 190-191

## Related

- **Mitigates:** [[techniques/model-extraction]], [[techniques/model-inversion-attacks]], [[techniques/api-abuse-and-scraping]]
- **Combined With:** [[mitigations/rate-limiting-and-throttling]], [[mitigations/anomaly-detection-architecture]], [[mitigations/soft-output-limiting]]
