---
title: "AI API Security"
tags:
  - type/mitigation
  - target/ml-model
  - target/llm
  - target/generative-ai
  - source/red-teaming-ai
atlas: AML.M0015
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# AI API Security

## Summary

AI API security protects inference endpoints from model extraction, denial of service, unauthorized access, injection attacks, and information leakage through rate limiting, strong authentication, input validation, minimal error disclosure, and API gateway controls. APIs exposing AI functionality—whether LLM chat interfaces, computer vision services, or recommendation engines—represent critical attack surfaces requiring layered defenses beyond traditional web API security.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0024 | [[techniques/model-extraction-stealing]] | Rate limiting prevents automated query-based model extraction; response filtering reduces information leakage |
| AML.T0008 | [[techniques/ai-infrastructure-attacks]] | Strong authentication, input validation, and gateway controls prevent API abuse, injection attacks, and DoS |
| AML.T0051 | [[techniques/prompt-injection]] | Input validation and sanitization reduce prompt injection attack surface |

## Implementation

### Rate Limiting

**Per-user query quotas:**
```python
from flask import Flask, request
from flask_limiter import Limiter
from flask_limiter.util import get_remote_address

app = Flask(__name__)
limiter = Limiter(
    get_remote_address,
    app=app,
    default_limits=["200 per day", "50 per hour"],
    storage_uri="redis://localhost:6379"
)

@app.route("/api/v1/predict", methods=["POST"])
@limiter.limit("10 per minute")  # Model-specific limit
def predict():
    """Inference endpoint with rate limiting"""
    data = request.get_json()
    prediction = model.predict(data['input'])
    
    return {"prediction": prediction}
```

**Adaptive throttling based on behavior:**
```python
from collections import defaultdict
import time

class AdaptiveRateLimiter:
    """Dynamically adjust rate limits based on user behavior"""
    
    def __init__(self):
        self.request_history = defaultdict(list)
        self.blocked_users = {}
    
    def check_rate_limit(self, user_id, endpoint):
        """Check if request should be allowed"""
        now = time.time()
        
        # Clean old requests (>1 hour)
        self.request_history[user_id] = [
            ts for ts in self.request_history[user_id]
            if now - ts < 3600
        ]
        
        # Count recent requests
        recent_requests = len(self.request_history[user_id])
        
        # Adaptive limits
        if recent_requests < 10:
            limit = 100  # Normal user
        elif recent_requests < 50:
            limit = 50   # Heavy user
        else:
            limit = 10   # Potential extraction attempt
        
        # Check if over limit
        if recent_requests >= limit:
            self.blocked_users[user_id] = now + 3600  # Block for 1 hour
            return False, f"Rate limit exceeded: {recent_requests}/{limit}"
        
        # Allow request
        self.request_history[user_id].append(now)
        return True, None
```

**Distributed rate limiting (API Gateway):**
```yaml
# Kong API Gateway configuration
services:
  - name: ml-inference-api
    url: http://inference-backend:8000
    routes:
      - name: predict
        paths:
          - /predict
    plugins:
      - name: rate-limiting
        config:
          minute: 10
          hour: 100
          day: 1000
          policy: redis
          redis_host: redis.default.svc.cluster.local
      
      - name: response-ratelimiting
        config:
          limits:
            prediction:
              minute: 5  # Limit expensive predictions
```

### Strong Authentication

**OAuth 2.0 with scoped permissions:**
```python
from authlib.integrations.flask_oauth2 import ResourceProtector
from authlib.oauth2.rfc6750 import BearerTokenValidator

require_oauth = ResourceProtector()

class MyBearerTokenValidator(BearerTokenValidator):
    def authenticate_token(self, token_string):
        return Token.query.filter_by(access_token=token_string).first()
    
    def request_invalid(self, request):
        return False
    
    def token_revoked(self, token):
        return token.revoked

require_oauth.register_token_validator(MyBearerTokenValidator())

@app.route('/api/v1/predict')
@require_oauth('predict:read')  # Requires specific scope
def predict():
    user = request.oauth.user
    # Authorization logic
```

**API key management with rotation:**
```python
import secrets
import hashlib

class APIKeyManager:
    """Secure API key generation and validation"""
    
    def generate_key(self, user_id):
        """Generate cryptographically secure API key"""
        # Format: prefix.random_bytes
        key = f"sk_live_{secrets.token_urlsafe(32)}"
        
        # Store hashed version only
        key_hash = hashlib.sha256(key.encode()).hexdigest()
        
        db.save_api_key(
            user_id=user_id,
            key_hash=key_hash,
            created_at=datetime.utcnow(),
            expires_at=datetime.utcnow() + timedelta(days=90)
        )
        
        # Return key once (never stored in plaintext)
        return key
    
    def validate_key(self, provided_key):
        """Validate API key against stored hash"""
        key_hash = hashlib.sha256(provided_key.encode()).hexdigest()
        
        key_record = db.get_key_by_hash(key_hash)
        
        if not key_record:
            return False, "Invalid API key"
        
        if key_record.expires_at < datetime.utcnow():
            return False, "API key expired"
        
        if key_record.revoked:
            return False, "API key revoked"
        
        return True, key_record.user_id
```

**Mutual TLS (mTLS) for high-security endpoints:**
```python
from flask import Flask
import ssl

app = Flask(__name__)

# Configure mTLS
context = ssl.SSLContext(ssl.PROTOCOL_TLSv1_2)
context.verify_mode = ssl.CERT_REQUIRED
context.load_cert_chain('server.crt', 'server.key')
context.load_verify_locations('ca.crt')  # Client CA

@app.route('/api/v1/sensitive-predict')
def sensitive_predict():
    """Endpoint requires client certificate"""
    # Client cert verified by TLS layer
    client_cert = request.environ.get('SSL_CLIENT_CERT')
    # Authorization based on cert
    
if __name__ == '__main__':
    app.run(ssl_context=context)
```

### Input Validation

**Schema enforcement:**
```python
from jsonschema import validate, ValidationError

PREDICT_SCHEMA = {
    "type": "object",
    "properties": {
        "input_text": {
            "type": "string",
            "minLength": 1,
            "maxLength": 5000  # Prevent oversized inputs
        },
        "temperature": {
            "type": "number",
            "minimum": 0.0,
            "maximum": 2.0
        }
    },
    "required": ["input_text"],
    "additionalProperties": False  # Reject unknown fields
}

@app.route('/api/v1/predict', methods=['POST'])
def predict():
    """Validate input against schema"""
    try:
        data = request.get_json()
        validate(instance=data, schema=PREDICT_SCHEMA)
    except ValidationError as e:
        return {"error": "Invalid input", "details": str(e)}, 400
    
    # Process validated input
    prediction = model.predict(data['input_text'])
    return {"prediction": prediction}
```

**Type checking and sanitization:**
```python
import bleach

def sanitize_input(user_input):
    """Sanitize user input for LLM prompts"""
    # Remove HTML/script tags
    clean_input = bleach.clean(user_input, tags=[], strip=True)
    
    # Limit length
    if len(clean_input) > 5000:
        raise ValueError("Input exceeds maximum length")
    
    # Detect prompt injection patterns
    injection_patterns = [
        r'ignore\s+previous\s+instructions',
        r'system\s+prompt',
        r'<\|im_start\|>',
        r'\\n\\nHuman:',
    ]
    
    for pattern in injection_patterns:
        if re.search(pattern, clean_input, re.IGNORECASE):
            raise SecurityError("Potential prompt injection detected")
    
    return clean_input
```

### Minimal Information Disclosure

**Generic error messages:**
```python
@app.errorhandler(Exception)
def handle_error(error):
    """Return generic errors, log details internally"""
    # Log full error server-side
    logger.error(f"API Error: {type(error).__name__}: {str(error)}", 
                 exc_info=True)
    
    # Return generic message to client
    return {
        "error": "Internal server error",
        "request_id": generate_request_id()
    }, 500

# NOT: return {"error": str(error), "stack_trace": ...}
```

**Response filtering:**
```python
def filter_response(prediction_result):
    """Remove sensitive internal details from API response"""
    return {
        "prediction": prediction_result.label,
        "confidence": round(prediction_result.confidence, 2)
        # DO NOT include:
        # - "logits": prediction_result.raw_logits (enables extraction)
        # - "model_version": "v2.3.1" (reveals internals)
        # - "processing_time": 0.043 (timing side-channel)
    }
```

**Confidence thresholding:**
```python
def predict_with_threshold(input_data):
    """Reduce extraction risk by limiting confidence scores"""
    result = model.predict(input_data)
    
    # Quantize confidence to reduce precision
    if result.confidence > 0.9:
        confidence_band = "high"
    elif result.confidence > 0.7:
        confidence_band = "medium"
    else:
        confidence_band = "low"
    
    return {
        "prediction": result.label,
        "confidence": confidence_band  # Not raw score
    }
```

### API Gateway Integration

**Centralized security controls:**
```yaml
# AWS API Gateway configuration
resources:
  PredictAPI:
    Type: AWS::ApiGateway::RestApi
    Properties:
      Name: ML-Inference-API
      
  PredictResource:
    Type: AWS::ApiGateway::Resource
    Properties:
      RestApiId: !Ref PredictAPI
      ParentId: !GetAtt PredictAPI.RootResourceId
      PathPart: predict
      
  PredictMethod:
    Type: AWS::ApiGateway::Method
    Properties:
      RestApiId: !Ref PredictAPI
      ResourceId: !Ref PredictResource
      HttpMethod: POST
      AuthorizationType: AWS_IAM  # Require IAM authentication
      RequestValidatorId: !Ref RequestValidator
      RequestModels:
        application/json: !Ref PredictRequestModel
      MethodResponses:
        - StatusCode: 200
        - StatusCode: 429  # Rate limit exceeded
```

**WAF integration:**
```json
// AWS WAF rules for API protection
{
  "Name": "ML-API-Protection",
  "Rules": [
    {
      "Name": "RateLimitRule",
      "Priority": 1,
      "Action": {"Block": {}},
      "Statement": {
        "RateBasedStatement": {
          "Limit": 100,
          "AggregateKeyType": "IP"
        }
      }
    },
    {
      "Name": "SQLInjectionRule",
      "Priority": 2,
      "Action": {"Block": {}},
      "Statement": {
        "SqliMatchStatement": {
          "FieldToMatch": {"Body": {}},
          "TextTransformations": [{"Priority": 0, "Type": "URL_DECODE"}]
        }
      }
    }
  ]
}
```

## Limitations & Trade-offs

**Limitations:**
- **Distributed extraction**: Attackers using multiple IPs/accounts bypass per-user rate limits
- **Legitimate high-volume users**: Rate limiting may block valid use cases
- **Credential theft**: Strong auth ineffective if keys/tokens compromised
- **Zero-day exploits**: Input validation can't catch unknown attack patterns

**Trade-offs:**
- **Security vs. Usability**: Strict rate limits frustrate legitimate users
- **Performance vs. Validation**: Deep input inspection adds latency
- **Transparency vs. Security**: Minimal error disclosure makes debugging harder

## Testing & Validation

1. **Rate limit enforcement**: Automated query flood; verify throttling
2. **Authentication bypass**: Attempt requests without credentials; confirm rejection
3. **Input validation**: Send malformed inputs; verify rejection with appropriate errors
4. **Injection attempts**: Test prompt injection patterns; verify detection
5. **Information leakage**: Check error messages for stack traces, internal paths

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Lack of rate limiting enables automated model extraction via excessive queries and denial of service through resource exhaustion. Defensive strategies include per-user query quotas, adaptive throttling based on behavior, and distributed rate limiting across API gateway clusters."
> — [[sources/bibliography#Red-Teaming AI]], p. 296-297

> "Weak authentication with leaked or guessable API keys enables unauthorized access. Strong authentication requires OAuth 2.0, short-lived API keys with rotation, and mutual TLS for highest security."
> — [[sources/bibliography#Red-Teaming AI]], p. 297

> "Insufficient input validation enables injection attacks, oversized inputs cause DoS, and malformed data triggers crashes. Whitelist validation, type checking, size limits, and sanitization are critical defenses."
> — [[sources/bibliography#Red-Teaming AI]], p. 297-298

> "Information leakage through detailed error messages reveals internals; excessive response data (confidence scores) enables extraction. Minimal disclosure with generic errors and response filtering reduce attack surface."
> — [[sources/bibliography#Red-Teaming AI]], p. 298

## Related

- **Complements**: [[mitigations/input-validation-patterns]], [[mitigations/output-filtering-and-sanitization]]
- **Defends against**: [[techniques/model-extraction-stealing]], [[techniques/prompt-injection]]
