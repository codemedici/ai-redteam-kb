---
title: "War Story: The Silent Backdoor"
tags:
  - type/case-study
  - target/ml-model
  - target/training-pipeline
  - source/red-teaming-ai
atlas: AML.CS0008
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# War Story: The Silent Backdoor

## Summary

A fintech startup's ML-based fraud detection system was compromised when an attacker exploited outdated Git credentials in a secondary repository to modify the CI/CD pipeline. By injecting malicious code into the model serialization script, the attacker embedded a Pickle-based backdoor that executed during model loading in production. The attack succeeded due to multiple MLOps security failures: lack of artifact signing, use of mutable container tags, absence of integrity checks, and vulnerable serialization format. The backdoored model established a reverse shell, enabling weeks of undetected access to production infrastructure and exfiltration of customer transaction data.

## Incident Details

**Target Organization:** Fintech startup  
**System Compromised:** ML-based fraud detection model  
**Initial Access:** Outdated developer credentials in secondary Git repository  
**Attack Duration:** Several weeks before detection  
**Detection Trigger:** Unusual outbound network traffic pattern

**Attack Timeline:**

1. **Initial Compromise (Day 0):**
   - Attacker discovered active credentials in secondary Git repository
   - Credentials had limited direct production access
   - Access granted to CI/CD system configuration

2. **Pipeline Modification (Day 1):**
   - Modified build script for model serialization
   - Inserted code to inject malicious payload during Pickle serialization
   - Changes disguised as minor refactoring (small commits, innocuous messages)

3. **Payload Creation (Day 2):**
   - Malicious code: reverse shell connecting to attacker-controlled server
   - Payload designed to execute automatically on model loading
   - Exploited Python Pickle's arbitrary code execution during deserialization

4. **Automated Build (Day 3):**
   - CI/CD pipeline automatically triggered on commit
   - Backdoored model built without detection
   - No security scanning for malicious payloads in model artifacts

5. **Registry Upload (Day 3):**
   - Backdoored model pushed to artifact registry
   - **MLOps Failure #1:** No artifact signing requirement
   - **MLOps Failure #2:** No signature verification at upload

6. **Production Deployment (Day 4):**
   - Deployment system pulled latest model using mutable `latest` tag
   - **MLOps Failure #3:** No version pinning (relied on mutable tags)
   - **MLOps Failure #4:** No integrity checks before deployment
   - Backdoored model loaded in production

7. **Backdoor Execution (Day 4):**
   - Pickle deserialization triggered malicious payload
   - Reverse shell established to attacker's command server
   - Connection bypassed firewall (outbound HTTPS to legitimate-looking domain)

8. **Persistence and Exfiltration (Days 4-24):**
   - Attacker gained production environment access
   - Lateral movement to database servers
   - Exfiltration of customer transaction data
   - Access to internal model details and training data
   - Weeks of undetected activity

9. **Detection (Day 25):**
   - Network monitoring flagged unusual outbound traffic pattern
   - Investigation revealed reverse shell connection
   - Forensics traced backdoor to model artifact

## Techniques Used

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0008 | [[techniques/ai-infrastructure-attacks]] | Compromised CI/CD pipeline to inject backdoor during model build; exploited lack of artifact signing and integrity verification |
| | [[techniques/insecure-deserialization]] | Exploited Python Pickle arbitrary code execution to embed reverse shell in model file |

## Tactic Flow

**MITRE ATT&CK / ATLAS Mapping:**

1. **[[frameworks/atlas/tactics/initial-access]]**: Credential access via exposed Git repository
2. **[[frameworks/atlas/tactics/execution]]**: Modified CI/CD pipeline to inject malicious code
3. **[[frameworks/atlas/tactics/persistence]]**: Backdoor embedded in production model artifact
4. **[[frameworks/atlas/tactics/privilege-escalation]]**: Production access via model loading context
5. **[[frameworks/atlas/tactics/defense-evasion]]**: Disguised changes as legitimate refactoring; bypass of firewall via HTTPS
6. **[[frameworks/atlas/tactics/collection]]**: Exfiltration of customer data and model details

## Impact & Outcome

**Immediate Impact:**
- Production fraud detection system compromised
- Customer transaction data exfiltrated
- Proprietary ML model details stolen
- Several weeks of undetected attacker access

**Business Consequences:**
- Regulatory fines for data breach
- Mandatory customer notification (GDPR, state breach laws)
- Reputational damage in competitive fintech market
- Emergency security audit and remediation costs
- Potential lawsuits from affected customers

**Remediation Actions:**
1. Immediate model rollback to verified clean version
2. Credential rotation across all systems
3. Full infrastructure security audit
4. Implementation of mandatory artifact signing
5. Deployment process overhaul (version pinning, integrity checks)
6. Transition from Pickle to SafeTensors for model serialization
7. Enhanced monitoring and alerting
8. Red team engagement to validate fixes

## Lessons Learned

**MLOps Security Failures:**

1. **No Artifact Signing:**
   - Models uploaded without cryptographic signatures
   - No verification of artifact integrity or origin
   - **Fix:** Mandatory signing with Sigstore/Cosign; deployment gate rejecting unsigned artifacts

2. **Mutable Tags in Production:**
   - Deployment used `latest` tag instead of version pinning
   - Enabled silent substitution of backdoored model
   - **Fix:** Immutable version pinning; content-addressable storage (SHA256 hashes)

3. **Lack of Integrity Checks:**
   - No hash verification before deployment
   - No comparison against known-good baseline
   - **Fix:** Deployment verification against stored checksums; immutable artifact registry

4. **Vulnerable Serialization Format:**
   - Python Pickle enabled arbitrary code execution
   - No scanning for malicious payloads
   - **Fix:** Migration to SafeTensors; ModelScan integration for Pickle scanning

5. **Insufficient CI/CD Security:**
   - Weak access controls on pipeline configuration
   - No review requirement for pipeline changes
   - **Fix:** Branch protection requiring security team approval; pipeline-as-code review gates

6. **Credential Hygiene:**
   - Stale credentials in secondary repository
   - No regular rotation or audit
   - **Fix:** Automated credential rotation; secrets scanning (truffleHog/Gitleaks); regular access reviews

**What Would Have Prevented This:**
- ✅ **Artifact Signing**: Unsigned model would have been rejected at deployment gate
- ✅ **Immutable Tags**: Version pinning would have prevented silent model substitution
- ✅ **Integrity Verification**: Hash mismatch would have triggered alert
- ✅ **Safe Serialization**: SafeTensors prevents arbitrary code execution
- ✅ **Branch Protection**: Pipeline modification would have required security review
- ✅ **Secret Scanning**: Git hook would have flagged exposed credentials
- ✅ **Network Monitoring**: Outbound reverse shell detected earlier with proper egress filtering

**Broader Implications:**
- Infrastructure security is not optional for ML systems
- "Soft" targets (MLOps) can bypass "hard" model-level defenses
- Defense in depth requires security at every pipeline stage
- Automated pipelines need automated security controls (signing, scanning, verification)

## Sources

> "War Story: The Silent Backdoor. Company: Fintech startup with ML-based fraud detection. Attack vector: Outdated developer credentials in secondary Git repo enabled CI/CD system access. Attack process: Modified build script for model serialization, inserted Pickle deserialization payload (reverse shell), automated build created backdoored model, pushed to registry without signature verification. MLOps failures: no artifact signing, mutable 'latest' tag, no integrity checks. Impact: Backdoored model loaded in production, reverse shell established, weeks of production access, customer data exfiltration."
> — [[sources/bibliography#Red-Teaming AI]], p. 284-285

> "Lesson: Artifact integrity verification is critical—signing and verification mandatory for production deployments."
> — [[sources/bibliography#Red-Teaming AI]], p. 285
