---
description: "Adversary intercepts and manipulates model outputs before downstream processing, making it appear as though the model produced different results."
tags:
  - trust-boundary/deployment-governance
  - trust-boundary/model
  - type/attack
  - target/llm-app
  - target/agent
  - access/black-box
  - severity/high
  - atlas/aml.t0051
---
# Output Integrity Attack

## Summary

Output integrity attacks target the infrastructure layer between model inference and downstream processing, rather than the model itself. Attackers intercept model outputs and modify them before consuming systems act on the results, creating a disconnect between what the model actually predicted and what downstream systems observe. This attack is particularly insidious because the model appears to function normally when inspected directly—the compromise occurs in transit or at integration boundaries. Unlike adversarial attacks that manipulate inputs or data poisoning that corrupts training, output integrity attacks exploit weak verification of model outputs in system pipelines. The vulnerability exists when consuming systems implicitly trust model outputs without cryptographic verification, integrity checking, or secure transport. These attacks enable adversaries to bypass security controls, manipulate decision outcomes, or subvert model-driven automation without requiring direct model access or sophisticated ML expertise.

## Threat Scenario

**Malware Classifier Bypass**: An enterprise deploys an ML-based malware detection system where binaries are scanned, classified by the model, and automatically deleted if labeled as malicious. An attacker gains access to the application layer between the model inference service and the file management system—either through a compromised middleware component, insider access, or exploitation of an API gateway vulnerability. When the attacker uploads their custom malware, the model correctly classifies it as malicious with high confidence. However, the attacker's malicious code intercepts the model's output before it reaches the file management system, replacing "malicious" with "benign" in the classification result. The file management system receives the forged output, believes the model deemed the file safe, and preserves the malware on disk. The attack succeeds without manipulating the model or triggering any model-level anomaly detection, leaving security teams unaware the model's correct decision was overridden at the integration layer.

**Fraud Detection Manipulation**: A financial institution uses an LLM-based fraud detection system to screen high-value transactions. An adversary compromises the message queue or API gateway between the model service and the transaction processing system. When a fraudulent transaction is submitted, the model correctly flags it with a fraud risk score of 0.95. The attacker's malicious middleware intercepts this API response and rewrites the risk score to 0.05 (low risk) before forwarding it to the transaction processing system. The transaction is approved and funds are transferred, while audit logs show the model produced a low-risk score. Forensic analysis of the model itself reveals no compromise—the attack occurred purely at the integration boundary.

**Autonomous Vehicle Safety Override**: A self-driving vehicle uses an ML-based perception system to classify road signs and obstacles. An attacker exploits a vulnerability in the vehicle's embedded software architecture, gaining access to the inter-process communication channel between the perception model and the vehicle control system. When the model correctly identifies a stop sign, the attacker's malicious code intercepts the classification result and changes it to "yield sign" before it reaches the control system. The vehicle fails to stop at the intersection despite the model functioning correctly, creating a safety hazard that evades model-based testing and validation.

## Preconditions

- Weak or absent integrity verification of model outputs (no cryptographic signatures, checksums, or authentication)
- Vulnerable integration points between model inference and consuming systems (insecure APIs, message queues, shared memory, unencrypted channels)
- Attacker has access to infrastructure layer:
  - Compromised middleware or API gateway
  - Insider threat with access to application layer
  - Exploitation of software vulnerabilities in integration components
- Lack of end-to-end secure transport (unencrypted channels, missing TLS, or improper certificate validation)
- Insufficient access controls on inter-service communication channels
- Absence of behavioral monitoring correlating model predictions with actual system actions
- Trust assumption: Downstream systems implicitly trust outputs without validation

## Abuse Path

1. **Access Acquisition**: Attacker gains position to intercept model outputs through:
   - **Compromised Middleware**: Exploit vulnerabilities in API gateways, load balancers, or message brokers
   - **Insider Threat**: Legitimate access to application or infrastructure layer by malicious employee or contractor
   - **Man-in-the-Middle**: Network-level interception if transport security is weak or absent
   - **Supply Chain Compromise**: Malicious code injected into dependencies, libraries, or container images used in integration layer
   - **Privilege Escalation**: Initial foothold expanded to gain access to inter-service communication channels

2. **Traffic Interception**: Attacker positions themselves to observe and intercept model outputs:
   - Monitor API calls between model service and consuming applications
   - Intercept messages in message queues (Kafka, RabbitMQ, SQS)
   - Hook into shared memory or inter-process communication channels
   - Proxy or redirect network traffic through attacker-controlled infrastructure

3. **Output Manipulation**: Attacker modifies intercepted model outputs to achieve desired outcome:
   - **Classification Flipping**: Change predicted class labels (malicious → benign, fraud → legitimate)
   - **Confidence Score Manipulation**: Alter probability scores to influence decision thresholds
   - **Content Rewriting**: Modify generated text, summaries, or recommendations in LLM outputs
   - **Selective Manipulation**: Only tamper with outputs matching specific patterns (e.g., attacker's own inputs)

4. **Validation Evasion**: Attacker ensures modified outputs pass downstream validation:
   - Maintain output format and schema compliance
   - Preserve message signatures if only basic format validation exists (but not cryptographic signing)
   - Ensure manipulated values remain within expected ranges
   - Avoid triggering anomaly detection by limiting manipulation frequency

5. **Downstream Impact**: Consuming system acts on forged output, believing it's the model's genuine prediction:
   - Security controls bypassed (malware not deleted, fraud not blocked)
   - Business logic subverted (approvals granted, access allowed, funds transferred)
   - Safety systems defeated (autonomous vehicle failures, medical device errors)

## Impact

**Business Impact:**
- **Security Control Bypass**: Model-based defenses (malware detection, fraud prevention, content moderation) systematically defeated without triggering model-level alerts
- **Financial Fraud**: Fraudulent transactions approved, payment fraud succeeding, or unauthorized fund transfers enabled
- **Regulatory Violations**: Compliance failures when security or risk models are compromised (PCI DSS, SOC 2, financial regulations)
- **Safety Incidents**: Physical harm in safety-critical systems (autonomous vehicles, medical devices, industrial control) when model safety predictions are overridden
- **Reputational Damage**: Loss of customer trust when model-driven decisions are revealed to be manipulated
- **Liability Exposure**: Legal liability for harm caused by tampered outputs, especially in regulated or safety-critical domains
- **Detection Difficulty**: Model appears functional during testing, making root cause analysis challenging and expensive

**Technical Impact:**
- **Trust Boundary Violation**: Implicit trust between model and consuming systems exploited as single point of failure
- **Model-Level Defenses Ineffective**: Adversarial robustness, input validation, and model monitoring cannot detect post-output tampering
- **Audit Trail Corruption**: Logs may show forged outputs rather than genuine model predictions, complicating forensics
- **Persistent Compromise**: Attacker maintains access to intercept future outputs until infrastructure compromise is discovered and remediated

**Severity:** **High to Critical** (Critical for safety/security systems where output tampering causes immediate harm; High for systems with strong compensating controls)

## Detection Signals

- **Model Output vs. System Action Divergence**: Discrepancies between logged model predictions and actual system behavior (e.g., model logs show "malicious" but file was not deleted)
- **Integrity Check Failures**: Cryptographic signature verification failures on model outputs (if implemented)
- **Network Traffic Anomalies**: Unexpected latency, routing, or protocol deviations in model-to-application communication
- **Access Log Inconsistencies**: Unauthorized access to API gateways, message queues, or inter-service channels
- **Message Queue Tampering Indicators**: Unexpected message modifications, duplicate messages with different content, or out-of-order delivery
- **Behavioral Drift**: Downstream system decisions diverging from expected patterns based on known model behavior
- **Process Integrity Violations**: Unexpected processes or threads accessing inter-service communication channels
- **Statistical Output Anomalies**: Distribution of outputs observed by consuming systems differing from model's actual output distribution

## Testing Approach

**Manual Testing:**

1. **Integration Point Vulnerability Assessment**:
   - Map all communication paths between model inference service and consuming systems
   - Identify unencrypted channels, weak authentication, or missing integrity verification
   - Test API gateway and middleware configurations for security misconfigurations
   - Assess message queue security (authentication, authorization, encryption)

2. **Man-in-the-Middle Testing**:
   - Simulate network-level interception of model outputs
   - Attempt to modify API responses or message queue payloads in transit
   - Test whether downstream systems detect or reject tampered outputs
   - Validate TLS/SSL implementation and certificate verification

3. **Integrity Verification Testing**:
   - Check if model outputs include cryptographic signatures or checksums
   - Attempt to forge or replay outputs to downstream systems
   - Test whether downstream systems validate output authenticity
   - Assess end-to-end integrity verification mechanisms

4. **Access Control Testing**:
   - Test authorization boundaries on inter-service communication channels
   - Attempt unauthorized access to API endpoints, message queues, or shared resources
   - Validate principle of least privilege for integration components

**Automated Testing:**
- **API Security Scanners**: Tools like OWASP ZAP, Burp Suite scanning for API gateway vulnerabilities and weak transport security
- **Message Queue Security Auditing**: Automated assessment of message broker configurations for weak authentication or missing encryption
- **Network Traffic Analysis**: Packet inspection tools detecting unencrypted or unauthenticated model-to-service communication
- **Integrity Monitoring**: Automated verification of cryptographic signatures or checksums on sampled outputs
- **End-to-End Testing Frameworks**: Automated tests validating model predictions match downstream system actions

## Evidence to Capture

- [ ] Communication architecture diagram showing model-to-application integration points
- [ ] Evidence of unencrypted or unauthenticated channels (packet captures, configuration files)
- [ ] Successful output manipulation examples (original model prediction vs. modified downstream observation)
- [ ] Access logs showing ability to intercept or modify inter-service communication
- [ ] Screenshots of tampered API responses or message queue payloads
- [ ] Integrity verification results (missing signatures, failed checksums, or absence of verification mechanisms)
- [ ] Divergence analysis comparing model logs to downstream system logs
- [ ] TLS/SSL configuration weaknesses (missing encryption, improper certificate validation)

## Mitigations

**Preventive Controls:**
- **Cryptographic Output Signing**: Model service digitally signs all outputs with private key; consuming systems verify signatures with public key before acting
- **End-to-End Encryption**: Enforce TLS 1.3+ for all model-to-application communication with mutual TLS (mTLS) for service-to-service authentication
- **Integrity Checksums**: Include cryptographic hash (SHA-256) of output content; downstream systems validate hash before processing
- **Secure Message Queues**: Configure message brokers with authentication, authorization, and encryption; use signed or encrypted message payloads
- **API Gateway Security**: Implement OAuth 2.0, JWT tokens, or API keys with signature validation for model API endpoints
- **Network Segmentation**: Isolate model inference services on separate network segments with strict firewall rules and zero-trust architecture
- **Immutable Audit Trails**: Log model outputs to tamper-evident audit log (blockchain, write-once storage) separate from operational systems
- **Service Mesh**: Deploy service mesh (Istio, Linkerd) enforcing mutual TLS, identity verification, and traffic encryption by default

**Detective Controls:**
- **Output Consistency Monitoring**: Continuously compare model service logs (original predictions) with downstream system logs (received predictions); alert on divergences
- **Behavioral Analysis**: Monitor downstream system decisions and correlate with expected model behavior; flag anomalies
- **Integrity Verification Audits**: Regularly sample outputs and verify cryptographic signatures or checksums are present and valid
- **Access Monitoring**: Track and alert on unauthorized access attempts to API gateways, message queues, or inter-service channels
- **Network Traffic Analysis**: Monitor for unusual patterns, protocol anomalies, or unencrypted traffic on model-to-application channels
- **Configuration Drift Detection**: Alert when integration component configurations change (e.g., encryption disabled, authentication weakened)

**Responsive Controls:**
- **Automated Rollback**: Immediately revert downstream system actions when output integrity failures detected
- **Service Quarantine**: Isolate compromised integration components from model service and downstream systems
- **Cryptographic Key Rotation**: Rotate signing keys and re-establish secure channels when tampering detected
- **Incident Investigation**: Forensic analysis to determine attack scope, identify compromised components, and assess impact
- **Compensating Controls**: Temporarily enforce human-in-the-loop review for high-stakes decisions until integrity restored
- **System Hardening**: Remediate identified vulnerabilities in API gateways, message brokers, or integration middleware post-incident

**Important Note**: Output integrity attacks bypass all model-level defenses. Protection requires infrastructure-level security (cryptography, secure transport, zero-trust architecture) rather than model hardening.

## Engagement Applicability

This issue is tested in the following engagements:

- [x] [[playbooks/ai-threat-exposure-review|AI Threat Exposure Review]] - Deployment/Governance boundary comprehensive assessment
- [x] [[playbooks/llm-application-red-team|LLM Application Red Team]] - Integration security and API vulnerability testing
- [x] Deployment & Integration Security - Infrastructure-level attack surface validation (detail page pending)
- [x] Governance & Compliance Deep Dive - Integrity verification and audit trail assessment (detail page pending)

[[playbooks/engagements-overview|View all engagements]]

## Framework References

**MITRE ATLAS:**
- AML.T0048: Exfiltrate via ML Inference API (related—involves model output manipulation)
- AML.TA0010: Exfiltration (tactic)

**OWASP LLM Top 10:**
- LLM08: Excessive Agency (related—downstream systems trusting outputs without validation)
- LLM09: Overreliance (related—uncritical acceptance of model outputs)

**OWASP ML Security Top 10:**
- ML09: Output Integrity Attack

**NIST AI RMF:**
- MANAGE 2.3: Mechanisms are in place for robust and reliable operation
- MEASURE 2.7: AI system security and resilience characteristics

**NIST GenAI Profile:**
- Information Security: Output integrity and authenticity verification
- Value Chain and Component Integration: Secure integration between AI system components
