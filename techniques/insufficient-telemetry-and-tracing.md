---
description: "Lack of adequate logging and monitoring prevents detection of attacks and anomalies."
tags:
  - trust-boundary/deployment-governance
  - type/attack
  - target/llm-app
  - target/agent
  - target/ml-pipeline
  - access/black-box
  - severity/medium
---
# Insufficient Telemetry and Tracing

## Summary

Insufficient telemetry and tracing occurs when LLM systems lack adequate logging, monitoring, and observability infrastructure to detect security attacks and anomalous behavior. Without comprehensive tracking of inputs, outputs, model performance, and user activity, organizations operate blind to ongoing prompt injection campaigns, data exfiltration attempts, tool abuse, and model drift. This gap transforms all other vulnerabilities from detectable incidents into silent breaches that can persist for weeks or months. Effective LLM security requires three critical layers of telemetry: input/output logging with sensitive data sanitization, performance monitoring to detect tampering or drift, and user activity monitoring to identify suspicious access patterns.

## Threat Scenario

A financial services company deploys a customer service LLM with minimal logging—only basic API access logs and error logs are retained. An attacker discovers a prompt injection vulnerability and begins a multi-week campaign to exfiltrate customer financial data. They craft prompts that cause the LLM to include sensitive information (account numbers, balances, transaction histories) in its responses. Because there is no input/output logging, the malicious prompts go unrecorded. Because there is no performance monitoring, the unusual pattern of responses containing structured financial data is never flagged. Because there is no user activity monitoring, the attacker's off-hours access and abnormal query volume raise no alerts. The breach continues for six weeks until a customer notices unauthorized account access. During forensic investigation, the security team discovers they have no audit trail—no logged prompts, no response content, no behavioral analytics. They cannot determine the scope of the breach, which data was exfiltrated, or how the attack was executed. The lack of telemetry transforms a detectable attack into an uncontrolled data breach with unknown impact.

## Preconditions

- LLM system lacks comprehensive input/output logging (prompts and responses not retained or only sampled)
- No real-time performance monitoring dashboards tracking accuracy, response time, or output patterns
- Absence of user activity monitoring or behavioral analytics
- No alerting rules configured for AI-specific threat indicators (prompt injection patterns, data exfiltration, tool abuse)
- Logs (if any exist) are not integrated with SIEM/SOAR platforms for correlation and threat detection
- No tamper-evident logging mechanisms, allowing attackers to potentially delete evidence if they gain access

## Abuse Path

1. **Reconnaissance Without Detection**: Attacker probes the LLM system with unusual prompts to identify vulnerabilities (prompt injection, sensitive data leakage, tool abuse). Because input/output logging is absent, these reconnaissance attempts go unrecorded and generate no alerts.

2. **Exploit Development and Testing**: Attacker refines their attack technique through iterative testing, experimenting with different prompt injection payloads or data exfiltration methods. Performance monitoring gaps mean that unusual model behavior (e.g., responses containing structured sensitive data) is never flagged as anomalous.

3. **Sustained Attack Execution**: Attacker launches persistent exploitation campaign over days or weeks, extracting data, manipulating model outputs, or abusing tool invocations. User activity monitoring gaps allow off-hours access, abnormal query volumes, and suspicious session patterns to proceed undetected.

4. **Evidence Destruction and Persistence**: In cases where minimal logs exist, attacker may attempt to delete or tamper with evidence if they gain system access. Lack of tamper-evident logging and SIEM integration means the attack continues indefinitely until discovered through external means (user complaints, third-party breach notification, or accidental discovery).

## Impact

**Business Impact:**
- Undetected security breaches with prolonged exposure (attacks may continue for weeks or months before discovery)
- Inability to determine scope of data breach for regulatory reporting (GDPR Article 33 requires breach notification within 72 hours, but without logs, scope is unknown)
- Failed compliance audits (SOC 2, ISO 27001, PCI-DSS, HIPAA all require comprehensive audit logging)
- Ineffective incident response due to lack of forensic evidence (cannot determine what was accessed, when, or by whom)
- Reputational damage when breach scope cannot be accurately communicated to customers or regulators
- Potential regulatory fines for inadequate security controls and logging failures

**Technical Impact:**
- Complete blind spots in security monitoring—no visibility into attack patterns, techniques, or indicators of compromise
- Inability to detect model drift, performance degradation, or tampering in production
- No forensic evidence for root cause analysis or attribution
- Cannot establish timeline of attack events or identify all compromised data
- No baseline for anomaly detection—cannot distinguish normal from malicious behavior patterns

**Severity:** **High** (enables all other attacks to go undetected; foundational security control failure)

## Detection Signals

*Note: Ironically, insufficient telemetry means these signals are NOT being captured. This section describes what SHOULD be logged to detect this gap and enable detection of attacks.*

**What Should Be Logged (Input/Output Layer):**
- All user prompts and model responses (with PII sanitization where required)
- Prompt patterns indicating injection attempts (ignore instructions, reveal system prompt, etc.)
- Model outputs containing sensitive data structures (PII, credentials, code snippets)
- Tool invocation logs (which tools were called, with what arguments, and what results)

**What Should Be Monitored (Performance Layer):**
- Model response time and latency trends (sudden changes may indicate tampering or adversarial inputs)
- Model accuracy degradation or drift from baseline performance
- Unusual output patterns (e.g., responses consistently containing structured financial data)
- Resource consumption anomalies (CPU, memory, API quota usage spikes)

**What Should Be Tracked (User Activity Layer):**
- User session metadata (login times, IP addresses, geolocation, user agents)
- Off-hours access or access from unusual locations
- Query volume spikes or abnormal query frequencies per user
- Behavioral anomalies (user suddenly querying domains outside their normal pattern)

## Testing Approach

**Manual Testing:**
- Review logging configuration files and infrastructure (verify what is being logged and retention policies)
- Attempt to trigger security alerts with known malicious prompts (e.g., prompt injection payloads) and verify if alerts fire
- Check for existence of monitoring dashboards tracking model performance, accuracy, and output patterns
- Verify log retention policies meet compliance requirements (e.g., GDPR, SOC 2 typically require 90+ days)
- Test SIEM integration—verify that LLM logs are being ingested and correlated with other security events
- Simulate off-hours access or query volume spike to test if behavioral analytics and alerting are functional
- Assess tamper-evidence of logs (are they append-only, cryptographically signed, or stored in immutable storage?)

**Automated Testing:**
- Log coverage scanners (analyze codebase to identify unlogged API endpoints, model interactions, or tool invocations)
- SIEM integration validators (verify log format compatibility, ingestion rates, and parsing accuracy)
- Compliance audit tools (automated checks against SOC 2, ISO 27001, PCI-DSS logging requirements)
- Monitoring gap analyzers (identify metrics that should be tracked but aren't configured)

## Evidence to Capture

- [ ] Gap analysis report documenting missing telemetry (input/output logging, performance monitoring, user activity tracking)
- [ ] Screenshots showing absence of monitoring dashboards or empty/non-existent dashboards
- [ ] Logging configuration files showing inadequate log retention or missing log categories
- [ ] Documentation of missing alerting rules (no alerts configured for prompt injection, data exfiltration, etc.)
- [ ] Evidence that malicious prompts did not trigger alerts (test payload execution logs showing no detection)
- [ ] SIEM integration status (not configured, or LLM logs not being ingested)
- [ ] Compliance gap documentation (failure to meet SOC 2, ISO 27001, GDPR, or HIPAA logging requirements)
- [ ] Evidence of log tampering possibility (logs not stored in tamper-evident or append-only format)

## Mitigations

**Preventive Controls:**
- Implement comprehensive input/output logging pipeline (capture all prompts, responses, tool invocations with timestamps and user context)
- Apply PII sanitization to logs where required (hash or redact sensitive data while preserving security utility)
- Deploy tamper-evident logging infrastructure (append-only logs, cryptographic signatures, or immutable storage like WORM)
- Establish log retention policies meeting compliance requirements (90+ days for SOC 2, 6+ years for financial regulations)
- Configure secure log storage with encryption at rest and in transit
- Implement structured logging formats (JSON, CEF) for easy SIEM ingestion and parsing

**Detective Controls:**
- Deploy real-time anomaly detection systems analyzing prompt patterns for injection attempts
- Implement performance baseline monitoring (track response time, accuracy, output patterns; alert on drift)
- Configure user behavior analytics (UBA) to detect off-hours access, query volume spikes, unusual geolocation)
- Integrate LLM logs with SIEM/SOAR platforms for correlation with other security events
- Set up automated alerting rules for AI-specific threats (prompt injection patterns, tool abuse, data exfiltration indicators)
- Deploy continuous model evaluation against benchmark datasets to detect tampering or drift
- Implement threat intelligence feeds specific to LLM attack patterns

**Responsive Controls:**
- Create incident response runbooks for LLM-specific security events (prompt injection detected, model drift, data exfiltration)
- Establish automated response playbooks (e.g., account suspension on detection of injection attempts)
- Configure escalation paths for critical alerts (security team notification, executive escalation)
- Implement forensic data retention separate from operational logs (immutable archive for investigations)
- Maintain chain of custody documentation for log evidence in case of legal proceedings

## Engagement Applicability

- [x] AI Threat Exposure Review (Deployment/Governance boundary assessment, monitoring gap analysis)
- [x] Continuous Monitoring Setup (directly addresses this vulnerability by implementing telemetry infrastructure)
- [x] Purple Team Workshop (teaches internal teams to build detection rules and monitoring dashboards)
- [x] Governance & Compliance Deep Dive (validates logging against SOC 2, ISO 27001, GDPR requirements)

## Framework References

**MITRE ATLAS:**
- **TODO:** Identify applicable ATLAS techniques

**OWASP LLM Top 10:**
- **TODO:** Map to OWASP LLM issues

**NIST AI RMF:**
- **TODO:** Map to NIST AI RMF

**NIST GenAI Profile:**
- **TODO:** Map to GenAI Profile

## Related

- **Mitigated by**: [[mitigations/telemetry-and-monitoring-architecture]], [[mitigations/anomaly-detection-architecture]], [[mitigations/llm-operational-resilience-monitoring]]
