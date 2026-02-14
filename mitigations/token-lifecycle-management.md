---
title: "Token Lifecycle Management"
tags:
  - type/mitigation
  - target/model-api
  - target/llm-app
  - source/generative-ai-security
owasp: LLM07
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Token Lifecycle Management

## Summary

Token lifecycle management implements responsive controls for authentication tokens throughout their entire lifecycle—from issuance through expiration, rotation, revocation, and emergency invalidation. This mitigation enables rapid response to authentication attacks by providing mechanisms to immediately revoke compromised tokens, enforce automatic rotation to limit token reuse, implement account lockout for suspicious activity, globally invalidate tokens during security incidents, and rotate signing keys when vulnerabilities are discovered. Effective token lifecycle management limits the window of exposure when credentials are compromised and provides essential response capabilities for authentication bypass incidents.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/api-authentication-bypass]] | Limits impact of stolen or forged tokens through rapid revocation and rotation; enables emergency response to authentication attacks |

## Implementation

### Automated Token Revocation

**Revocation Triggers:**
- Anomaly detection signals (expired token reuse, signature bypass attempts, geolocation anomalies)
- Security events (suspicious authentication patterns, failed MFA verification)
- Manual user-initiated logout or password change
- Admin-initiated revocation during security investigations

**Revocation Implementation:**

**Token Revocation Registry:**
```python
# Centralized token revocation (Redis-based)
import redis
from datetime import timedelta

redis_client = redis.Redis(host='localhost', port=6379, db=0)

def revoke_token(token_id, reason, ttl_seconds=3600):
    """Add token to revocation list with TTL matching original expiration"""
    redis_client.setex(
        name=f"revoked:token:{token_id}",
        time=ttl_seconds,
        value=reason
    )
    log_security_event("token_revoked", token_id=token_id, reason=reason)

def is_token_revoked(token_id):
    """Check if token is in revocation list"""
    return redis_client.exists(f"revoked:token:{token_id}")

# Middleware to check revocation before processing request
def check_token_revocation(token):
    payload = validate_jwt(token, public_key)
    if payload and is_token_revoked(payload.get("jti")):
        return {"error": "Token has been revoked"}, 401
    return payload
```

**Automated Revocation on Anomaly:**
```python
def on_authentication_anomaly(event):
    """Automatically revoke tokens when anomalies detected"""
    if event.severity in ["HIGH", "CRITICAL"]:
        # Revoke current session tokens
        revoke_token(event.token_id, reason=event.anomaly_type)
        
        # Revoke all active sessions for user if critical
        if event.severity == "CRITICAL":
            user_sessions = get_active_sessions(event.user_id)
            for session in user_sessions:
                revoke_token(session.token_id, reason="critical_anomaly_detected")
        
        # Notify user of revocation
        notify_user(event.user_id, "Your session has been terminated due to suspicious activity")
```

**Revocation Propagation:**
- Maintain distributed revocation cache across all API gateway nodes
- Implement cache synchronization with low latency (<1 second)
- Use pub/sub messaging for real-time revocation distribution (Redis Pub/Sub, Kafka)
- Handle network partitions gracefully (fail-closed: reject tokens when revocation status unknown)

### Secure Token Rotation

**Refresh Token Rotation:**
```python
def rotate_refresh_token(old_refresh_token):
    """Rotate refresh token on each use (prevents replay)"""
    # Validate old refresh token
    payload = validate_refresh_token(old_refresh_token)
    if not payload:
        return {"error": "Invalid refresh token"}, 401
    
    # Check if token already rotated (detects replay attack)
    if is_token_revoked(payload["jti"]):
        # Token already used - potential theft
        alert("Refresh token reuse detected", 
              user=payload["sub"], 
              token_id=payload["jti"])
        # Revoke ALL user sessions
        revoke_all_user_sessions(payload["sub"])
        return {"error": "Token reuse detected - all sessions revoked"}, 401
    
    # Revoke old refresh token
    revoke_token(payload["jti"], reason="rotated")
    
    # Issue new refresh token
    new_refresh_token = issue_refresh_token(
        user_id=payload["sub"],
        scope=payload.get("scope")
    )
    
    # Issue new access token
    new_access_token = issue_access_token(
        user_id=payload["sub"],
        scope=payload.get("scope")
    )
    
    log_security_event("token_rotation", 
                      user=payload["sub"],
                      old_token=payload["jti"],
                      new_token=get_token_id(new_refresh_token))
    
    return {
        "access_token": new_access_token,
        "refresh_token": new_refresh_token,
        "token_type": "Bearer",
        "expires_in": 1800  # 30 minutes
    }
```

**Rotation Best Practices:**
- Rotate refresh tokens on every use (not just expiration)
- Maintain rotation history to detect reuse (store previous token IDs temporarily)
- Set strict TTL on rotation history (no longer than original token expiration)
- Alert on rotation anomalies (rapid rotation, rotation from multiple IPs)

### Account Lockout

**Lockout Triggers:**
- Repeated failed authentication attempts (threshold: 5 failures in 5 minutes)
- Multiple concurrent failed MFA verifications
- Detection of credential stuffing or brute-force patterns
- Signature bypass attempts or token forgery detection
- Manual admin-initiated lockout during investigations

**Lockout Implementation:**
```python
def check_and_enforce_lockout(user_id, failed_attempts):
    """Enforce account lockout on repeated failures"""
    lockout_threshold = 5
    lockout_duration = timedelta(minutes=15)
    
    if failed_attempts >= lockout_threshold:
        # Lock account
        redis_client.setex(
            name=f"locked:user:{user_id}",
            time=int(lockout_duration.total_seconds()),
            value=failed_attempts
        )
        
        # Revoke all active sessions
        revoke_all_user_sessions(user_id)
        
        # Log lockout event
        log_security_event("account_locked",
                          user=user_id,
                          failed_attempts=failed_attempts,
                          lockout_duration=lockout_duration)
        
        # Notify user
        notify_user(user_id, 
                   f"Your account has been locked for {lockout_duration.minutes} minutes due to repeated failed login attempts")
        
        return True
    return False

def is_account_locked(user_id):
    """Check if account is currently locked"""
    return redis_client.exists(f"locked:user:{user_id}")
```

**Lockout Response:**
- Temporary lockout (15-30 minutes) for initial violations
- Escalating lockout duration for repeated violations (exponential backoff)
- Manual unlock required for severe violations (detected token forgery, coordinated attacks)
- Notify security team for suspicious lockout patterns

**Lockout Recovery:**
- Self-service unlock via email verification or SMS code
- Manual admin unlock with security verification
- Automatic unlock after timeout period
- Require password reset for severe security events

### Emergency Token Invalidation

**Global Invalidation Capability:**
```python
def emergency_invalidate_all_tokens(user_id=None, reason="security_incident"):
    """Emergency invalidation of tokens (user-specific or global)"""
    if user_id:
        # Invalidate all tokens for specific user
        sessions = get_active_sessions(user_id)
        for session in sessions:
            revoke_token(session.token_id, reason=reason)
        log_security_event("emergency_user_invalidation",
                          user=user_id, reason=reason)
    else:
        # Global invalidation - increment signing key version
        increment_key_version()
        # All tokens signed with old key become invalid
        log_security_event("emergency_global_invalidation", reason=reason)
        alert_critical("Global token invalidation executed", reason=reason)
```

**Emergency Scenarios:**
- Signing key compromise detected
- Mass credential theft (breach of credential database)
- Detection of widespread authentication bypass attack
- Critical vulnerability in JWT validation logic discovered
- Insider threat investigation requires immediate access revocation

**Global Invalidation Methods:**

**1. Key Version Incrementing:**
```python
# Embed key version in token claims
def issue_token_with_version(user_id):
    current_key_version = get_current_key_version()
    payload = {
        "sub": user_id,
        "exp": datetime.utcnow() + timedelta(minutes=30),
        "iat": datetime.utcnow(),
        "key_version": current_key_version
    }
    return jwt.encode(payload, get_key_for_version(current_key_version), algorithm="RS256")

def validate_token_version(token):
    payload = jwt.decode(token, public_key, algorithms=["RS256"])
    if payload.get("key_version") != get_current_key_version():
        return None  # Token invalid - old key version
    return payload
```

**2. Global Revocation Flag:**
```python
# Set global revocation timestamp
def set_global_revocation_time():
    revocation_time = datetime.utcnow()
    redis_client.set("global_revocation_timestamp", revocation_time.timestamp())
    log_security_event("global_revocation_set", timestamp=revocation_time)

def validate_against_global_revocation(token):
    payload = jwt.decode(token, public_key, algorithms=["RS256"])
    global_revocation = redis_client.get("global_revocation_timestamp")
    if global_revocation:
        revocation_time = float(global_revocation)
        if payload["iat"] < revocation_time:
            return None  # Token issued before global revocation
    return payload
```

### Security Key Rotation

**Key Rotation Procedures:**

**Planned Key Rotation (Routine):**
```python
def rotate_signing_key_planned():
    """Rotate JWT signing keys (routine maintenance)"""
    # Generate new key pair
    new_private_key, new_public_key = generate_rsa_keypair(bits=2048)
    new_key_id = generate_key_id()
    
    # Store new key
    store_signing_key(key_id=new_key_id, 
                     private_key=new_private_key,
                     public_key=new_public_key,
                     status="active")
    
    # Mark old key as "deprecated" (still validate, don't sign)
    update_key_status(current_key_id, status="deprecated")
    
    # New tokens use new key
    set_current_signing_key(new_key_id)
    
    # After grace period (e.g., max token lifetime), retire old key
    schedule_key_retirement(current_key_id, delay=timedelta(hours=1))
    
    log_security_event("signing_key_rotated", 
                      old_key=current_key_id,
                      new_key=new_key_id)
```

**Emergency Key Rotation (Compromise):**
```python
def rotate_signing_key_emergency():
    """Emergency key rotation (compromise detected)"""
    # Generate new key
    new_key_id = rotate_signing_key_planned()
    
    # Immediately retire compromised key (no grace period)
    update_key_status(current_key_id, status="compromised")
    
    # Global token invalidation
    increment_key_version()  # Invalidate all tokens signed with old key
    
    # Alert security team
    alert_critical("Signing key compromised - emergency rotation executed")
    
    # Force re-authentication for all users
    require_global_reauthentication()
```

**Key Rotation Schedule:**
- Routine rotation: Every 90 days (planned maintenance)
- Emergency rotation: Immediately upon compromise detection
- Key retirement grace period: Equal to maximum token lifetime (e.g., 1 hour for 1-hour max tokens)
- Maintain key history for audit purposes (retired keys marked "retired", not deleted)

**Multi-Key Support (JWKS):**
```json
{
  "keys": [
    {
      "kid": "key-2026-02-14",
      "kty": "RSA",
      "use": "sig",
      "alg": "RS256",
      "n": "...",
      "e": "AQAB",
      "status": "active"
    },
    {
      "kid": "key-2025-11-15",
      "kty": "RSA",
      "use": "sig",
      "alg": "RS256",
      "n": "...",
      "e": "AQAB",
      "status": "deprecated"
    }
  ]
}
```

- Publish JSON Web Key Set (JWKS) endpoint for clients to discover current keys
- Support validation of tokens signed with deprecated keys during grace period
- Include `kid` (key ID) in JWT header to identify signing key
- Validate tokens against appropriate key based on `kid` claim

## Limitations & Trade-offs

**Operational Complexity:**
- Token revocation requires maintaining server-side state (contradicts stateless JWT benefits)
- Distributed revocation cache adds infrastructure complexity
- Key rotation requires coordination across distributed systems
- Emergency invalidation causes service disruption (all users must re-authenticate)

**User Experience Impact:**
- Aggressive token rotation may cause frequent re-authentication
- Account lockout can frustrate legitimate users (false positives)
- Global invalidation forces all users to log back in (business impact)
- Revocation latency may allow brief window of continued access

**Performance Considerations:**
- Revocation checks add latency to every request (database/cache lookup)
- Distributed cache synchronization adds network overhead
- Key rotation requires updating all API gateway configurations
- Large-scale revocation (global invalidation) causes authentication stampede

**Security Trade-offs:**
- Revocation cache inconsistency window allows brief continued access
- Key rotation grace period maintains old key validity temporarily
- Account lockout can be weaponized for denial-of-service attacks

## Testing & Validation

**Revocation Testing:**

1. **Manual Revocation:**
```python
# Issue token, revoke, and verify rejection
token = issue_token(user_id="test_user")
revoke_token(extract_token_id(token), reason="test")
response = requests.get(api_url, headers={"Authorization": f"Bearer {token}"})
assert response.status_code == 401, "Revoked token was accepted"
```

2. **Refresh Token Rotation:**
```python
# Use refresh token twice and verify second use detected
refresh_token = issue_refresh_token(user_id="test_user")
response1 = rotate_refresh_token(refresh_token)  # First use - should succeed
response2 = rotate_refresh_token(refresh_token)  # Reuse - should fail and trigger alert
assert response2.status_code == 401
# Verify alert: "Refresh token reuse detected"
```

3. **Account Lockout:**
```bash
# Trigger account lockout via failed attempts
for i in {1..6}; do
  curl -X POST https://api.example.com/auth \
    -d '{"username":"test_user","password":"wrong"}'
done
# Verify lockout enforced and alert generated
```

4. **Emergency Global Invalidation:**
```python
# Issue tokens, execute global invalidation, verify all tokens rejected
tokens = [issue_token(f"user_{i}") for i in range(10)]
emergency_invalidate_all_tokens(reason="test")
for token in tokens:
    response = requests.get(api_url, headers={"Authorization": f"Bearer {token}"})
    assert response.status_code == 401
```

**Validation Checklist:**

- [ ] Revoked tokens immediately rejected (within cache sync latency)
- [ ] Refresh token rotation prevents reuse attacks
- [ ] Account lockout triggered at configured threshold
- [ ] Lockout auto-expires after timeout period
- [ ] Emergency global invalidation capability functional
- [ ] Signing key rotation procedures tested (both planned and emergency)
- [ ] JWKS endpoint publishes current and deprecated keys
- [ ] Distributed revocation cache synchronized across all nodes
- [ ] Revocation alerts routed to security team
- [ ] Token lifecycle events logged with full context

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Automated token revocation immediately revokes tokens when anomalies detected. Account lockout temporarily suspends accounts exhibiting authentication bypass attempts. Emergency token invalidation provides capability to globally invalidate all tokens for compromised users or during security incidents."
> — Extracted from [[techniques/api-authentication-bypass]]

> "Security key rotation procedures for emergency signing key rotation when weak keys are discovered or compromised. Refresh token rotation implemented to prevent indefinite token reuse. Token lifecycle management limits the window of exposure when credentials are compromised."
> — Extracted from [[techniques/api-authentication-bypass]]
