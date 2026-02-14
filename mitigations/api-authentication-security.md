---
title: "API Authentication Security"
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
# API Authentication Security

## Summary

API authentication security implements robust, industry-standard authentication mechanisms to prevent unauthorized access to LLM APIs and services. This preventive control establishes strong identity verification through properly configured OAuth 2.0/OpenID Connect, secure JWT implementation with strong signing algorithms and server-side validation, mandatory TLS encryption, token binding to client characteristics, multi-factor authentication for sensitive operations, and centralized enforcement at API gateway layers. Proper authentication security is the foundational control protecting all API endpoints—without it, all downstream authorization, rate limiting, and monitoring controls become ineffective.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/api-authentication-bypass]] | Prevents token forgery, signature bypass, and credential reuse through strong authentication mechanisms and validation |

## Implementation

### Industry-Standard Authentication Protocols

**Use OAuth 2.0 or OpenID Connect:**
- Implement OAuth 2.0 for authorization and OpenID Connect (OIDC) for authentication
- Follow RFC 6749 (OAuth 2.0) and OpenID Connect Core 1.0 specifications strictly
- Avoid custom authentication schemes—use battle-tested, peer-reviewed protocols
- Use Authorization Code Flow with PKCE (Proof Key for Code Exchange) for public clients
- Implement proper redirect URI validation to prevent authorization code interception

**Configuration Requirements:**
- Validate all redirect URIs against a strict allowlist (exact match, no wildcards)
- Enforce state parameter to prevent CSRF attacks during OAuth flows
- Implement nonce validation for OpenID Connect ID tokens
- Use separate authorization and resource servers where appropriate for defense-in-depth

### JWT Security Implementation

**Strong Signing Algorithms:**
- Use asymmetric algorithms: RS256 (RSA 2048+ bits) or ES256 (ECDSA) for production systems
- NEVER use HS256 with weak keys (<256 bits)—if symmetric signing is required, use 256+ bit keys
- NEVER accept "none" algorithm—explicitly reject unsigned tokens
- Prevent algorithm confusion attacks by validating algorithm field matches expected value

**Server-Side Validation (Every Request):**
```python
# Example: Proper JWT validation (Python/PyJWT)
import jwt
from jwt.exceptions import InvalidTokenError

def validate_jwt(token, public_key):
    try:
        # Strict validation - algorithm explicitly specified
        payload = jwt.decode(
            token,
            public_key,
            algorithms=["RS256"],  # Explicit allowlist
            options={
                "verify_signature": True,  # Always verify
                "verify_exp": True,        # Enforce expiration
                "verify_nbf": True,        # Enforce not-before
                "verify_iat": True,        # Validate issued-at
                "verify_aud": True,        # Validate audience
                "require": ["exp", "iat", "sub", "aud"]  # Required claims
            },
            audience="https://api.example.com"  # Expected audience
        )
        return payload
    except InvalidTokenError as e:
        # Log attempt and reject
        log_security_event("jwt_validation_failed", token_id=token[:20], error=str(e))
        return None
```

**Token Lifecycle Management:**
- **Short-lived access tokens**: 15-60 minute expiration (balance security vs. user experience)
- **Secure refresh tokens**: Separate refresh tokens with longer expiration, rotated on each use
- **Refresh token rotation**: Issue new refresh token on refresh, invalidate old token (prevents replay)
- **Refresh token storage**: Store refresh tokens securely server-side with user binding

**Claim Validation Requirements:**
- Validate `exp` (expiration) server-side—NEVER trust client-side expiration checks
- Validate `nbf` (not-before) to prevent premature token usage
- Validate `iat` (issued-at) to detect clock skew and backdated tokens
- Validate `iss` (issuer) matches expected authorization server
- Validate `aud` (audience) matches current API service
- Validate `sub` (subject) exists and is non-empty
- Reject tokens with future expiration dates beyond maximum allowed lifetime

### TLS/HTTPS Enforcement

**Mandatory Transport Security:**
- Require TLS 1.2 or TLS 1.3 for all API communications (no TLS 1.0/1.1)
- Configure strong cipher suites only (disable weak ciphers: RC4, 3DES, MD5-based)
- Implement HSTS (HTTP Strict Transport Security) header with long max-age
- Reject all HTTP requests—no fallback to unencrypted transport
- Use HTTPS redirects (301/308) for HTTP requests before authentication

**Certificate Management:**
- Use certificates from trusted Certificate Authorities (CAs)
- Implement certificate pinning for high-security environments
- Monitor certificate expiration and automate renewal
- Validate certificate chains and revocation status (OCSP/CRL)

### Token Binding to Client Characteristics

**Binding Mechanisms:**
- **IP address binding**: Validate token usage from same IP as issuance (with tolerance for mobile networks)
- **Device fingerprinting**: Bind tokens to browser fingerprint or device ID
- **Cryptographic binding**: Use DPoP (Demonstrating Proof of Possession) or certificate-bound tokens
- **User-Agent validation**: Verify User-Agent header consistency (detect token theft across platforms)

**Example: IP-based token binding:**
```python
def issue_token(user_id, client_ip):
    payload = {
        "sub": user_id,
        "exp": datetime.utcnow() + timedelta(minutes=30),
        "iat": datetime.utcnow(),
        "client_ip": client_ip  # Bind to issuing IP
    }
    return jwt.encode(payload, private_key, algorithm="RS256")

def validate_token_binding(token, request_ip):
    payload = validate_jwt(token, public_key)
    if payload and payload.get("client_ip") != request_ip:
        log_security_event("token_ip_mismatch", 
                          token_ip=payload.get("client_ip"), 
                          request_ip=request_ip)
        return False
    return payload
```

### Multi-Factor Authentication (MFA)

**MFA for Sensitive Operations:**
- Require second factor for admin API access, privileged operations, or sensitive data queries
- Support TOTP (Time-based One-Time Password), WebAuthn/FIDO2, or push notifications
- Implement step-up authentication: re-authenticate for high-risk operations even in active session
- Never bypass MFA for "trusted" networks—zero-trust principle applies

**MFA Implementation:**
- Enforce MFA enrollment for privileged accounts
- Support backup authentication methods (recovery codes)
- Rate-limit MFA verification attempts to prevent brute-force
- Log all MFA events (enrollment, verification, failures)

### API Gateway Authentication Enforcement

**Centralized Authentication Layer:**
- Implement authentication validation at API gateway/proxy layer (Kong, AWS API Gateway, Apigee)
- NEVER rely solely on individual microservice authentication—gateway is the enforcement point
- Gateway validates tokens before routing requests to backend services
- Backend services receive validated identity context (extracted claims) from gateway

**Gateway Configuration:**
- Configure gateway to reject requests without valid authentication tokens
- Implement token caching at gateway to reduce validation overhead (with TTL matching token expiration)
- Pass validated user identity to backend services via secure headers (X-User-ID, X-User-Role)
- Log all authentication decisions at gateway for centralized monitoring

**Defense-in-Depth:**
- Backend services should still validate received identity context (don't blindly trust gateway)
- Use mutual TLS (mTLS) between gateway and backend services to prevent internal spoofing
- Implement network segmentation to restrict direct backend access bypassing gateway

## Limitations & Trade-offs

**Operational Complexity:**
- OAuth 2.0/OIDC implementation requires dedicated authorization server infrastructure
- Key management for JWT signing adds operational overhead (key rotation, secure storage)
- Token binding can cause usability issues (mobile network IP changes, NAT environments)
- MFA adds friction to user experience—balance security requirements with usability

**Performance Impact:**
- Server-side JWT signature verification adds latency (RSA/ECDSA operations are computationally expensive)
- Token binding validation requires additional database lookups or state checks
- TLS encryption/decryption adds overhead (mitigated by hardware acceleration)
- Centralized gateway authentication can become bottleneck at scale

**False Positives:**
- IP-based token binding may block legitimate users on mobile networks or VPNs
- Aggressive token expiration may force frequent re-authentication
- Device fingerprinting can break for users clearing cookies or using privacy tools

**Attack Surface Considerations:**
- Authorization server becomes high-value target (compromise grants access to all systems)
- JWT signing keys require secure storage and rotation procedures
- Refresh token storage introduces server-side state and potential credential stuffing target
- API gateway becomes single point of failure if not properly redundant

## Testing & Validation

**Automated Security Testing:**

1. **Algorithm Confusion Testing:**
```bash
# Test if API accepts "none" algorithm (should reject)
jwt_tool.py <captured_token> -X a  # Algorithm confusion attack
```

2. **Signature Bypass Testing:**
```bash
# Modify JWT payload without re-signing
jwt_tool.py <captured_token> -I -pc "role" -pv "admin"  # Inject claims
```

3. **Expiration Validation Testing:**
```python
# Test if expired tokens are rejected
expired_token = create_token(exp=datetime.utcnow() - timedelta(hours=1))
response = requests.get(api_url, headers={"Authorization": f"Bearer {expired_token}"})
assert response.status_code == 401, "API accepted expired token"
```

4. **TLS Enforcement Testing:**
```bash
# Test if HTTP requests are rejected
curl -k http://api.example.com/endpoint
# Should return 301/308 redirect or connection refused
```

5. **Token Binding Validation:**
```python
# Test if tokens can be used from different IP
token = authenticate_from_ip("192.168.1.100")
response = requests.get(api_url, headers={"Authorization": f"Bearer {token}"},
                       proxies={"https": "different-ip-proxy"})
assert response.status_code == 401, "Token accepted from different IP"
```

**Manual Security Review:**

- [ ] Audit OAuth 2.0 configuration (redirect URIs, state parameter, PKCE)
- [ ] Review JWT signing algorithm configuration (no "none", strong keys)
- [ ] Validate server-side claim validation implementation
- [ ] Test token lifecycle (expiration, refresh rotation)
- [ ] Verify TLS configuration (cipher suites, HSTS headers)
- [ ] Test MFA enforcement for privileged operations
- [ ] Review API gateway authentication rules
- [ ] Test defense-in-depth (backend still validates after gateway)

**Penetration Testing Scenarios:**

- Attempt to brute-force HS256 signing keys if symmetric signing is used
- Test for OAuth redirect URI manipulation and authorization code interception
- Attempt token replay attacks across different clients/IPs
- Test for session fixation vulnerabilities
- Attempt to escalate privileges by modifying JWT claims
- Test refresh token reuse after rotation

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Weak or missing API authentication allows unauthorized access to LLM endpoints through token forgery, signature bypass, or credential reuse. Since authentication is the gateway control protecting all LLM API endpoints, bypass vulnerabilities render all downstream authorization, rate limiting, and monitoring controls ineffective."
> — Extracted from [[techniques/api-authentication-bypass]]

> "Use industry-standard OAuth 2.0 or OpenID Connect with secure configurations. Avoid custom authentication schemes. Enforce strong JWT signing algorithms (RS256 with 2048+ bit keys or ES256). Validate signatures server-side for every request, never trust client-side validation."
> — Extracted from [[techniques/api-authentication-bypass]]
