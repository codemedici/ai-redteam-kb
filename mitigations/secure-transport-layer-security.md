---
title: "Secure Transport Layer Security"
tags:
  - type/mitigation
  - target/llm-app
  - target/ml-model
  - target/agent
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Secure Transport Layer Security

## Summary

Secure transport layer security protects model outputs during transit between inference services and consuming applications using TLS encryption, mutual TLS (mTLS) authentication, and secure service mesh architectures. By enforcing encrypted channels for all model-to-application communication, organizations prevent man-in-the-middle interception and tampering of predictions. This mitigation is critical for defending against output integrity attacks that exploit weak transport security to modify model responses before they reach downstream systems.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0051 | [[techniques/output-integrity-attack]] | Prevents interception and modification of model outputs through encrypted transport; mTLS ensures both endpoints are authenticated |

## Implementation

### TLS 1.3 Enforcement

**Enforce TLS 1.3+ for all model API endpoints:**

**Example: Flask API with TLS enforcement**

```python
from flask import Flask
import ssl

app = Flask(__name__)

# Configure TLS context
context = ssl.SSLContext(ssl.PROTOCOL_TLS_SERVER)
context.load_cert_chain('server_certificate.pem', 'server_private_key.pem')

# Enforce TLS 1.3 minimum version
context.minimum_version = ssl.TLSVersion.TLSv1_3

# Disable weak ciphers
context.set_ciphers('ECDHE+AESGCM:ECDHE+CHACHA20:DHE+AESGCM:DHE+CHACHA20:!aNULL:!MD5:!DSS')

# Model inference endpoint
@app.route('/api/v1/predict', methods=['POST'])
def predict():
    # Process prediction request
    # All communication encrypted via TLS 1.3
    pass

if __name__ == '__main__':
    app.run(ssl_context=context, host='0.0.0.0', port=443)
```

**Nginx reverse proxy with TLS termination:**

```nginx
server {
    listen 443 ssl http2;
    server_name model-api.example.com;

    # TLS 1.3 only
    ssl_protocols TLSv1.3;
    
    # Strong cipher suites
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305';
    ssl_prefer_server_ciphers off;
    
    # Certificate configuration
    ssl_certificate /etc/ssl/certs/model-api.crt;
    ssl_certificate_key /etc/ssl/private/model-api.key;
    
    # HSTS header (force HTTPS)
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    
    # Proxy to model service
    location /api/v1/ {
        proxy_pass http://localhost:8000;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Host $host;
    }
}

# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name model-api.example.com;
    return 301 https://$server_name$request_uri;
}
```

### Mutual TLS (mTLS) Authentication

**Enforce bidirectional authentication for service-to-service communication:**

**Server-side configuration (requires client certificates):**

```python
import ssl
from flask import Flask, request

app = Flask(__name__)

# Configure mTLS context
context = ssl.SSLContext(ssl.PROTOCOL_TLS_SERVER)
context.load_cert_chain('server_certificate.pem', 'server_private_key.pem')

# Require client certificates
context.verify_mode = ssl.CERT_REQUIRED
context.load_verify_locations('client_ca.pem')  # CA that signed client certs

@app.route('/api/v1/predict', methods=['POST'])
def predict():
    # Extract client certificate information
    client_cert = request.environ.get('SSL_CLIENT_CERT')
    client_subject = request.environ.get('SSL_CLIENT_S_DN')
    
    # Verify client identity
    if not is_authorized_client(client_subject):
        return {"error": "Unauthorized client"}, 403
    
    # Process prediction (client authenticated)
    prediction = model.predict(request.get_json())
    return {"prediction": prediction}

if __name__ == '__main__':
    app.run(ssl_context=context, host='0.0.0.0', port=443)
```

**Client-side configuration (presents certificate):**

```python
import requests

# Load client certificate and key
client_cert = ('client_certificate.pem', 'client_private_key.pem')

# Make authenticated request to model service
response = requests.post(
    'https://model-api.example.com/api/v1/predict',
    json={'input': data},
    cert=client_cert,  # Present client certificate
    verify='server_ca.pem'  # Verify server certificate
)

prediction = response.json()
```

**mTLS configuration in Nginx:**

```nginx
server {
    listen 443 ssl http2;
    server_name model-api.example.com;

    # Server certificate
    ssl_certificate /etc/ssl/certs/server.crt;
    ssl_certificate_key /etc/ssl/private/server.key;
    
    # Require client certificates
    ssl_client_certificate /etc/ssl/certs/client_ca.crt;
    ssl_verify_client on;
    ssl_verify_depth 2;
    
    # TLS 1.3 enforcement
    ssl_protocols TLSv1.3;
    
    location /api/v1/ {
        # Verify client certificate before proxying
        if ($ssl_client_verify != SUCCESS) {
            return 403;
        }
        
        proxy_pass http://localhost:8000;
        
        # Pass client certificate info to backend
        proxy_set_header X-SSL-Client-Cert $ssl_client_cert;
        proxy_set_header X-SSL-Client-S-DN $ssl_client_s_dn;
    }
}
```

### Service Mesh Architecture (Istio)

**Deploy service mesh to enforce mTLS by default for all inter-service communication:**

**Istio PeerAuthentication policy:**

```yaml
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: default
  namespace: ml-platform
spec:
  mtls:
    mode: STRICT  # Enforce mTLS for all services
```

**DestinationRule for model service:**

```yaml
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: model-service
  namespace: ml-platform
spec:
  host: model-service.ml-platform.svc.cluster.local
  trafficPolicy:
    tls:
      mode: ISTIO_MUTUAL  # Use Istio-managed mTLS certificates
```

**VirtualService for traffic encryption:**

```yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: model-service
  namespace: ml-platform
spec:
  hosts:
  - model-service
  http:
  - match:
    - uri:
        prefix: /api/v1/predict
    route:
    - destination:
        host: model-service
        port:
          number: 443  # HTTPS endpoint
    # All traffic encrypted via Istio mTLS
```

**Benefits of service mesh:**
- **Automatic mTLS:** Istio automatically manages certificates and encrypts all traffic
- **Zero-trust networking:** Every service authenticates; no implicit trust within cluster
- **Traffic encryption without code changes:** Application-transparent security
- **Policy enforcement:** Centralized control over transport security policies

### Message Queue Security

**Secure message brokers (Kafka, RabbitMQ) used for model output delivery:**

**Kafka with TLS and SASL authentication:**

```properties
# Kafka broker configuration
listeners=SSL://0.0.0.0:9093
ssl.protocol=TLSv1.3
ssl.enabled.protocols=TLSv1.3
ssl.keystore.location=/var/private/ssl/kafka.server.keystore.jks
ssl.keystore.password=<password>
ssl.key.password=<password>
ssl.truststore.location=/var/private/ssl/kafka.server.truststore.jks
ssl.truststore.password=<password>
ssl.client.auth=required  # Require client certificates

# SASL authentication
security.inter.broker.protocol=SASL_SSL
sasl.mechanism.inter.broker.protocol=PLAIN
sasl.enabled.mechanisms=PLAIN
```

**Kafka producer (model service) configuration:**

```python
from kafka import KafkaProducer
import json

producer = KafkaProducer(
    bootstrap_servers='kafka.example.com:9093',
    security_protocol='SSL',
    ssl_cafile='/path/to/ca-cert',
    ssl_certfile='/path/to/client-cert.pem',
    ssl_keyfile='/path/to/client-key.pem',
    ssl_check_hostname=True,
    value_serializer=lambda v: json.dumps(v).encode('utf-8')
)

# Publish model prediction
prediction_message = {
    'model_version': 'v1.2.3',
    'prediction': prediction_result,
    'timestamp': datetime.now().isoformat()
}

producer.send('model-predictions', value=prediction_message)
```

**Kafka consumer (downstream application) configuration:**

```python
from kafka import KafkaConsumer

consumer = KafkaConsumer(
    'model-predictions',
    bootstrap_servers='kafka.example.com:9093',
    security_protocol='SSL',
    ssl_cafile='/path/to/ca-cert',
    ssl_certfile='/path/to/client-cert.pem',
    ssl_keyfile='/path/to/client-key.pem',
    auto_offset_reset='earliest',
    value_deserializer=lambda m: json.loads(m.decode('utf-8'))
)

for message in consumer:
    prediction = message.value
    # Process encrypted prediction message
    process_prediction(prediction)
```

### Certificate Validation

**Enforce strict certificate verification in consuming systems:**

```python
import requests
from requests.exceptions import SSLError

def secure_api_call(url, data):
    """Make API call with strict certificate validation"""
    try:
        response = requests.post(
            url,
            json=data,
            verify=True,  # Enforce certificate verification
            timeout=10
        )
        response.raise_for_status()
        return response.json()
    
    except SSLError as e:
        # Certificate validation failed - DO NOT proceed
        print(f"[SECURITY] SSL certificate validation failed: {e}")
        # Alert security team
        # Do not fall back to unencrypted connection
        raise
    
    except requests.exceptions.RequestException as e:
        print(f"[ERROR] Request failed: {e}")
        raise

# Usage
try:
    prediction = secure_api_call(
        'https://model-api.example.com/api/v1/predict',
        {'input': input_data}
    )
except SSLError:
    # Handle security failure (do not process prediction)
    pass
```

**Certificate pinning for critical endpoints:**

```python
import hashlib
import ssl
import requests

def pin_certificate(hostname, expected_fingerprint):
    """Validate server certificate fingerprint matches expected value"""
    
    # Get server certificate
    cert = ssl.get_server_certificate((hostname, 443))
    
    # Calculate fingerprint
    cert_der = ssl.PEM_cert_to_DER_cert(cert)
    fingerprint = hashlib.sha256(cert_der).hexdigest()
    
    if fingerprint != expected_fingerprint:
        raise SecurityError(
            f"Certificate fingerprint mismatch! "
            f"Expected: {expected_fingerprint}, Got: {fingerprint}"
        )

# Validate certificate before making requests
EXPECTED_FINGERPRINT = 'a1b2c3d4...'  # Known-good certificate fingerprint
pin_certificate('model-api.example.com', EXPECTED_FINGERPRINT)

# Proceed with API call (certificate validated)
response = requests.post('https://model-api.example.com/api/v1/predict', json=data)
```

## Limitations & Trade-offs

**Limitations:**

- **Certificate management overhead:** Requires infrastructure for certificate issuance, renewal, and revocation
- **Performance impact:** TLS encryption/decryption adds latency (typically 1-5ms)
- **Complexity:** mTLS and service mesh add deployment and operational complexity
- **Certificate expiration:** Expired certificates break service communication; requires monitoring and auto-renewal
- **Trust anchor compromise:** If CA private key compromised, entire PKI trust chain breaks

**Trade-offs:**

- **Security vs. Performance:** Stronger encryption (TLS 1.3, 4096-bit keys) more secure but slower
- **Security vs. Complexity:** mTLS and service mesh provide strong security but increase system complexity
- **Strict validation vs. Availability:** Strict certificate validation improves security but can cause outages if certificates expire

**Bypass Scenarios:**

- **Fallback to HTTP:** If HTTPS endpoints also support HTTP, attackers use unencrypted path
- **Certificate validation disabled:** If consuming systems skip certificate verification, encryption provides no authentication
- **MITM with trusted CA:** Attacker with access to trusted CA can issue fake certificates
- **Compromised private keys:** Stolen server/client private keys allow impersonation

## Testing & Validation

**Functional Testing:**

1. **TLS enforcement:** Verify all endpoints reject unencrypted connections
2. **mTLS authentication:** Confirm clients without valid certificates are rejected
3. **Certificate validation:** Verify consuming systems reject invalid/expired certificates
4. **Cipher suite enforcement:** Confirm only strong ciphers accepted
5. **HSTS enforcement:** Verify HTTP requests redirect to HTTPS

**Security Testing:**

1. **Downgrade attacks:** Attempt to force TLS 1.2 or lower (should fail)
2. **Invalid certificates:** Present self-signed or expired certificates (should be rejected)
3. **MITM simulation:** Attempt interception with proxy (should fail if certificate pinning used)
4. **Cipher suite weakness:** Attempt to negotiate weak ciphers (should be rejected)

**Monitoring:**

- Track TLS version distribution (alert if TLS 1.2 or lower seen)
- Monitor certificate expiration dates (auto-renew before expiry)
- Alert on certificate validation failures
- Track mTLS authentication failures

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "End-to-End Encryption: Enforce TLS 1.3+ for all model-to-application communication with mutual TLS (mTLS) for service-to-service authentication. Service Mesh: Deploy service mesh (Istio, Linkerd) enforcing mutual TLS, identity verification, and traffic encryption by default."
> — Derived from [[sources/bibliography#OWASP ML Security Top 10]], Output Integrity Attack mitigation guidance

## Related

- **Combined with:** [[mitigations/cryptographic-output-signing]], [[mitigations/output-consistency-monitoring]], [[mitigations/ai-api-security]]
- **Depends on:** PKI infrastructure, certificate management (Let's Encrypt, internal CA, cloud certificate services)
