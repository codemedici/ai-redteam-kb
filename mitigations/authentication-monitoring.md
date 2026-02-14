---
title: "Authentication Monitoring"
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
# Authentication Monitoring

## Summary

Authentication monitoring provides detective controls that identify authentication bypass attempts, compromised credentials, and anomalous authentication patterns in real-time. This mitigation implements comprehensive logging, behavioral analytics, anomaly detection, and alerting across all authentication events to detect attacks that evade preventive controls. By monitoring for expired token reuse, signature bypass attempts, unusual geolocation patterns, token integrity violations, and coordinated credential abuse, organizations can rapidly detect and respond to authentication attacks before significant damage occurs.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/api-authentication-bypass]] | Detects token forgery, signature bypass, expired token reuse, and other authentication attack patterns |

## Implementation

### Authentication Anomaly Detection

**Expired Token Reuse Detection:**
- Monitor for API requests with expired access tokens
- Alert on repeated attempts to use expired tokens (indicates missing server-side validation or attacker probing)
- Track token expiration timestamps and flag usage beyond lifetime

**Example detection rule:**
```python
def detect_expired_token_reuse(auth_events):
    """Alert on expired tokens being accepted"""
    for event in auth_events:
        if event.token_expired and event.authentication_success:
            alert(severity="CRITICAL",
                  message="Expired token accepted by API",
                  token_id=event.token_id,
                  user=event.user_id,
                  endpoint=event.endpoint)
```

**Algorithm Confusion Detection:**
- Monitor JWT validation logs for tokens using "none" algorithm
- Alert immediately on any token with missing or "none" signature algorithm
- Track algorithm field modifications (RS256 → HS256, RS256 → none)

**Token Payload Manipulation Detection:**
- Detect signature validation failures (indicates payload tampering)
- Monitor for tokens with modified claims (user_id, role, scope changes)
- Alert on tokens with future expiration dates beyond maximum allowed lifetime
- Track tokens with missing required claims

**Example anomaly patterns:**
```yaml
# Anomaly detection rules (SIEM/log analytics)
rules:
  - name: "JWT None Algorithm Detected"
    condition: jwt.algorithm == "none" OR jwt.algorithm == "None"
    severity: CRITICAL
    
  - name: "JWT Signature Validation Failed"
    condition: jwt.signature_valid == false AND http.status == 200
    severity: CRITICAL
    description: "API accepted token with invalid signature"
    
  - name: "Future Expiration Date"
    condition: jwt.exp > (current_time + max_token_lifetime)
    severity: HIGH
    
  - name: "Expired Token Accepted"
    condition: jwt.exp < current_time AND http.status == 200
    severity: CRITICAL
```

### Behavioral Analytics

**User Authentication Patterns:**
- Establish baseline authentication patterns per user (typical hours, geolocations, devices)
- Detect deviations from baseline:
  - Authentication from new geolocation (different country/continent)
  - Authentication during unusual hours (night-time for daytime user)
  - Sudden change in device fingerprint or User-Agent
  - Multiple concurrent sessions from different locations

**Token Usage Anomalies:**
- Monitor token usage frequency and patterns
- Detect unusual API call patterns for authenticated user (volume spikes, new endpoints)
- Alert on privilege escalation within session (analyst → admin role)
- Track session duration anomalies (unusually long sessions may indicate stolen tokens)

**Example behavioral detection:**
```python
def detect_authentication_anomaly(user_id, auth_event):
    """Behavioral anomaly detection for authentication"""
    baseline = get_user_baseline(user_id)
    
    # Geolocation anomaly
    if geo_distance(auth_event.location, baseline.typical_location) > 500km:
        if auth_event.timestamp - baseline.last_auth < 1hour:
            alert("Impossible travel", user=user_id, 
                  prev_location=baseline.last_location,
                  current_location=auth_event.location)
    
    # Time-of-day anomaly
    if auth_event.hour not in baseline.typical_hours:
        score_anomaly(user_id, "unusual_time_of_day", severity="MEDIUM")
    
    # Device fingerprint change
    if auth_event.device_fingerprint != baseline.device_fingerprint:
        alert("New device detected", user=user_id,
              device=auth_event.device_fingerprint)
```

### Token Integrity Monitoring

**Token Attribute Validation:**
- Monitor for tokens with suspicious attributes:
  - Missing standard claims (sub, exp, iat)
  - Non-standard or unexpected claims injected
  - Mismatched issuer or audience fields
  - Token IDs reused across different sessions

**Signing Key Monitoring:**
- Alert on weak signing keys detected (HS256 with <256 bits)
- Monitor for signing algorithm downgrades (RS256 → HS256)
- Track key rotation events and validate proper implementation
- Detect key compromise indicators (same key used across environments)

### Failed Authentication Logging

**Comprehensive Authentication Logging:**
```json
{
  "event_type": "authentication_attempt",
  "timestamp": "2026-02-14T19:45:32Z",
  "user_id": "user_12345",
  "client_ip": "203.0.113.42",
  "geolocation": {"country": "US", "city": "San Francisco"},
  "user_agent": "Mozilla/5.0...",
  "device_fingerprint": "abc123...",
  "token_id": "jti_xyz789",
  "authentication_method": "jwt_bearer",
  "result": "failure",
  "failure_reason": "signature_validation_failed",
  "endpoint": "/api/v1/llm/query",
  "request_id": "req_456def"
}
```

**Log all authentication events:**
- Successful authentications (with token ID, user, IP, geolocation)
- Failed authentication attempts (with failure reason: expired, invalid signature, missing claims)
- Token refresh events (old token ID → new token ID for rotation tracking)
- MFA verification attempts and results
- Account lockout events
- Token revocation events

**Failed Authentication Analysis:**
- Track failed authentication attempts per user, IP, and endpoint
- Alert on repeated failures from single IP (brute-force indicator)
- Detect distributed brute-force (many IPs, same user)
- Monitor for credential stuffing patterns (many users, same IP or IP range)

### Session Anomaly Detection

**Session Lifecycle Monitoring:**
- Track refresh token usage patterns
- Alert on refresh tokens used beyond expected lifecycle (never expiring)
- Detect refresh token reuse after rotation (indicates token theft or implementation flaw)
- Monitor for multiple concurrent sessions per user (unusual for most workflows)

**Example session anomaly rules:**
```python
def detect_session_anomalies(user_id):
    """Detect unusual session patterns"""
    sessions = get_active_sessions(user_id)
    
    # Multiple concurrent sessions from different geolocations
    if len(sessions) > 1:
        locations = [s.geolocation for s in sessions]
        if geo_diversity(locations) > threshold:
            alert("Concurrent sessions from multiple locations",
                  user=user_id, sessions=sessions)
    
    # Refresh token used beyond expected lifecycle
    for session in sessions:
        if session.duration > max_session_lifetime:
            alert("Session exceeds maximum lifetime",
                  user=user_id, session_id=session.id,
                  duration=session.duration)
        
        # Refresh token reused after rotation
        if session.refresh_token in revoked_tokens:
            alert("Revoked refresh token reused",
                  user=user_id, token=session.refresh_token)
```

### Geolocation and IP Monitoring

**Geolocation-Based Detection:**
- Track authentication source geolocations per user
- Alert on impossible travel (user authenticated from distant locations in short time)
- Detect authentication from high-risk countries or known VPN/proxy IPs
- Monitor for geolocation spoofing indicators (IP geolocation mismatch with claimed location)

**IP Reputation Monitoring:**
- Integrate with threat intelligence feeds for malicious IP detection
- Alert on authentication attempts from known attacker IPs, Tor exit nodes, or botnet IPs
- Track IP reputation scores and flag low-reputation sources
- Detect proxy/VPN usage patterns (may indicate evasion)

**Example geolocation rules:**
```yaml
rules:
  - name: "Impossible Travel Detected"
    condition: |
      geo_distance(current_location, previous_location) > 500km
      AND time_delta < 1hour
    severity: HIGH
    
  - name: "Authentication from High-Risk Country"
    condition: country in ["XX", "YY", "ZZ"]  # High-risk countries
    severity: MEDIUM
    
  - name: "Tor Exit Node Authentication"
    condition: ip in tor_exit_node_list
    severity: HIGH
```

### Brute-Force and Credential Stuffing Detection

**Brute-Force Attack Detection:**
- Monitor authentication failure rates per IP, user, and endpoint
- Alert on threshold violations:
  - >5 failed attempts per user in 5 minutes
  - >10 failed attempts per IP in 5 minutes
  - >50 failed attempts per endpoint in 10 minutes
- Implement exponential backoff or account lockout after threshold

**Credential Stuffing Detection:**
- Detect large-scale authentication attempts across many usernames from single IP or IP range
- Monitor for distributed credential stuffing (botnet pattern: many IPs, coordinated timing)
- Track password spray patterns (common password tried across many accounts)

## Limitations & Trade-offs

**False Positive Rate:**
- Geolocation-based detection may flag legitimate travel (business trips, VPN usage)
- Behavioral analytics require baseline establishment (new users have no baseline)
- Token binding validation can break for mobile users (IP changes frequently)
- Time-of-day anomalies may flag legitimate use cases (on-call engineers, global teams)

**Performance Overhead:**
- Real-time anomaly detection adds latency to authentication flow
- Behavioral analytics require maintaining historical baselines (storage costs)
- Geolocation lookups add external dependency and latency
- Log volume for high-traffic APIs can be substantial (storage and analysis costs)

**Operational Complexity:**
- SIEM/log analytics infrastructure required for effective monitoring
- Anomaly detection rules require tuning to reduce false positives
- Behavioral baselines need continuous updating as user patterns evolve
- Alert fatigue if thresholds too aggressive

**Privacy Considerations:**
- Logging geolocation and device fingerprints may raise privacy concerns
- Must comply with GDPR, CCPA for personal data in authentication logs
- Log retention policies must balance security needs with privacy requirements

## Testing & Validation

**Detection Rule Testing:**

1. **Expired Token Reuse:**
```bash
# Generate expired token and test if detection triggers
expired_token=$(create_token --exp -1h)
curl -H "Authorization: Bearer $expired_token" https://api.example.com/test
# Verify alert generated
```

2. **Algorithm Confusion:**
```python
# Create token with "none" algorithm and verify detection
token_none = create_unsigned_token(payload)
response = requests.get(api_url, headers={"Authorization": f"Bearer {token_none}"})
# Check SIEM for alert: "JWT None Algorithm Detected"
```

3. **Geolocation Anomaly:**
```python
# Authenticate from normal location, then immediately from distant location
auth1 = authenticate_from_ip("us-west-ip")  # San Francisco
time.sleep(10)
auth2 = authenticate_from_ip("eu-central-ip")  # Frankfurt
# Verify "Impossible Travel" alert triggered
```

4. **Brute-Force Detection:**
```bash
# Attempt multiple failed authentications
for i in {1..10}; do
  curl -X POST https://api.example.com/auth \
    -d '{"username":"target_user","password":"wrong_password"}'
done
# Verify rate-limiting or lockout triggered
```

**Validation Checklist:**

- [ ] Authentication events logged with all required fields (user, IP, geolocation, result, failure reason)
- [ ] Expired token reuse alerts trigger within acceptable latency (<5 seconds)
- [ ] Algorithm confusion attempts detected and logged
- [ ] Signature validation failures trigger alerts
- [ ] Geolocation anomalies detected (impossible travel, high-risk countries)
- [ ] Behavioral analytics baseline established for active users
- [ ] Failed authentication thresholds properly configured and enforced
- [ ] Session anomalies detected (concurrent sessions, refresh token reuse)
- [ ] Brute-force detection triggers account lockout or rate limiting
- [ ] SIEM integration functional with alert routing to security team

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Authentication anomaly detection monitors for expired token reuse, 'none' algorithm tokens, manipulated payload patterns. Behavioral analytics detect authentication patterns inconsistent with user profiles (geolocation changes, unusual access times, device fingerprint mismatches)."
> — Extracted from [[techniques/api-authentication-bypass]]

> "Track failed signature validation attempts, expired token rejections, and brute-force patterns. Monitor for refresh tokens used beyond expected lifecycle or from multiple concurrent sessions. Alert when tokens are used from geolocations or IP ranges inconsistent with user history."
> — Extracted from [[techniques/api-authentication-bypass]]
