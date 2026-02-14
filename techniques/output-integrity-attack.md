---
title: "Output Integrity Attack"
tags:
  - type/technique
  - target/llm-app
  - target/agent
  - target/ml-model
  - access/inference
  - access/api
atlas: AML.T0051
owasp: LLM09
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

## Summary

Output integrity attacks target the infrastructure layer between model inference and downstream processing, rather than the model itself. Attackers intercept model outputs and modify them before consuming systems act on the results, creating a disconnect between what the model actually predicted and what downstream systems observe. This attack is particularly insidious because the model appears to function normally when inspected directly—the compromise occurs in transit or at integration boundaries. Unlike adversarial attacks that manipulate inputs or data poisoning that corrupts training, output integrity attacks exploit weak verification of model outputs in system pipelines. The vulnerability exists when consuming systems implicitly trust model outputs without cryptographic verification, integrity checking, or secure transport.


## Mechanism

### Attack Surface

Output integrity attacks exploit vulnerable integration points in the ML inference pipeline:

**Vulnerable Channels:**
- Unencrypted or weakly encrypted API calls between model service and consuming applications
- Insecure message queues (Kafka, RabbitMQ, SQS) without authentication or encryption
- Shared memory or inter-process communication channels lacking integrity verification
- API gateways and middleware components with weak access controls
- Network traffic without TLS/mTLS protection

**Attack Vectors:**

1. **Man-in-the-Middle (MITM):**
   - Attacker positions themselves on network path between model service and consumer
   - Intercepts API responses or message queue payloads
   - Modifies prediction values, confidence scores, or generated text before forwarding

2. **Compromised Middleware:**
   - Exploitation of vulnerabilities in API gateways, load balancers, or message brokers
   - Malicious code injected into integration layer through supply chain compromise
   - Insider threat with legitimate access to application infrastructure

3. **API Gateway Exploitation:**
   - Weak authentication allows unauthorized modification of proxied responses
   - Missing output signing enables tampering without detection
   - Lack of end-to-end integrity verification creates trust gap

### Threat Scenarios

**Malware Classifier Bypass:**

An enterprise deploys an ML-based malware detection system where binaries are scanned, classified by the model, and automatically deleted if labeled as malicious. An attacker gains access to the application layer between the model inference service and the file management system—either through a compromised middleware component, insider access, or exploitation of an API gateway vulnerability. When the attacker uploads their custom malware, the model correctly classifies it as malicious with high confidence. However, the attacker's malicious code intercepts the model's output before it reaches the file management system, replacing "malicious" with "benign" in the classification result. The file management system receives the forged output, believes the model deemed the file safe, and preserves the malware on disk. The attack succeeds without manipulating the model or triggering any model-level anomaly detection, leaving security teams unaware the model's correct decision was overridden at the integration layer.

**Fraud Detection Manipulation:**

A financial institution uses an LLM-based fraud detection system to screen high-value transactions. An adversary compromises the message queue or API gateway between the model service and the transaction processing system. When a fraudulent transaction is submitted, the model correctly flags it with a fraud risk score of 0.95. The attacker's malicious middleware intercepts this API response and rewrites the risk score to 0.05 (low risk) before forwarding it to the transaction processing system. The transaction is approved and funds are transferred, while audit logs show the model produced a low-risk score. Forensic analysis of the model itself reveals no compromise—the attack occurred purely at the integration boundary.

**Autonomous Vehicle Safety Override:**

A self-driving vehicle uses an ML-based perception system to classify road signs and obstacles. An attacker exploits a vulnerability in the vehicle's embedded software architecture, gaining access to the inter-process communication channel between the perception model and the vehicle control system. When the model correctly identifies a stop sign, the attacker's malicious code intercepts the classification result and changes it to "yield sign" before it reaches the control system. The vehicle fails to stop at the intersection despite the model functioning correctly, creating a safety hazard that evades model-based testing and validation.


## Preconditions

- **Weak Output Integrity Verification:** Consuming systems lack cryptographic signature validation, checksums, or authentication of model outputs
- **Vulnerable Integration Points:** Insecure APIs, message queues, shared memory, or unencrypted communication channels between model and consuming systems
- **Infrastructure Access:** Attacker has compromised middleware, API gateway, network path, or has insider access to application layer
- **Lack of Secure Transport:** Missing or improperly configured TLS/mTLS for model-to-application communication
- **Insufficient Access Controls:** Weak authorization on inter-service communication channels
- **Absent Behavioral Monitoring:** No correlation analysis between model predictions and actual system actions
- **Implicit Trust Assumption:** Downstream systems unconditionally trust received outputs without validation


## Impact

**Business Impact:**

- **Security Control Bypass:** Model-based defenses (malware detection, fraud prevention, content moderation) systematically defeated without triggering model-level alerts
- **Financial Fraud:** Fraudulent transactions approved, payment fraud succeeding, or unauthorized fund transfers enabled
- **Regulatory Violations:** Compliance failures when security or risk models are compromised (PCI DSS, SOC 2, financial regulations)
- **Safety Incidents:** Physical harm in safety-critical systems (autonomous vehicles, medical devices, industrial control) when model safety predictions are overridden
- **Reputational Damage:** Loss of customer trust when model-driven decisions are revealed to be manipulated
- **Liability Exposure:** Legal liability for harm caused by tampered outputs, especially in regulated or safety-critical domains
- **Detection Difficulty:** Model appears functional during testing, making root cause analysis challenging and expensive

**Technical Impact:**

- **Trust Boundary Violation:** Implicit trust between model and consuming systems exploited as single point of failure
- **Model-Level Defenses Ineffective:** Adversarial robustness, input validation, and model monitoring cannot detect post-output tampering
- **Audit Trail Corruption:** Logs may show forged outputs rather than genuine model predictions, complicating forensics
- **Persistent Compromise:** Attacker maintains access to intercept future outputs until infrastructure compromise is discovered and remediated

**Severity:** **High** to **Critical** (Critical for safety/security systems where output tampering causes immediate harm; High for systems with strong compensating controls)


## Detection

**Output Divergence Indicators:**

- **Model Output vs. System Action Divergence:** Discrepancies between logged model predictions and actual system behavior (e.g., model logs show "malicious" but file was not deleted)
- **Integrity Check Failures:** Cryptographic signature verification failures on model outputs (if implemented)
- **Behavioral Drift:** Downstream system decisions diverging from expected patterns based on known model behavior

**Infrastructure Anomalies:**

- **Network Traffic Anomalies:** Unexpected latency, routing, or protocol deviations in model-to-application communication
- **Access Log Inconsistencies:** Unauthorized access to API gateways, message queues, or inter-service channels
- **Message Queue Tampering Indicators:** Unexpected message modifications, duplicate messages with different content, or out-of-order delivery
- **Process Integrity Violations:** Unexpected processes or threads accessing inter-service communication channels

**Statistical Anomalies:**

- **Output Distribution Mismatch:** Distribution of outputs observed by consuming systems differing from model's actual output distribution
- **Confidence Score Anomalies:** Patterns of confidence scores inconsistent with model's normal behavior
- **Prediction Pattern Changes:** Sudden shifts in prediction patterns not correlated with input distribution changes


## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |


## Mitigations

| ID | Name | Description |
|----|------|-------------|
| | [[mitigations/cryptographic-output-signing]] | Model service digitally signs all outputs; consuming systems verify signatures before acting |
| | [[mitigations/secure-transport-layer-security]] | Enforce TLS 1.3+ with mutual TLS (mTLS) for all model-to-application communication |
| | [[mitigations/output-consistency-monitoring]] | Compare model service logs with downstream system logs to detect output divergence |
| AML.M0015 | [[mitigations/ai-api-security]] | API gateway controls, authentication, and authorization prevent unauthorized access to model outputs |
| | [[mitigations/output-filtering-and-sanitization]] | Validate and sanitize outputs before consumption; detect anomalous content |
| | [[mitigations/output-monitoring-validation]] | Runtime monitoring of model outputs for quality, safety, and integrity violations |
| | [[mitigations/dual-llm-judge-pattern]] | Secondary LLM evaluates outputs for policy violations and integrity issues |
| | [[mitigations/anomaly-detection-architecture]] | Statistical monitoring for output distribution anomalies and behavioral drift |
| | [[mitigations/telemetry-and-monitoring-architecture]] | Comprehensive logging and monitoring of model-to-application communication |
| | [[mitigations/incident-response-procedures]] | Automated rollback, service quarantine, and forensic investigation procedures |


## Sources

> "Output integrity attacks target the infrastructure layer between model inference and downstream processing. Attackers intercept model outputs and modify them before consuming systems act on the results. The vulnerability exists when consuming systems implicitly trust model outputs without cryptographic verification, integrity checking, or secure transport."
> — Derived from [[sources/bibliography#OWASP ML Security Top 10]], ML09: Output Integrity Attack

> "Unlike adversarial attacks that manipulate inputs or data poisoning that corrupts training, output integrity attacks exploit weak verification of model outputs in system pipelines. These attacks enable adversaries to bypass security controls, manipulate decision outcomes, or subvert model-driven automation without requiring direct model access or sophisticated ML expertise."
> — Derived from [[sources/bibliography#OWASP ML Security Top 10]], ML09: Output Integrity Attack

> "Important Note: Output integrity attacks bypass all model-level defenses. Protection requires infrastructure-level security (cryptography, secure transport, zero-trust architecture) rather than model hardening."
> — Derived from [[sources/bibliography#OWASP ML Security Top 10]], ML09: Output Integrity Attack
