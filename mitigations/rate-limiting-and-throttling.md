---
title: "Rate Limiting and Throttling"
tags:
  - type/mitigation
  - target/llm
  - target/model-api
  - target/agent
  - source/developers-playbook-llm
  - source/generative-ai-security
atlas: AML.M0015
owasp: LLM04
maturity: draft
created: 2026-02-12
updated: 2026-02-14
---

# Rate Limiting and Throttling

## Summary

Rate limiting and throttling are defensive controls that restrict the frequency and volume of requests to AI systems, preventing attackers from executing large-scale automated attacks. By capping requests per user, IP address, or session within a time window, rate limiting raises the cost and difficulty of attacks that require numerous queries—including prompt injection experimentation, model extraction, denial-of-service (DoS), and resource exhaustion. While rate limiting alone cannot prevent all attacks, it serves as a critical layer in defense-in-depth strategies by limiting attacker experimentation velocity and creating detection opportunities through anomalous request patterns.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0051 | [[techniques/prompt-injection]] | Limits attacker's ability to rapidly test prompt injection variants |
| AML.T0054 | [[techniques/jailbreak-policy-bypass]] | Restricts attacker's ability to test automated jailbreak suffixes at scale; limits experimentation velocity for GCG-style attacks |
| AML.T0049 | [[techniques/model-extraction]] | Restricts query volume needed for model extraction attacks |
| AML.T0049 | [[techniques/model-extraction-llm]] | Limits systematic API querying needed for LLM knowledge extraction and synthetic dataset generation |
| AML.T0024 | [[techniques/sensitive-info-disclosure]] | Restricts query volume per account/IP to limit attacker's ability to gather statistical samples for membership inference attacks |
| AML.T0020 | [[techniques/rag-data-poisoning]] | Limits frequency of knowledge base updates and bulk data changes to reduce incremental poisoning attack velocity |
| | [[techniques/api-rate-limiting-bypass]] | Multi-factor rate limiting (per API key, user, IP, behavioral fingerprint) prevents attackers from multiplying quota through multi-account campaigns; sliding window algorithms prevent burst exploitation after rate limit resets |
| | [[techniques/api-authentication-bypass]] | Limits brute-force authentication attempts |
| | [[techniques/insecure-model-gateway-config]] | Reduces exposure from misconfigured rate limits |
| | [[techniques/membership-inference-attacks]] | Limits query volume for privacy attacks |
| | [[techniques/privacy-attacks-beyond-membership-inference]] | Restricts systematic probing for privacy exploitation |
| AML.T0056 | [[techniques/training-data-memorization]] | Limits query volume to restrict automated extraction campaigns and privacy attack sampling at scale |
| | [[techniques/unauthorized-knowledge-disclosure]] | Limits attacker's ability to systematically extract large volumes of restricted knowledge through high-frequency querying |
| | [[techniques/vector-embedding-weaknesses]] | Limits attacker's ability to test embedding inversion queries at scale; restricts embedding space exploration velocity |
| | [[techniques/insufficient-output-encoding]] | Limits attacker's ability to rapidly test injection payloads via LLM-mediated injection attacks |

## Implementation

### Core Limiting Strategies

Rate limiting for LLM applications can be implemented using multiple strategies, each with different strengths and bypass potential:

#### 1. IP-Based Rate Limiting

**Description:** Caps requests per IP address within a time window (e.g., 1000 requests per hour per IP)

**Implementation:**
```python
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

app = Flask(__name__)
limiter = Limiter(
    app,
    key_func=get_remote_address,
    default_limits=["1000 per hour", "100 per minute"]
)

@app.route("/api/query")
@limiter.limit("10 per minute")
def query_endpoint():
    # LLM query logic
    pass
```

**Strengths:**
- Simple to implement
- No authentication required
- Effective against casual attackers

**Limitations:**
- **Bypassed by IP rotation:** Attackers use VPNs, proxies, or botnets to cycle through IPs
- **Shared IP issues:** Legitimate users behind NAT or corporate proxies share IPs, causing false positives
- **Cloud environment challenges:** Requests from cloud services may appear from a small IP pool

**Verdict:** Necessary baseline defense, but insufficient alone against sophisticated attackers.

> Source: [[sources/developers-playbook-llm]], p. 35

#### 2. User-Based Rate Limiting

**Description:** Ties request limits to verified user credentials (API keys, accounts, session tokens)

**Implementation:**
```python
from functools import wraps
from flask import request, abort

user_request_counts = {}  # In production: use Redis or similar

def user_rate_limit(max_requests=100, window_seconds=3600):
    def decorator(f):
        @wraps(f)
        def wrapped(*args, **kwargs):
            user_id = request.headers.get('X-API-Key')
            if not user_id:
                abort(401, "API key required")
            
            # Check rate limit
            count, timestamp = user_request_counts.get(user_id, (0, time.time()))
            if time.time() - timestamp > window_seconds:
                count, timestamp = 0, time.time()
            
            if count >= max_requests:
                abort(429, "Rate limit exceeded")
            
            user_request_counts[user_id] = (count + 1, timestamp)
            return f(*args, **kwargs)
        return wrapped
    return decorator

@app.route("/api/query")
@user_rate_limit(max_requests=1000, window_seconds=3600)
def query_endpoint():
    # LLM query logic
    pass
```

**Strengths:**
- More reliable than IP-based limiting
- Enables per-user accountability
- Attackers cannot bypass by simply changing IPs

**Limitations:**
- **Multi-account attacks:** Attackers create multiple accounts to multiply their quota
- **Requires authentication:** Not suitable for public/anonymous endpoints

**Verdict:** Effective when combined with account verification and monitoring for multi-account abuse.

> Source: [[sources/developers-playbook-llm]], p. 35

#### 3. Session-Based Rate Limiting

**Description:** Limits requests per user session (suitable for web applications with session management)

**Implementation:**
```python
from flask import session

@app.route("/api/query")
def query_endpoint():
    session_id = session.get('id')
    session_count = session.get('request_count', 0)
    
    if session_count >= 50:  # 50 requests per session
        abort(429, "Session rate limit exceeded")
    
    session['request_count'] = session_count + 1
    # LLM query logic
    pass
```

**Strengths:**
- Works well for interactive web applications
- Limits abuse within a single user session
- Harder to bypass than IP-based alone

**Limitations:**
- Attackers can create new sessions to reset limits
- Less suitable for API-based integrations

**Verdict:** Suitable for web apps, but must be combined with other strategies for APIs.

> Source: [[sources/developers-playbook-llm]], p. 35

### Advanced Rate Limiting Techniques

#### Adaptive Rate Limiting

**Description:** Dynamically adjusts rate limits based on user behavior and risk signals

**Implementation Patterns:**
- **Tiered limits:** Free users get 100 req/hour, paid users get 10,000 req/hour
- **Reputation-based:** Users with good history get higher limits; flagged users get lower limits
- **Behavioral triggers:** Automatically tighten limits when suspicious patterns detected
- **Progressive throttling:** Gradually slow down responses for users approaching limits (soft degradation)

**Example:**
```python
def get_user_limit(user_id):
    user_reputation = get_reputation_score(user_id)
    if user_reputation > 0.9:
        return 10000  # High reputation: generous limit
    elif user_reputation > 0.5:
        return 1000   # Medium reputation: standard limit
    else:
        return 100    # Low/flagged reputation: restrictive limit

@app.route("/api/query")
def query_endpoint():
    user_id = request.headers.get('X-API-Key')
    limit = get_user_limit(user_id)
    # Apply dynamic limit
    pass
```

**Benefits:**
- Balances security with user experience
- Attackers face tighter limits without affecting legitimate power users
- Reduces false positives (blocking legitimate users)

> Source: [[sources/developers-playbook-llm]], p. 35

#### Distributed Rate Limiting

**Description:** Coordinate rate limiting across multiple API gateways or instances using shared state

**Implementation:**
- **Redis-backed counters:** Use Redis to store request counts across all API servers
- **Token bucket algorithm:** Consume tokens from a shared bucket for each request
- **Leaky bucket algorithm:** Smooth out request bursts over time

**Example (Redis-based):**
```python
import redis
from flask import request, abort

redis_client = redis.Redis(host='localhost', port=6379, db=0)

@app.route("/api/query")
def query_endpoint():
    user_id = request.headers.get('X-API-Key')
    key = f"rate_limit:{user_id}"
    
    current_count = redis_client.incr(key)
    if current_count == 1:
        redis_client.expire(key, 3600)  # 1-hour window
    
    if current_count > 1000:
        abort(429, "Rate limit exceeded")
    
    # LLM query logic
    pass
```

**Benefits:**
- Consistent limits across distributed deployments
- Prevents attackers from bypassing limits by targeting different API servers

### Configuration Considerations

**Limit Tuning:**
- **Too restrictive:** Hinders legitimate use, degrades user experience
- **Too permissive:** Fails to prevent attacks, allows abuse at scale

**Best Practices:**
- **Profile legitimate usage:** Analyze typical request patterns before setting limits
- **Set tiered limits:** Different limits for free/trial vs. paid users
- **Use multiple time windows:** E.g., 10 per minute AND 1000 per hour (prevents both burst and sustained abuse)
- **Graceful degradation:** Warn users approaching limits before hard cutoff

**Error Messaging:**
- Return **HTTP 429 (Too Many Requests)** with `Retry-After` header
- Provide clear messaging: "Rate limit exceeded. Retry in 15 minutes."
- Avoid revealing exact limits to attackers (prevents precise limit probing)

### Defending Against API Rate Limiting Bypass

**Multi-Factor Rate Limiting:**

To defend against distributed bypass campaigns that use multiple accounts and IP addresses, implement rate limiting across **multiple dimensions simultaneously**:

```python
import redis
from flask import request, abort

redis_client = redis.Redis()

def check_multi_factor_rate_limit(user_id, ip_address, api_key, complexity_score=1.0):
    """
    Enforce rate limits across multiple dimensions simultaneously
    
    Args:
        user_id: User account identifier
        ip_address: Source IP address
        api_key: API key used for request
        complexity_score: Computational cost weighting (expensive queries count more)
    
    Returns:
        bool: True if request allowed, False if rate limit exceeded
    """
    current_time = time.time()
    window_seconds = 3600  # 1 hour window
    
    # Factor 1: Per API key limit
    api_key_count = redis_client.incr(f"rate_limit:api_key:{api_key}")
    if api_key_count == 1:
        redis_client.expire(f"rate_limit:api_key:{api_key}", window_seconds)
    if api_key_count > 1000:
        abort(429, "API key rate limit exceeded")
    
    # Factor 2: Per user account limit (even across multiple API keys)
    user_count = redis_client.incr(f"rate_limit:user:{user_id}")
    if user_count == 1:
        redis_client.expire(f"rate_limit:user:{user_id}", window_seconds)
    if user_count > 1500:  # Slightly higher than per-key to allow multi-key usage
        abort(429, "User account rate limit exceeded")
    
    # Factor 3: Per IP address limit
    ip_count = redis_client.incr(f"rate_limit:ip:{ip_address}")
    if ip_count == 1:
        redis_client.expire(f"rate_limit:ip:{ip_address}", window_seconds)
    if ip_count > 2000:  # Account for shared IPs (NAT, corporate proxies)
        abort(429, "IP address rate limit exceeded")
    
    # Factor 4: Per CIDR range (detect distributed attacks from related IPs)
    ip_prefix = '.'.join(ip_address.split('.')[:3])  # /24 CIDR
    cidr_count = redis_client.incr(f"rate_limit:cidr:{ip_prefix}")
    if cidr_count == 1:
        redis_client.expire(f"rate_limit:cidr:{ip_prefix}", window_seconds)
    if cidr_count > 10000:  # Higher threshold for CIDR ranges
        abort(429, "Network range rate limit exceeded")
    
    # Factor 5: Request complexity weighting
    # Expensive queries (e.g., long context, complex prompts) count more against quota
    weighted_count = redis_client.incrbyfloat(
        f"rate_limit:complexity:{user_id}",
        complexity_score
    )
    if weighted_count > 5000:  # Complexity-weighted limit
        abort(429, "Computational quota exceeded")
    
    # Factor 6: Behavioral fingerprint (device/browser characteristics)
    fingerprint = generate_device_fingerprint(request)
    fingerprint_count = redis_client.incr(f"rate_limit:fingerprint:{fingerprint}")
    if fingerprint_count == 1:
        redis_client.expire(f"rate_limit:fingerprint:{fingerprint}", window_seconds)
    if fingerprint_count > 1200:
        abort(429, "Device fingerprint rate limit exceeded")
    
    return True

def generate_device_fingerprint(request):
    """
    Generate fingerprint from browser/device characteristics
    """
    import hashlib
    
    fingerprint_data = f"{request.headers.get('User-Agent')}|" \
                      f"{request.headers.get('Accept-Language')}|" \
                      f"{request.headers.get('Accept-Encoding')}"
    
    return hashlib.sha256(fingerprint_data.encode()).hexdigest()[:16]

@app.route("/api/query")
def query_endpoint():
    user_id = get_user_id()
    ip_address = request.remote_addr
    api_key = request.headers.get('X-API-Key')
    
    # Calculate complexity score based on request
    complexity_score = calculate_query_complexity(request.json.get('prompt'))
    
    check_multi_factor_rate_limit(user_id, ip_address, api_key, complexity_score)
    
    # Process request
    pass
```

**Sliding Window Rate Limiting:**

Prevent exploitation of predictable reset times (e.g., midnight UTC bursts) by using sliding window algorithms instead of fixed time windows:

```python
import time
import redis

redis_client = redis.Redis()

def sliding_window_rate_limit(user_id, max_requests=1000, window_seconds=3600):
    """
    Implement sliding window rate limiting to prevent reset-time burst exploitation
    
    Args:
        user_id: User identifier
        max_requests: Maximum requests allowed in window
        window_seconds: Time window in seconds
    
    Returns:
        bool: True if request allowed, False if rate limit exceeded
    """
    current_time = time.time()
    window_start = current_time - window_seconds
    
    # Use sorted set with scores as timestamps
    key = f"rate_limit:sliding:{user_id}"
    
    # Remove old entries outside the window
    redis_client.zremrangebyscore(key, 0, window_start)
    
    # Count requests in current window
    request_count = redis_client.zcard(key)
    
    if request_count >= max_requests:
        return False  # Rate limit exceeded
    
    # Add current request
    redis_client.zadd(key, {str(current_time): current_time})
    redis_client.expire(key, window_seconds)  # Auto-cleanup
    
    return True

@app.route("/api/query")
def query_endpoint():
    user_id = get_user_id()
    
    if not sliding_window_rate_limit(user_id):
        abort(429, "Rate limit exceeded. Using sliding window algorithm.")
    
    # Process request
    pass
```

**Account Creation Hardening:**

Make account farming economically infeasible by increasing friction for bulk account creation:

```python
from flask import request, abort
import requests

def harden_account_creation(email, phone, ip_address):
    """
    Apply multiple verification layers to prevent account farming
    
    Args:
        email: Email address for new account
        phone: Phone number for verification
        ip_address: Source IP of signup request
    
    Raises:
        ValueError: If verification fails
    """
    # 1. Block disposable email domains
    disposable_domains = [
        'tempmail.com', 'guerrillamail.com', '10minutemail.com',
        'mailinator.com', 'throwaway.email'
    ]
    email_domain = email.split('@')[1]
    if email_domain in disposable_domains:
        raise ValueError("Disposable email addresses not allowed")
    
    # 2. Require email verification with time-limited token
    send_email_verification(email, expiry_minutes=15)
    
    # 3. Require phone number verification
    if not phone:
        raise ValueError("Phone number required for API access")
    send_sms_verification(phone)
    
    # 4. Implement effective CAPTCHA
    # Use reCAPTCHA v3 or hCaptcha with high threshold
    captcha_score = verify_recaptcha_v3(request.form.get('captcha_token'))
    if captcha_score < 0.7:  # High threshold to block bots
        raise ValueError("CAPTCHA verification failed")
    
    # 5. Signup velocity limits (max N accounts per IP per day)
    signup_count = redis_client.incr(f"signup_velocity:{ip_address}")
    if signup_count == 1:
        redis_client.expire(f"signup_velocity:{ip_address}", 86400)  # 24 hours
    if signup_count > 5:  # Max 5 accounts per IP per day
        abort(429, "Signup velocity limit exceeded. Try again tomorrow.")
    
    # 6. For paid tiers: Require payment method on file (even for free usage)
    # This doesn't charge but validates card authenticity
    require_payment_method_validation()

def verify_recaptcha_v3(token):
    """Verify reCAPTCHA v3 token and return score"""
    response = requests.post('https://www.google.com/recaptcha/api/siteverify', data={
        'secret': RECAPTCHA_SECRET_KEY,
        'response': token
    })
    result = response.json()
    return result.get('score', 0.0)
```

**Economic Disincentives:**

Reduce free-tier generosity to make large-scale abuse economically unfeasible:

| Tier | Rate Limit | Account Creation | Cost to Attacker (500k queries/day) |
|------|-----------|------------------|-------------------------------------|
| **Free (Unverified)** | 100/day | No verification | 5,000 accounts (easily farmed) |
| **Free (Email Verified)** | 500/day | Email verification | 1,000 accounts (moderate friction) |
| **Free (Phone Verified)** | 1,000/day | Email + Phone | 500 accounts (higher friction) |
| **Paid Tier** | 10,000/day | Payment on file | 50 accounts + payment method (expensive) |

**Implementation:**

```python
def get_tier_rate_limit(user):
    """Return rate limit based on verification tier"""
    if user.payment_method_verified:
        return 10000  # Paid tier
    elif user.phone_verified:
        return 1000   # Phone-verified free tier
    elif user.email_verified:
        return 500    # Email-verified free tier
    else:
        return 100    # Unverified free tier
```

> Source: [[sources/bibliography#Generative AI Security]], techniques/api-rate-limiting-bypass.md (extracted mitigation content)

### Policy-Violating Query Rate Limiting

**Concept:** Apply stricter rate limits to users who repeatedly trigger safety filters or generate policy-violating outputs, even if the violations are bypassed through jailbreaks.

**Implementation:**

Track policy violations per user and dynamically tighten limits for repeat offenders:

```python
import redis
from datetime import datetime, timedelta

redis_client = redis.Redis()

def track_policy_violation(user_id, violation_type):
    """
    Track policy violations and adjust rate limits accordingly
    """
    key = f"policy_violations:{user_id}"
    
    # Increment violation count
    redis_client.hincrby(key, violation_type, 1)
    redis_client.expire(key, 86400)  # 24-hour window
    
    # Get total violations
    violations = redis_client.hgetall(key)
    total_violations = sum(int(v) for v in violations.values())
    
    return total_violations

def get_dynamic_rate_limit(user_id, base_limit=1000):
    """
    Calculate dynamic rate limit based on policy violation history
    """
    key = f"policy_violations:{user_id}"
    violations = redis_client.hgetall(key)
    total_violations = sum(int(v) for v in violations.values()) if violations else 0
    
    if total_violations == 0:
        return base_limit  # Normal limit
    elif total_violations < 5:
        return base_limit // 2  # 50% reduction
    elif total_violations < 10:
        return base_limit // 5  # 80% reduction
    else:
        return base_limit // 10  # 90% reduction (heavy penalty)

@app.route("/api/query")
def query_endpoint():
    user_id = get_user_id()
    
    # Apply dynamic rate limit based on violation history
    current_limit = get_dynamic_rate_limit(user_id)
    
    # Check if user exceeded their limit
    request_count = redis_client.incr(f"rate_limit:{user_id}")
    if request_count == 1:
        redis_client.expire(f"rate_limit:{user_id}", 3600)
    
    if request_count > current_limit:
        abort(429, f"Rate limit exceeded. Your limit: {current_limit}/hour due to policy violations.")
    
    # Process request
    response = generate_llm_response(request.json.get('message'))
    
    # Check if response violates policy
    is_violation, violation_type = check_policy_compliance(response)
    
    if is_violation:
        # Track violation even if we deliver the response
        total_violations = track_policy_violation(user_id, violation_type)
        
        log_security_event("policy_violation", {
            "user_id": user_id,
            "violation_type": violation_type,
            "total_violations": total_violations
        })
        
        # Optionally block the response
        if total_violations > 10:
            abort(400, "Policy violation limit exceeded. Account flagged for review.")
    
    return jsonify({"response": response})
```

**Escalation Tiers:**

| Violation Count (24h) | Rate Limit Multiplier | Action |
|-----------------------|------------------------|--------|
| 0 | 1x (normal) | No action |
| 1-4 | 0.5x (50% reduction) | Warning logged |
| 5-9 | 0.2x (80% reduction) | Account flagged |
| 10-19 | 0.1x (90% reduction) | Account review triggered |
| 20+ | 0.01x (near-zero) | Account suspended pending investigation |

**Use Cases:**

1. **Automated jailbreak campaigns:** User testing thousands of jailbreak variants gets rate-limited after first few policy violations
2. **Multi-account abuse:** Accounts exhibiting coordinated policy violations get collectively rate-limited
3. **Persistent attackers:** Users who systematically bypass safety controls face progressively stricter limits

**Integration with [[mitigations/anomaly-detection-architecture]]:**

Combine policy violation tracking with anomaly detection:

```python
def check_and_limit(user_id, user_input):
    """Combine anomaly detection with dynamic rate limiting"""
    
    # Anomaly detection
    is_anomalous, signals, risk_score = detect_jailbreak_query_anomalies(user_input, user_id)
    
    if is_anomalous and risk_score > 0.8:
        # Treat high-risk anomaly as policy violation
        track_policy_violation(user_id, "jailbreak_attempt")
    
    # Apply dynamic rate limit
    current_limit = get_dynamic_rate_limit(user_id)
    if exceeds_limit(user_id, current_limit):
        abort(429, "Rate limit exceeded")
    
    # Continue processing
    pass
```

**Monitoring:**

- **Violation distribution:** Track which users have highest violation counts
- **Repeat offender rate:** Percentage of users with 10+ violations
- **Rate limit escalation:** Track how often limits are tightened due to violations
- **False positive review:** Sample policy violations to identify legitimate use cases incorrectly flagged

> Source: [[sources/bibliography#Generative AI Security]], p. 169

### Responsive Controls for Rate Limit Bypass

When rate limit bypass is detected, automated and manual response procedures should activate:

**Automated Account Suspension:**

```python
def suspend_abusive_account(user_id, reason, evidence):
    """
    Suspend account exhibiting bypass behavior
    
    Args:
        user_id: User account to suspend
        reason: Suspension reason (coordinated_patterns, automated_usage, etc.)
        evidence: Dictionary of evidence data
    """
    # Mark account as suspended
    db.update_user(user_id, status='suspended', suspension_reason=reason)
    
    # Revoke all API keys
    api_keys = db.get_user_api_keys(user_id)
    for api_key in api_keys:
        revoke_api_key(api_key)
    
    # Log suspension event
    log_security_event("account_suspended", {
        "user_id": user_id,
        "reason": reason,
        "evidence": evidence
    })
    
    # Notify user
    send_suspension_notification(user_id, reason)

# Example: Automated suspension trigger
def check_coordinated_campaign(user_id):
    """Check if user is part of coordinated bypass campaign"""
    # Get behavioral fingerprint
    similar_users = find_users_with_similar_behavior(user_id)
    
    if len(similar_users) >= 10:  # 10+ accounts with identical patterns
        evidence = {
            "similar_accounts": similar_users,
            "pattern_type": "coordinated_queries",
            "detection_time": datetime.now().isoformat()
        }
        
        # Suspend all related accounts
        for related_user in similar_users:
            suspend_abusive_account(
                related_user,
                "coordinated_bypass_campaign",
                evidence
            )
```

**Dynamic Rate Limit Adjustment:**

Temporarily reduce rate limits for suspicious accounts or increase verification requirements:

```python
def apply_dynamic_rate_reduction(user_id, reduction_factor=0.5, duration_hours=24):
    """
    Temporarily reduce rate limits for suspicious account
    
    Args:
        user_id: User account to throttle
        reduction_factor: Multiplier for rate limit (0.5 = 50% reduction)
        duration_hours: How long reduction lasts
    """
    redis_client.setex(
        f"rate_reduction:{user_id}",
        duration_hours * 3600,
        reduction_factor
    )
    
    log_security_event("dynamic_rate_reduction", {
        "user_id": user_id,
        "reduction_factor": reduction_factor,
        "duration_hours": duration_hours
    })

def get_adjusted_rate_limit(user_id, base_limit):
    """Get rate limit adjusted for suspicious behavior"""
    reduction = redis_client.get(f"rate_reduction:{user_id}")
    if reduction:
        return int(base_limit * float(reduction))
    return base_limit
```

**Progressive Challenge:**

Require additional verification when suspicious patterns detected:

```python
def require_progressive_challenge(user_id, challenge_type):
    """
    Require additional verification for suspicious users
    
    Challenge types:
    - captcha: Require CAPTCHA on next request
    - email_reverification: Require email re-verification
    - phone_verification: Require phone number verification
    - payment_method: Require payment method on file
    """
    redis_client.set(f"challenge_required:{user_id}", challenge_type)
    
@app.route("/api/query")
def query_endpoint():
    user_id = get_user_id()
    
    # Check if challenge required
    challenge = redis_client.get(f"challenge_required:{user_id}")
    if challenge:
        if challenge == 'captcha' and not request.headers.get('X-Captcha-Token'):
            abort(403, "CAPTCHA verification required")
        elif challenge == 'email_reverification' and not user_is_reverified(user_id):
            abort(403, "Email re-verification required")
        # ... handle other challenge types
    
    # Process request
    pass
```

**IP Blocklisting:**

Block IP ranges exhibiting distributed attack patterns:

```python
def blocklist_ip_range(ip_prefix, duration_hours=24, reason=""):
    """
    Block IP range (CIDR) for specified duration
    
    Args:
        ip_prefix: IP prefix (e.g., "203.0.113" for /24 range)
        duration_hours: Blocklist duration
        reason: Human-readable reason for block
    """
    redis_client.setex(
        f"ip_blocklist:{ip_prefix}",
        duration_hours * 3600,
        reason
    )
    
    log_security_event("ip_range_blocked", {
        "ip_prefix": ip_prefix,
        "duration_hours": duration_hours,
        "reason": reason
    })

@app.before_request
def check_ip_blocklist():
    """Check if source IP is blocklisted"""
    ip = request.remote_addr
    ip_prefix = '.'.join(ip.split('.')[:3])  # /24 CIDR
    
    if redis_client.exists(f"ip_blocklist:{ip_prefix}"):
        reason = redis_client.get(f"ip_blocklist:{ip_prefix}")
        abort(403, f"IP range blocked: {reason}")
```

**Emergency Throttling:**

Global rate limit reduction during active attacks:

```python
def enable_emergency_throttling(global_reduction_factor=0.1):
    """
    Enable global rate limit reduction during active attack
    
    Args:
        global_reduction_factor: Multiplier for all rate limits (0.1 = 90% reduction)
    """
    redis_client.set("emergency_throttling", global_reduction_factor)
    
    alert_security_team("Emergency throttling enabled", {
        "reduction_factor": global_reduction_factor,
        "timestamp": datetime.now().isoformat()
    })

def get_effective_rate_limit(user_id, base_limit):
    """Apply emergency throttling if active"""
    emergency_factor = redis_client.get("emergency_throttling")
    if emergency_factor:
        base_limit = int(base_limit * float(emergency_factor))
    
    # Also apply user-specific reductions
    return get_adjusted_rate_limit(user_id, base_limit)
```

**Coordinated Account Termination:**

When distributed campaign detected, terminate all related accounts simultaneously:

```python
def terminate_coordinated_campaign(user_ids, campaign_evidence):
    """
    Terminate all accounts in coordinated bypass campaign
    
    Args:
        user_ids: List of user IDs in campaign
        campaign_evidence: Evidence dictionary
    """
    log_security_event("coordinated_campaign_terminated", {
        "account_count": len(user_ids),
        "evidence": campaign_evidence
    })
    
    for user_id in user_ids:
        suspend_abusive_account(
            user_id,
            "coordinated_bypass_campaign",
            campaign_evidence
        )
    
    # Block related IP ranges
    ip_prefixes = set()
    for user_id in user_ids:
        user_ips = get_user_recent_ips(user_id)
        for ip in user_ips:
            ip_prefix = '.'.join(ip.split('.')[:3])
            ip_prefixes.add(ip_prefix)
    
    for ip_prefix in ip_prefixes:
        blocklist_ip_range(
            ip_prefix,
            duration_hours=72,
            reason="Coordinated bypass campaign"
        )
```

> Source: [[sources/bibliography#Generative AI Security]], techniques/api-rate-limiting-bypass.md (extracted responsive controls)

## Limitations & Trade-offs

**Limitations:**

- **Multi-account bypass:** Determined attackers create multiple accounts or use distributed infrastructure
- **Shared IP false positives:** Legitimate users behind NAT/corporate proxies may be unfairly limited
- **Usability impact:** Overly strict limits frustrate power users and degrade experience
- **Insufficient alone:** Rate limiting slows attacks but doesn't prevent them (defense-in-depth required)

**Trade-offs:**

- **Security vs. Usability:** Stricter limits improve security but may block legitimate users
- **Simplicity vs. Sophistication:** Simple IP-based limiting is easy to deploy but easy to bypass; sophisticated adaptive limiting requires more infrastructure
- **Cost vs. Benefit:** Distributed rate limiting (Redis, API gateways) adds infrastructure complexity/cost

**Bypass Scenarios:**

- **IP rotation:** Attackers use VPNs, proxies, or botnets to cycle through IPs
- **Multi-account attacks:** Attackers create hundreds of accounts to multiply quota
- **Slow-and-low attacks:** Attackers stay just below rate limit thresholds over extended periods
- **Distributed campaigns:** Coordinate attacks across many users/devices to evade per-user limits

## Testing & Validation

**Functional Testing:**

1. **Limit Enforcement:** Verify limits are correctly enforced (submit requests beyond limit, confirm rejection)
2. **Multi-window Testing:** Test both short-term (per-minute) and long-term (per-hour) limits
3. **Reset Validation:** Confirm limits reset after time window expires
4. **Error Handling:** Validate HTTP 429 responses and `Retry-After` headers

**Security Testing:**

1. **Bypass Attempts:** Test IP rotation, multi-account abuse, session manipulation
2. **Burst Testing:** Rapid-fire requests to test short-term limits
3. **Sustained Testing:** Long-duration request streams to test sustained limits
4. **Distributed Testing:** Simulate distributed attacks from multiple IPs/accounts

**Monitoring & Metrics:**

- **Rate Limit Hit Rate:** Percentage of requests that hit rate limits (indicates if limits are too tight or attacks are occurring)
- **Per-User Request Distribution:** Identify users consistently hitting limits (potential abuse or legitimate power users needing higher quotas)
- **429 Response Rate:** Track rate limit rejections over time
- **Bypass Indicators:** Detect patterns like rapid account creation or IP cycling correlated with limit hits

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Restricts frequency of requests to limit attacker's ability to brute-force injection variants. IP-based: caps requests per IP (bypassed by IP rotation). User-based: ties limit to verified credentials (requires auth). Session-based: limits per user session (suitable for web apps)."
> — [[sources/developers-playbook-llm]], p. 35

> "Rate limiting on policy-violating queries: Track users who frequently trigger safety filters (even if bypassed through jailbreaks). Apply progressively stricter rate limits to repeat offenders. Particularly effective against automated jailbreak campaigns where attackers test thousands of suffix variants."
> — [[sources/bibliography#Generative AI Security]], p. 169

## Related

- **Mitigates**: [[techniques/prompt-injection]], [[techniques/model-extraction]], [[techniques/api-rate-limiting-bypass]], [[techniques/api-authentication-bypass]], [[techniques/membership-inference-attacks]]
- **Combined with**: [[mitigations/input-validation-patterns]], [[mitigations/anomaly-detection-architecture]], [[mitigations/llm-monitoring]]
