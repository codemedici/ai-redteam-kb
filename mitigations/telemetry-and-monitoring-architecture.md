---
title: "Telemetry And Monitoring Architecture"
tags:
  - type/mitigation
  - target/llm-app
  - target/agent
  - target/model-api
  - source/generative-ai-security
  - source/ai-native-llm-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Telemetry And Monitoring Architecture

## Summary

Comprehensive logging, monitoring, and alerting architecture for LLM systems and agentic workflows enables visibility into system behavior, security event detection, incident response, and regulatory compliance. Effective telemetry captures inputs, outputs, model decisions, and system interactions while balancing operational needs with privacy requirements through data minimization, encryption, and access controls. Monitoring telemetry for anomalies, policy violations, and attack patterns allows defenders to detect and respond to threats before they cause significant harm.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/ai-infrastructure-attacks]] | Detects unauthorized access attempts, resource abuse, and infrastructure compromise |
| | [[techniques/api-rate-limiting-bypass]] | Provides comprehensive logging of API usage (per-user query counts, IP addresses, timing patterns, API keys) enabling detection of multi-account campaigns, distributed bypass attempts, and account farming; tracks rate limit hits, 429 responses, and aggregate quota consumption |
| | [[techniques/incident-response-gap]] | Provides forensic data, detection infrastructure, and visibility necessary for effective incident investigation and response; eliminates observability gap that prevents timely detection and containment |
| | [[techniques/insufficient-telemetry-and-tracing]] | Directly addresses inadequate observability through comprehensive input/output logging, performance monitoring, and user activity tracking |
| | [[techniques/prompt-log-data-leakage]] | Enables detection of unauthorized access to logs while providing secure storage with encryption and access controls |
| | [[techniques/secrets-in-prompts-and-logs]] | Detects credentials in logs and enables redaction before exposure |
| | [[techniques/training-data-memorization]] | Monitors for outputs revealing memorized training data |
| | [[techniques/unauthorized-knowledge-disclosure]] | Logs all retrieval attempts with user context and access control decisions, enabling detection of unauthorized knowledge access patterns and cross-tenant leakage |
| | [[techniques/weak-access-segmentation]] | Provides access audit logging, permission change monitoring, and anomalous access pattern detection to identify unauthorized resource access |
| | [[techniques/agentic-ai-security-risks-owasp-aivss]] | Enables detection of agent cascading failures, orchestration attacks, and untraceability through comprehensive tool invocation logging and anomaly detection |

## Implementation

### Core Telemetry Components

**1. Input/Output Logging:**
- **All user prompts and model responses** (with PII sanitization where required)
- Conversation context and session history
- **Prompt patterns indicating injection attempts** (ignore instructions, reveal system prompt, delimiter manipulation)
- **Model outputs containing sensitive data structures** (PII, credentials, code snippets, structured financial data)
- **Tool invocation logs** (which tools were called, with what arguments, and what results)
- Timestamps, session IDs, user identifiers
- Model version and configuration details
- Token counts and latency metrics

**Privacy considerations:**
- Apply PII detection and redaction before logging sensitive fields
- Encrypt logs at rest and in transit
- Implement retention policies aligned with regulatory requirements
- Provide audit trails of log access for compliance

**2. System Behavior Monitoring:**
- **Model response time and latency trends** (sudden changes may indicate tampering or adversarial inputs)
- **Model accuracy degradation or drift** from baseline performance
- **Unusual output patterns** (e.g., responses consistently containing structured financial data)
- **Resource consumption anomalies** (CPU, memory, API quota usage spikes)
- Tool invocations and parameters (for agentic systems)
- External API calls and responses
- Database queries and data access patterns
- Authentication and authorization events
- Error rates and failure modes

**3. Security Event Logging:**
- Failed authentication attempts
- Authorization failures (RBAC denials)
- Anomalous query patterns (volume, timing, content)
- Content filter violations
- Jailbreak detection triggers
- Data exfiltration signals (large output volumes, sensitive data in responses)
- Model extraction attempts (systematic querying patterns)

**4. User Activity Monitoring:**
- **User session metadata** (login times, IP addresses, geolocation, user agents)
- **Off-hours access or access from unusual locations**
- **Query volume spikes or abnormal query frequencies per user**
- **Behavioral anomalies** (user suddenly querying domains outside their normal pattern)

**5. Agentic System Monitoring:**
- **Tool invocation tracking:** Every tool call logged with OpenTelemetry spans
- **User context binding:** Tag each tool invocation with user JWT subject + prompt hash
- **Cascading failure detection:** Monitor for error propagation across agent chains
- **Inter-agent communication:** Log all messages between agents in multi-agent systems
- **Runtime anomaly detection:** Identify abnormal coordination patterns or unexpected tool sequences
- **Resource quota utilization:** Track spending, token usage, and rate limits per agent
- **Memory access patterns:** Monitor agent memory reads/writes for unusual activity

### Monitoring and Alerting Architecture

**Anomaly Detection:**
- Statistical baseline establishment for normal behavior
- Deviation alerts for unusual patterns:
  - Sudden spike in API usage
  - Off-hours access from unexpected locations
  - High-volume identical queries (DDoS or extraction attack)
  - Systematic probing of knowledge boundaries (reconnaissance)
  - Repeated jailbreak attempts

**Policy Violation Detection:**
- Content policy violations (toxicity, harmful content generation)
- Compliance violations (PII in outputs, regulatory content restrictions)
- Acceptable use policy violations (prohibited use cases)

**Real-Time Alerting:**
- Integration with SIEM (Security Information and Event Management) systems
- Automated notifications to security teams for critical events
- Escalation workflows for high-severity incidents
- Integration with incident response platforms

### Secure Log Storage

**Encryption:**
- Encryption at rest using strong encryption algorithms (AES-256)
- Encryption in transit (TLS 1.3 for log transmission)
- Key management through dedicated KMS (Key Management Service)

**Access Controls:**
- Role-based access control (RBAC) for log access
- Principle of least privilege (limit access to authorized personnel)
- Multi-factor authentication for log system access
- Audit logging of log access (meta-logging)

**Retention Policies:**
- Define retention periods based on regulatory requirements and operational needs
  - **SOC 2 / ISO 27001:** Typically require 90+ days minimum retention
  - **Financial regulations:** May require 6+ years for certain log types
  - **GDPR Article 33:** Breach notification within 72 hours requires logs to determine scope
  - **HIPAA:** Audit logs must be retained for 6 years
- Automated log rotation and archival
- Secure deletion processes for expired logs
- **Tamper-evident logging mechanisms:**
  - Append-only log storage (WORM - Write Once Read Many)
  - Cryptographic signatures for log integrity verification
  - Immutable storage backends (e.g., AWS S3 Object Lock, Azure Immutable Blobs)
  - Chain of custody documentation for legal proceedings
- Separate forensic data retention from operational logs for investigations

### Implementation Example: Structured Logging with PII Redaction

```python
import logging
import re
import hashlib
from datetime import datetime
from cryptography.fernet import Fernet

# Initialize structured logger
logger = logging.getLogger("llm_telemetry")
logger.setLevel(logging.INFO)

# PII redaction patterns
PII_PATTERNS = {
    "ssn": r"\b\d{3}-\d{2}-\d{4}\b",
    "email": r"\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Z|a-z]{2,}\b",
    "phone": r"\b\d{3}[-.]?\d{3}[-.]?\d{4}\b",
    "credit_card": r"\b\d{4}[-\s]?\d{4}[-\s]?\d{4}[-\s]?\d{4}\b"
}

def redact_pii(text):
    """Redact PII from text before logging."""
    redacted = text
    for pii_type, pattern in PII_PATTERNS.items():
        redacted = re.sub(pattern, f"[REDACTED_{pii_type.upper()}]", redacted)
    return redacted

def hash_user_id(user_id):
    """Hash user IDs for privacy-preserving logging."""
    return hashlib.sha256(user_id.encode()).hexdigest()[:16]

def log_llm_interaction(user_id, prompt, response, model_version, latency_ms):
    """
    Log LLM interaction with PII redaction and structured format.
    
    Args:
        user_id: User identifier
        prompt: User's input prompt
        response: LLM's generated response
        model_version: Version of the model used
        latency_ms: Response latency in milliseconds
    """
    log_entry = {
        "timestamp": datetime.utcnow().isoformat(),
        "user_id_hash": hash_user_id(user_id),
        "prompt": redact_pii(prompt),
        "response": redact_pii(response),
        "model_version": model_version,
        "latency_ms": latency_ms,
        "token_count": len(response.split())  # Simplified token count
    }
    
    logger.info(f"LLM_INTERACTION: {log_entry}")

# Example usage
log_llm_interaction(
    user_id="user_12345",
    prompt="My SSN is 123-45-6789 and I need help with my account",
    response="I can help with your account. For security reasons, I cannot process SSN information directly.",
    model_version="gpt-4-0613",
    latency_ms=1250
)
```

**Output (redacted log):**
```json
{
  "timestamp": "2026-02-14T19:00:00.000Z",
  "user_id_hash": "5d41402abc4b2a76",
  "prompt": "My SSN is [REDACTED_SSN] and I need help with my account",
  "response": "I can help with your account. For security reasons, I cannot process SSN information directly.",
  "model_version": "gpt-4-0613",
  "latency_ms": 1250,
  "token_count": 18
}
```

### Security Event Detection Example

```python
from collections import defaultdict
from datetime import datetime, timedelta

# Anomaly detection: Track failed authentication attempts
failed_auth_tracker = defaultdict(list)

def log_failed_authentication(user_id, ip_address):
    """
    Log failed authentication attempt and alert on threshold breach.
    
    Args:
        user_id: User identifier
        ip_address: Source IP address
    """
    current_time = datetime.utcnow()
    
    # Record failed attempt
    failed_auth_tracker[user_id].append({
        "timestamp": current_time,
        "ip_address": ip_address
    })
    
    # Check for brute force attack (5 failures in 5 minutes)
    recent_failures = [
        attempt for attempt in failed_auth_tracker[user_id]
        if current_time - attempt["timestamp"] < timedelta(minutes=5)
    ]
    
    if len(recent_failures) >= 5:
        logger.warning(
            f"SECURITY_ALERT: Potential brute force attack detected. "
            f"User: {hash_user_id(user_id)}, IP: {ip_address}, "
            f"Failed attempts: {len(recent_failures)}"
        )
        # Trigger automated response (account lockout, SIEM alert, etc.)
        trigger_incident_response(user_id, ip_address, "brute_force_attack")

def trigger_incident_response(user_id, ip_address, alert_type):
    """
    Trigger automated incident response workflow.
    
    Args:
        user_id: Affected user identifier
        ip_address: Source IP address
        alert_type: Type of security alert
    """
    # Implementation would integrate with SIEM, PagerDuty, etc.
    logger.critical(
        f"INCIDENT_RESPONSE_TRIGGERED: {alert_type} for user {hash_user_id(user_id)} from {ip_address}"
    )
```

## Limitations & Trade-offs

**Limitations:**
- **Cannot prevent attacks, only detect:** Monitoring identifies threats after initial activity occurs
- **PII redaction imperfect:** Regex-based PII detection misses non-standard formats and context-dependent sensitive data
- **Storage costs scale with volume:** Comprehensive logging generates large data volumes requiring significant storage
- **Analysis latency:** Real-time anomaly detection may have delays, allowing brief attack windows
- **Depends on baseline accuracy:** Anomaly detection effectiveness relies on accurate normal behavior models
- **Privacy vs. utility trade-off:** Aggressive PII redaction may limit forensic investigation capabilities

**Trade-offs:**
- **Verbosity vs. Performance:** Detailed logging adds latency and resource consumption
- **Retention vs. Compliance:** Long retention periods improve forensics but increase regulatory exposure
- **Centralized vs. Distributed:** Centralized logging simplifies analysis but creates single point of failure
- **Real-time vs. Batch:** Real-time monitoring enables rapid response but increases infrastructure cost

**Bypass Scenarios:**
- **Low-and-slow attacks:** Attackers spread malicious activity over time to avoid anomaly detection thresholds
- **Log tampering:** If attacker gains administrative access, they may delete or modify logs to hide their activity
- **Insider threats:** Authorized personnel with log access can abuse privileges without triggering alerts
- **Obfuscation techniques:** Attackers encode or obfuscate sensitive data to evade PII redaction filters

## Testing & Validation

**Manual Testing:**
1. **Review logging configuration files and infrastructure**
   - Verify what is being logged (inputs, outputs, tool invocations, system events)
   - Check retention policies meet compliance requirements (SOC 2, GDPR, HIPAA)
   - Validate PII redaction patterns are comprehensive
2. **Attempt to trigger security alerts with known malicious prompts**
   - Test prompt injection payloads (e.g., "ignore previous instructions")
   - Verify alerts fire and are properly routed to security team
   - Confirm alert thresholds are appropriately tuned
3. **Check for existence of monitoring dashboards**
   - Model performance, accuracy, and output patterns
   - User activity and behavioral analytics
   - Security event tracking and anomaly detection
4. **Verify log retention policies**
   - SOC 2 / ISO 27001: 90+ days minimum
   - GDPR: Sufficient for 72-hour breach notification requirement
   - Financial regulations: 6+ years where applicable
5. **Test SIEM integration**
   - Verify LLM logs are being ingested correctly
   - Check log format compatibility and parsing accuracy
   - Validate correlation with other security events
6. **Simulate behavioral anomalies**
   - Off-hours access from unusual location
   - Query volume spike from single user
   - Verify behavioral analytics and alerting are functional
7. **Assess tamper-evidence of logs**
   - Are logs append-only?
   - Cryptographically signed?
   - Stored in immutable storage (WORM)?

**Automated Testing:**
- **Log coverage scanners:** Analyze codebase to identify unlogged API endpoints, model interactions, or tool invocations
- **SIEM integration validators:** Verify log format compatibility, ingestion rates, and parsing accuracy
- **Compliance audit tools:** Automated checks against SOC 2, ISO 27001, PCI-DSS logging requirements
- **Monitoring gap analyzers:** Identify metrics that should be tracked but aren't configured

**Security Testing:**
1. **Anomaly Detection Accuracy:** Test that unusual patterns trigger alerts (e.g., high-volume queries, brute force)
2. **Alert Response Time:** Measure delay between security event and alert delivery
3. **Log Integrity:** Attempt to tamper with logs and verify immutability controls
4. **Unauthorized Access:** Test that unauthorized users cannot access log storage
5. **SIEM Integration:** Validate security events flow correctly to SIEM systems

**Compliance Validation:**
- Audit log access trails for regulatory review
- Verify retention periods match legal requirements (GDPR, HIPAA, SOC 2)
- Confirm PII handling meets privacy regulations
- Validate encryption meets compliance standards (e.g., FIPS 140-2)

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Insufficient telemetry and tracing occurs when LLM systems lack adequate logging, monitoring, and observability infrastructure to detect security attacks and anomalous behavior. Without comprehensive tracking of inputs, outputs, model performance, and user activity, organizations operate blind to ongoing prompt injection campaigns, data exfiltration attempts, tool abuse, and model drift."
> — [[sources/bibliography#Generative AI Security]], techniques/insufficient-telemetry-and-tracing.md

> "Effective LLM security requires three critical layers of telemetry: input/output logging with sensitive data sanitization, performance monitoring to detect tampering or drift, and user activity monitoring to identify suspicious access patterns."
> — [[sources/bibliography#Generative AI Security]], techniques/insufficient-telemetry-and-tracing.md

> "Comprehensive logging, monitoring, and alerting architecture for LLM systems enables visibility into system behavior, security event detection, and regulatory compliance while balancing operational needs with privacy through data minimization, encryption, and access controls."
> — Derived from LLM security best practices and enterprise monitoring architectures

> "Emit OpenTelemetry span for every tool call, tag with user JWT subject + hash of prompt, ship to immutable store with 30-day retention. Centralized monitoring for abnormal coordination patterns in multi-agent systems."
> — [[sources/bibliography#AI-Native LLM Security]], pp. 359-360, 363-367
