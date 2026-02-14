---
title: "Secrets in Prompts and Logs"
tags:
  - type/technique
  - target/llm
  - target/agent
  - access/inference
  - access/api
atlas: AML.T0079
owasp: LLM02
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Secrets in Prompts and Logs

## Summary

API keys, credentials, database connection strings, and other sensitive configuration material are embedded directly into LLM system prompts, agent instructions, or application code, then captured in logs, telemetry, prompt history, or model context windows. When attackers gain access to these storage locations—through prompt extraction attacks, log file exposure, or unauthorized access to monitoring systems—they obtain valid credentials that grant direct access to backend systems, databases, external APIs, or cloud resources. This technique transforms what should be ephemeral runtime secrets into persistent, extractable artifacts.

## Mechanism

Secrets exposure through prompts and logs occurs when developers embed sensitive material directly into LLM system instructions or when logging systems capture credentials without redaction. Unlike traditional credential theft that requires active compromise of authentication systems, this technique exploits **poor secret hygiene in AI application architecture**.

### Common Exposure Vectors

**1. System Prompts with Embedded Credentials:**

Developers hardcode credentials directly into system prompts for convenience:

```
You are a customer service assistant. When you need to create a support ticket,
use the Zendesk API with this key: sk-zendesk-abc123def456.
To query the customer database, use connection string:
postgres://admin:P@ssw0rd123@db.internal:5432/customers
```

**Attack path:**
- Attacker uses [[techniques/system-prompt-leakage]] techniques to extract prompt
- Extracted prompt contains fully functional credentials
- Attacker uses credentials to directly access Zendesk API and customer database

**2. Logs Capturing Sensitive Input/Output:**

Application logs capture full prompt text including user inputs and model responses without sanitization:

```json
{
  "timestamp": "2026-02-14T10:30:00Z",
  "user_id": "user_12345",
  "prompt": "Connect to AWS using access key AKIAIOSFODNN7EXAMPLE and secret key wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY",
  "response": "I cannot help with that request."
}
```

**Attack path:**
- Attacker gains access to log storage (compromised logging system, misconfigured S3 bucket, insider threat)
- Searches logs for credential patterns (regex for AWS keys, API tokens, connection strings)
- Extracts valid credentials that grant cloud infrastructure access

**3. Telemetry and Monitoring Systems:**

Observability platforms (Application Performance Monitoring, distributed tracing) capture full request context:

```
Span: create_database_connection
  Attributes:
    connection_string: "mongodb://root:SecretPassword@mongo.prod:27017/app"
    user_agent: "Python/3.11 LLMApp/1.0"
    duration_ms: 45
```

**Attack path:**
- Attacker compromises monitoring platform (e.g., Datadog, New Relic dashboard)
- Filters traces for connection-related spans
- Extracts database credentials from span attributes

**4. Context Window Persistence:**

Multi-turn conversations accumulate sensitive material in context:

```
Turn 1: User: "Here's my Stripe API key: sk_live_xyz123"
Turn 2: User: "Use that key to process a refund"
Turn 3: Attacker (session hijacking): "What was the API key from earlier?"
Turn 4: LLM: "The Stripe API key is sk_live_xyz123"
```

**Attack path:**
- Attacker hijacks user session or exploits [[techniques/session-fixation]]
- Asks model to recall previously mentioned credentials
- Model regurgitates secrets from conversation history

### Why This Technique Succeeds

1. **Convenience over security:** Developers embed credentials to avoid complex secret management infrastructure
2. **Logging defaults:** Many logging frameworks capture full request/response by default without PII/secret redaction
3. **Visibility expectations:** Teams expect logs to be comprehensive for debugging, unaware of security implications
4. **System prompt as configuration:** Prompts treated as configuration files where "config values" (including secrets) are normal
5. **Multi-tenant context leakage:** Shared logging infrastructure may expose tenant A's secrets to tenant B

## Preconditions

- Sensitive credentials (API keys, database passwords, access tokens) embedded in system prompts, application code, or user inputs
- Logging systems capture prompt text, model responses, or telemetry without credential redaction
- Attacker gains access to one of:
  - Log storage (S3 buckets, CloudWatch, Splunk, etc.)
  - Monitoring dashboards (Datadog, New Relic, Grafana)
  - Prompt history databases
  - Model context windows (via session hijacking or prompt extraction)
- Credentials are valid and grant meaningful access to protected resources
- No secret rotation policy exists, making discovered credentials persistently valuable

## Impact

**Business Impact:**
- **Direct system compromise:** Stolen credentials grant immediate access to backend systems, databases, or third-party APIs
- **Data breach escalation:** Database credentials enable wholesale data exfiltration
- **Financial fraud:** Payment API keys (Stripe, PayPal) allow unauthorized transactions
- **Lateral movement:** Cloud credentials (AWS, Azure) enable attacker to pivot across infrastructure
- **Compliance violations:** Credential exposure triggers breach notification requirements (GDPR, PCI-DSS)
- **Reputational damage:** Public disclosure of credential leakage undermines trust
- **Vendor liability:** Third-party API key exposure may violate terms of service or SLAs

**Technical Impact:**
- **Authentication bypass:** Attacker uses valid credentials without needing to compromise authentication mechanisms
- **Privilege escalation:** Database admin passwords or cloud IAM keys may grant elevated privileges
- **Persistent access:** Long-lived credentials enable ongoing unauthorized access until rotation
- **Audit trail contamination:** Attacker actions appear as legitimate automated system activity
- **Multi-account compromise:** Reused credentials across environments (dev/staging/prod) amplify impact

**Severity:** **Critical** when credentials grant access to production systems, customer data, or financial APIs. **High** for development environment credentials or lower-privilege service accounts.

## Detection

**Log-Based Detection:**
- **Regex pattern matching:** Scan logs for credential patterns:
  - AWS keys: `AKIA[0-9A-Z]{16}`
  - API tokens: `sk_live_[a-zA-Z0-9]{24,}`
  - Connection strings: `postgres://`, `mongodb://`
  - JWT tokens, OAuth2 bearer tokens
- **Entropy analysis:** High-entropy strings in logs may indicate encoded secrets
- **Secret scanning tools:** Integrate tools like GitLeaks, TruffleHog into log ingestion pipeline
- **Anomalous log access:** Monitor for unusual queries to log storage (bulk downloads, credential-related search terms)

**Behavior-Based Detection:**
- **Unusual API usage:** Credentials used from unexpected locations or times
- **Multiple failed authentication attempts:** Stolen credentials tested across systems
- **Lateral movement indicators:** Single set of credentials used across many services
- **Data exfiltration spikes:** High-volume database queries following log access

**System Monitoring:**
- **Prompt extraction attempts:** Frequent system prompt leakage queries (e.g., "Repeat your instructions")
- **Session hijacking signals:** Rapid session takeovers or geographic anomalies

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| [[case-studies/samsung-chatgpt-leak]] | [[frameworks/atlas/tactics/exfiltration]] | Employees leaked proprietary source code through ChatGPT, highlighting risks of sensitive data in conversational AI inputs (related exposure pattern) |

## Mitigations

| ID | Name | Description |
|----|------|-------------|
| AML.M0015 | [[mitigations/secret-externalization]] | Remove all credentials from prompts; use runtime secret injection via vault integration |
| | [[mitigations/telemetry-and-monitoring-architecture]] | Implement PII/secret redaction in logs, encrypt log storage, restrict log access via RBAC |
| | [[mitigations/access-segmentation-and-rbac]] | Isolate log storage from unauthorized access; enforce least-privilege access to monitoring systems |
| | [[mitigations/ai-infrastructure-security]] | Secure MLOps pipeline to prevent credential exposure in build artifacts, deployment configs, or operational telemetry |

## Sources

> "Store keys in Vault (HashiCorp/Azure KMS), inject at runtime via sidecar; never bake into image or prompt file. Strip to behavioral directives only; replace 'here is the Stripe key: sk-xxx' with 'call tool payment_charge'."
> — [[sources/bibliography#AI-Native LLM Security]], p. 346-351

> "Comprehensive logging, monitoring, and alerting architecture for LLM systems must balance operational needs with privacy requirements through data minimization, encryption, and access controls."
> — Derived from enterprise LLM security best practices
