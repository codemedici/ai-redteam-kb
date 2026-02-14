---
title: "Incident Response Procedures"
tags:
  - type/mitigation
  - target/llm-app
  - target/agent
  - target/ml-pipeline
  - target/model-api
  - source/generative-ai-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Incident Response Procedures

## Summary

Incident response procedures for AI systems provide structured playbooks for detecting, containing, remediating, and learning from security incidents specific to LLM and agentic AI deployments. These procedures address the unique characteristics of AI security incidents—including jailbreak campaigns, prompt injection attacks, data leakage, model compromise, and agent failures—with response workflows tailored to the technical and operational realities of AI systems. Unlike traditional cybersecurity incidents where threats are external, AI incidents often involve adversarial manipulation of the model's own behavior through input manipulation or safety control bypass. Effective incident response requires rapid detection, coordinated cross-functional action, forensic analysis capabilities, and continuous improvement feedback loops.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/incident-response-gap]] | Establishes comprehensive IR procedures and capabilities to eliminate the vulnerability created by absence of AI-specific incident response |
| AML.T0015 | [[techniques/adversarial-robustness]] | Structured response to adversarial example attacks: adversarial example database maintenance, dynamic defense adaptation, model replacement procedures |
| AML.T0054 | [[techniques/jailbreak-policy-bypass]] | Rapid response to discovered jailbreak campaigns, including automated account suspension and emergency patching |
| AML.T0051 | [[techniques/prompt-injection]] | Structured response to prompt injection incidents and data leakage |
| AML.T0020 | [[techniques/rag-data-poisoning]] | Defined procedures for data poisoning incident investigation, containment, and remediation |
| | [[techniques/system-prompt-leakage]] | Incident response for unauthorized disclosure of system prompts |
| | [[techniques/sensitive-info-disclosure]] | Procedures for containing and remediating data leakage incidents |
| | [[techniques/agent-goal-hijack]] | Response playbooks for agent manipulation and unauthorized actions |
| | [[techniques/unsafe-tool-invocation]] | Emergency procedures for dangerous tool invocations |
| | [[techniques/weak-access-segmentation]] | Automated permission revocation, account suspension for policy violations, emergency access revocation procedures |
| AML.T0049 | [[techniques/model-extraction-llm]] | Account suspension, forensic analysis, and legal response for LLM extraction campaigns |
| AML.T0020 | [[techniques/supply-chain-model-poisoning]] | Incident playbooks for poisoned model discovery: model rollback, downstream impact assessment, forensic analysis of training provenance, and trigger pattern identification |
| AML.T0047 | [[techniques/model-tampering]] | Defined procedures for investigating suspected tampering, preserving evidence, quarantining tampered models, and determining scope of compromise |
| AML.T0056 | [[techniques/training-data-memorization]] | Privacy breach procedures: regulatory notification, affected party communication, model replacement when memorization-based leakage confirmed |
| | [[techniques/api-authentication-bypass]] | Emergency response to authentication bypass incidents including token revocation, key rotation, account lockout, and forensic analysis |

## Implementation

### Incident Response Framework

#### Phase 1: Preparation

**Establish incident response infrastructure:**

1. **Incident Response Team (IRT) Structure:**
   - **Incident Commander:** Coordinates response, makes escalation decisions
   - **Security Analysts:** Forensic analysis, threat intelligence
   - **Model Engineers:** Model behavior analysis, emergency patching
   - **Application Developers:** System modifications, mitigation deployment
   - **Legal/Compliance:** Regulatory notification, external communications
   - **On-call rotation:** 24/7 coverage for critical incidents

2. **Detection Systems:**
   - [[mitigations/anomaly-detection-architecture|Anomaly detection]] for unusual query patterns
   - [[mitigations/output-filtering-and-sanitization|Output monitoring]] for policy violations
   - Alert thresholds and escalation criteria
   - Integration with SIEM (Security Information and Event Management) systems

3. **Communication Channels:**
   - Dedicated Slack/Teams channel for incident coordination
   - Emergency contact lists (phone, email, pager)
   - Stakeholder notification templates
   - External communication protocols (users, regulators, media)

4. **Documentation and Tools:**
   - Incident response playbooks (see below)
   - Forensic analysis tools (log aggregation, query replay, model probing)
   - Rollback and patching procedures
   - Post-incident review templates

#### Phase 2: Detection and Identification

**Incident triggers:**

1. **Automated Detection:**
   - Anomaly detection alerts (unusual query patterns, adversarial suffixes)
   - Policy violation spikes (sudden increase in harmful outputs)
   - Rate limiting violations (coordinated attack campaigns)
   - System alerts (unauthorized access, privilege escalation)

2. **User Reports:**
   - User-reported jailbreaks or harmful outputs
   - Customer support escalations
   - External researcher disclosures

3. **Threat Intelligence:**
   - External disclosure of new jailbreak techniques
   - Industry intelligence sharing (ISACs, security communities)
   - Academic publications of adversarial attacks

**Initial triage:**

```python
class IncidentTriage:
    """
    Automated incident triage and severity classification
    """
    
    SEVERITY_LEVELS = {
        'CRITICAL': {
            'criteria': [
                'Full jailbreak bypass affecting all users',
                'Sensitive data leakage at scale',
                'Agent performing dangerous unauthorized actions',
                'Active coordinated attack campaign'
            ],
            'response_time': '15 minutes',
            'escalation': 'CISO, CEO'
        },
        'HIGH': {
            'criteria': [
                'Partial jailbreak bypass (limited scope)',
                'Individual sensitive data disclosure',
                'Successful prompt injection with limited impact',
                'Policy violation spike (>10x baseline)'
            ],
            'response_time': '1 hour',
            'escalation': 'Security Director'
        },
        'MEDIUM': {
            'criteria': [
                'Edge case policy violation',
                'Unusual query patterns without confirmed bypass',
                'Single-user anomalous behavior'
            ],
            'response_time': '4 hours',
            'escalation': 'Security Team Lead'
        },
        'LOW': {
            'criteria': [
                'False positive alerts',
                'Minor misconfigurations',
                'User education issues'
            ],
            'response_time': '24 hours',
            'escalation': 'None'
        }
    }
    
    def classify_incident(self, alert_data):
        """
        Classify incident severity based on alert characteristics
        """
        # Check for critical indicators
        if self._is_critical(alert_data):
            return 'CRITICAL'
        elif self._is_high(alert_data):
            return 'HIGH'
        elif self._is_medium(alert_data):
            return 'MEDIUM'
        else:
            return 'LOW'
    
    def _is_critical(self, alert_data):
        """Critical: Full jailbreak bypass or coordinated campaign"""
        return (
            alert_data.get('jailbreak_success_rate', 0) > 0.8 or
            alert_data.get('affected_users') > 1000 or
            alert_data.get('coordinated_campaign_detected', False) or
            alert_data.get('data_leakage_severity') == 'high'
        )
    
    def _is_high(self, alert_data):
        """High: Partial bypass or significant policy violations"""
        return (
            alert_data.get('jailbreak_success_rate', 0) > 0.3 or
            alert_data.get('policy_violation_spike') > 10 or
            alert_data.get('affected_users') > 100
        )
    
    def _is_medium(self, alert_data):
        """Medium: Unusual patterns without confirmed compromise"""
        return (
            alert_data.get('anomaly_score') > 0.7 or
            alert_data.get('policy_violation_spike') > 3
        )
```

#### Phase 3: Containment

**Immediate containment actions:**

**1. Automated Account Suspension**

For coordinated jailbreak campaigns or systematic abuse:

```python
class AutomatedContainment:
    """
    Automated containment actions for AI security incidents
    """
    
    def __init__(self):
        self.suspension_db = connect_to_user_management()
        self.audit_log = connect_to_audit_system()
    
    def suspend_abusive_accounts(self, incident_data):
        """
        Automatically suspend accounts exhibiting jailbreak patterns
        """
        abusive_accounts = incident_data.get('flagged_users', [])
        
        for account in abusive_accounts:
            # Validate suspension criteria
            if self._meets_suspension_criteria(account):
                # Suspend account
                self.suspension_db.suspend_user(
                    user_id=account['user_id'],
                    reason='automated_jailbreak_detection',
                    duration='24_hours',  # Temporary pending review
                    incident_id=incident_data['incident_id']
                )
                
                # Audit log
                self.audit_log.record({
                    'action': 'account_suspended',
                    'user_id': account['user_id'],
                    'reason': 'jailbreak_pattern_detected',
                    'automated': True,
                    'timestamp': datetime.now()
                })
                
                # Notify security team
                notify_security_team({
                    'event': 'account_suspended',
                    'user_id': account['user_id'],
                    'incident_id': incident_data['incident_id']
                })
    
    def _meets_suspension_criteria(self, account):
        """
        Determine if account should be automatically suspended
        """
        return (
            account.get('jailbreak_attempts', 0) > 5 or
            account.get('policy_violations', 0) > 10 or
            account.get('coordinated_campaign_participant', False)
        )
    
    def rate_limit_aggressive_users(self, incident_data):
        """
        Apply aggressive rate limiting to suspicious accounts
        (less severe than full suspension)
        """
        suspicious_accounts = incident_data.get('suspicious_users', [])
        
        for account in suspicious_accounts:
            # Apply 10x stricter rate limit
            self.suspension_db.update_rate_limit(
                user_id=account['user_id'],
                new_limit='10 requests per hour',  # Down from 100/hour
                duration='24_hours',
                reason='suspicious_activity'
            )
```

**2. Emergency Model Rollback**

If deployed model is compromised or exhibiting widespread jailbreak vulnerability:

```python
def emergency_model_rollback(incident_id, reason):
    """
    Emergency rollback to previous stable model version
    """
    logging.critical(f"EMERGENCY ROLLBACK INITIATED: {reason}")
    
    # Identify previous stable version
    previous_version = get_previous_stable_model_version()
    
    # Perform rollback
    rollback_result = deploy_model_version(
        version=previous_version,
        reason=f"Emergency rollback: {reason}",
        incident_id=incident_id,
        priority='CRITICAL'
    )
    
    # Invalidate model cache
    invalidate_model_cache()
    
    # Notify stakeholders
    notify_incident_team({
        'action': 'emergency_rollback',
        'from_version': get_current_model_version(),
        'to_version': previous_version,
        'reason': reason,
        'incident_id': incident_id
    })
    
    # Audit log
    audit_log.record({
        'action': 'emergency_model_rollback',
        'incident_id': incident_id,
        'timestamp': datetime.now(),
        'previous_version': get_current_model_version(),
        'rollback_version': previous_version
    })
    
    return rollback_result
```

**3. Circuit Breaker Activation**

Temporarily disable high-risk features:

```python
class CircuitBreaker:
    """
    Emergency circuit breaker for high-risk AI features
    """
    
    def activate_circuit_breaker(self, feature_name, incident_id):
        """
        Disable specific feature during incident containment
        """
        features_to_disable = {
            'tool_execution': 'Disable all agent tool invocations',
            'external_api_calls': 'Block external API access',
            'code_generation': 'Disable code generation capabilities',
            'web_search': 'Disable web search tool',
            'file_access': 'Block file system access'
        }
        
        if feature_name in features_to_disable:
            logging.warning(f"Activating circuit breaker: {feature_name}")
            
            # Update feature flags
            set_feature_flag(feature_name, enabled=False)
            
            # Notify users (if customer-facing)
            notify_users({
                'message': f'Feature temporarily unavailable due to security maintenance: {feature_name}',
                'incident_id': incident_id
            })
            
            # Audit log
            audit_log.record({
                'action': 'circuit_breaker_activated',
                'feature': feature_name,
                'incident_id': incident_id,
                'timestamp': datetime.now()
            })
```

#### Phase 4: Investigation and Forensics

**Forensic analysis:**

1. **Query Log Analysis:**
   - Extract all queries from flagged accounts
   - Identify attack patterns, adversarial suffixes, injection techniques
   - Reconstruct attack timeline
   - Assess scope (how many users affected, how long attack persisted)

2. **Model Output Analysis:**
   - Collect all policy-violating outputs
   - Categorize violation types (toxicity, illegal content, etc.)
   - Analyze model behavior patterns during attack
   - Determine if attack exploited model vulnerability or bypassed filters

3. **Attribution:**
   - Correlate accounts, IP addresses, query patterns
   - Identify if coordinated campaign (multiple accounts, shared suffixes)
   - Check threat intelligence for known attack campaigns
   - Preserve evidence for potential legal action

```python
class IncidentForensics:
    """
    Forensic analysis tools for AI security incidents
    """
    
    def analyze_jailbreak_incident(self, incident_id):
        """
        Comprehensive forensic analysis of jailbreak incident
        """
        # Collect incident data
        incident_data = self.collect_incident_data(incident_id)
        
        # Analyze attack patterns
        attack_analysis = {
            'attack_type': self.classify_attack_type(incident_data),
            'adversarial_suffixes': self.extract_adversarial_suffixes(incident_data),
            'affected_users': self.identify_affected_users(incident_data),
            'policy_violations': self.categorize_violations(incident_data),
            'attack_timeline': self.reconstruct_timeline(incident_data),
            'coordination_evidence': self.detect_coordinated_campaign(incident_data)
        }
        
        # Generate forensic report
        report = self.generate_forensic_report(incident_id, attack_analysis)
        
        return report
    
    def extract_adversarial_suffixes(self, incident_data):
        """
        Extract and analyze adversarial suffixes used in attack
        """
        queries = incident_data['flagged_queries']
        suffixes = []
        
        for query in queries:
            # Extract potential suffix (last 20% of query)
            words = query.split()
            if len(words) > 10:
                suffix = ' '.join(words[int(len(words)*0.8):])
                suffixes.append(suffix)
        
        # Cluster similar suffixes
        unique_suffixes = self.cluster_similar_suffixes(suffixes)
        
        return unique_suffixes
    
    def detect_coordinated_campaign(self, incident_data):
        """
        Detect if incident is part of coordinated attack campaign
        """
        accounts = incident_data['flagged_accounts']
        
        # Check for shared suffix patterns
        suffix_overlap = self.calculate_suffix_overlap(accounts)
        
        # Check for temporal correlation
        temporal_correlation = self.analyze_attack_timing(accounts)
        
        # Check for IP/device correlation
        infrastructure_overlap = self.analyze_infrastructure(accounts)
        
        is_coordinated = (
            suffix_overlap > 0.7 or  # >70% use same suffixes
            temporal_correlation > 0.8 or  # Attacks highly time-correlated
            infrastructure_overlap > 0.5  # Share infrastructure
        )
        
        return {
            'is_coordinated': is_coordinated,
            'suffix_overlap': suffix_overlap,
            'temporal_correlation': temporal_correlation,
            'infrastructure_overlap': infrastructure_overlap
        }
```

#### Phase 5: Remediation

**Rapid patching procedures:**

**1. Emergency Model Fine-Tuning**

```python
def emergency_model_patch(discovered_jailbreaks):
    """
    Emergency fine-tuning to patch discovered jailbreak techniques
    """
    # Create adversarial training examples
    training_examples = []
    for jailbreak in discovered_jailbreaks:
        training_examples.append({
            'input': jailbreak['query'] + " " + jailbreak['suffix'],
            'output': "I cannot and will not provide assistance with that request.",
            'metadata': {'source': 'incident_response', 'severity': 'critical'}
        })
    
    # Fine-tune model (fast training with high learning rate)
    patched_model = fine_tune_model(
        base_model=current_model,
        training_data=training_examples,
        epochs=3,
        learning_rate=1e-4,  # Higher than normal for fast adaptation
        validation_split=0.2
    )
    
    # Validate patch effectiveness
    validation_results = validate_patch(patched_model, discovered_jailbreaks)
    
    if validation_results['success_rate'] < 0.1:  # <10% still succeed
        # Deploy patched model
        deploy_model(patched_model, reason='emergency_jailbreak_patch')
        return {'status': 'success', 'model_version': patched_model.version}
    else:
        # Patch insufficient, escalate
        return {'status': 'insufficient', 'requires_manual_intervention': True}
```

**2. Update Defensive Controls**

```python
def update_defensive_controls(incident_analysis):
    """
    Update defensive systems based on incident findings
    """
    # Update suffix blacklist
    for suffix in incident_analysis['adversarial_suffixes']:
        add_to_suffix_blacklist(suffix, source='incident_response')
    
    # Update anomaly detection rules
    new_attack_patterns = extract_attack_patterns(incident_analysis)
    update_anomaly_detection_rules(new_attack_patterns)
    
    # Update input validation
    update_input_validation_patterns(incident_analysis['attack_patterns'])
    
    # Update rate limits (if needed)
    if incident_analysis['coordination_evidence']['is_coordinated']:
        tighten_rate_limits(reason='coordinated_attack_response')
```

#### Phase 6: Recovery

**Return to normal operations:**

1. **Gradual rollout:** Deploy patches to canary users first, then broader rollout
2. **Monitoring:** Intensive monitoring for 48-72 hours post-patch
3. **User communications:** Notify affected users, explain incident and resolution
4. **Feature restoration:** Re-enable circuit breakers and suspended features

#### Phase 7: Post-Incident Review

**Lessons learned:**

```markdown
## Post-Incident Review Template

**Incident ID:** [ID]
**Date:** [Date]
**Severity:** Critical/High/Medium/Low

### Incident Summary
[Brief description of what happened]

### Timeline
| Time | Event |
|------|-------|
| T+0 | Initial detection |
| T+15min | Incident declared, IRT activated |
| T+30min | Containment actions deployed |
| T+2hr | Patch developed and validated |
| T+4hr | Patch deployed to production |
| T+24hr | Monitoring confirms remediation |

### Root Cause Analysis
**What failed:**
- [Technical control that was bypassed]
- [Process gap that delayed detection]

**Why it failed:**
- [Underlying vulnerability or misconfiguration]

### Remediation Actions
**Immediate (completed):**
- [Emergency patches deployed]
- [Accounts suspended]

**Short-term (1-2 weeks):**
- [Defensive control improvements]
- [Process updates]

**Long-term (1-3 months):**
- [Architectural changes]
- [Organizational improvements]

### Metrics
- **Time to detection:** [X minutes]
- **Time to containment:** [X minutes]
- **Time to remediation:** [X hours]
- **Affected users:** [X]
- **False positive rate:** [X%]

### Lessons Learned
**What went well:**
- [Effective detection systems]
- [Rapid team coordination]

**What needs improvement:**
- [Gaps in monitoring]
- [Slow escalation process]

### Action Items
| Action | Owner | Due Date | Status |
|--------|-------|----------|--------|
| Update suffix blacklist | Security Team | [Date] | Complete |
| Improve anomaly detection | ML Eng | [Date] | In Progress |
| Conduct training on new procedures | IR Lead | [Date] | Planned |
```

### Incident-Specific Playbooks

#### Authentication Bypass Incident Response

**Incident Type:** API Authentication Bypass (token forgery, signature bypass, credential reuse)

**Detection Signals:**
- Multiple API requests with expired tokens succeeding
- JWT tokens with "none" algorithm accepted
- Signature validation failures followed by successful API responses
- Unusual token usage patterns (geolocation anomalies, impossible travel)
- High-volume API access from newly authenticated accounts
- Tokens with modified payload claims (role escalation, extended expiration)

**Phase 1: Immediate Containment (0-15 minutes)**

1. **Automated Token Revocation:**
```python
# Revoke all tokens for affected users
def emergency_revoke_compromised_tokens(incident_id, affected_users):
    """Immediately revoke all active tokens for compromised users"""
    for user_id in affected_users:
        # Get all active sessions
        sessions = get_active_sessions(user_id)
        
        # Revoke each token
        for session in sessions:
            revoke_token(session.token_id, reason=f"incident_{incident_id}")
        
        # Force re-authentication
        set_user_status(user_id, status="requires_reauthentication")
        
        logging.critical(f"Revoked all tokens for user {user_id} - Incident {incident_id}")
```

2. **Account Lockout (if malicious activity confirmed):**
- Temporarily lock accounts exhibiting attack patterns
- Prevent re-authentication until investigation complete
- Log lockout events with full context

3. **Circuit Breaker Activation:**
- If widespread bypass detected, temporarily disable authentication for affected endpoints
- Route to maintenance page or rate-limit aggressively
- Prevent continued unauthorized access while patch is developed

**Phase 2: Investigation (15 minutes - 2 hours)**

1. **Forensic Analysis:**
   - **Token Analysis:** Collect and analyze all tokens involved in bypass
     - Extract JWT claims, validate signatures manually
     - Identify if "none" algorithm accepted or signature validation bypassed
     - Check for payload manipulation (modified user_id, role, expiration)
   
   - **Access Pattern Analysis:**
     - Identify all API endpoints accessed by compromised tokens
     - Determine scope of unauthorized data access or operations
     - Check for data exfiltration or privilege escalation
   
   - **Attribution:**
     - Correlate IP addresses, geolocations, User-Agents
     - Identify if coordinated attack (multiple accounts, shared attack patterns)
     - Check threat intelligence for known attacker infrastructure

2. **Vulnerability Identification:**
```python
def analyze_authentication_vulnerability(incident_data):
    """Identify root cause of authentication bypass"""
    vulnerabilities = []
    
    # Check if expired tokens accepted
    if incident_data.get('expired_token_accepted'):
        vulnerabilities.append({
            'type': 'missing_expiration_validation',
            'severity': 'CRITICAL',
            'fix': 'Implement server-side expiration validation'
        })
    
    # Check if "none" algorithm accepted
    if incident_data.get('none_algorithm_accepted'):
        vulnerabilities.append({
            'type': 'algorithm_confusion',
            'severity': 'CRITICAL',
            'fix': 'Reject tokens with "none" or missing algorithm'
        })
    
    # Check if signature validation bypassed
    if incident_data.get('signature_bypass'):
        vulnerabilities.append({
            'type': 'signature_validation_failure',
            'severity': 'CRITICAL',
            'fix': 'Enforce server-side signature validation'
        })
    
    # Check if weak signing key
    if incident_data.get('weak_key_cracked'):
        vulnerabilities.append({
            'type': 'weak_signing_key',
            'severity': 'CRITICAL',
            'fix': 'Rotate to stronger signing algorithm (RS256 2048-bit)'
        })
    
    return vulnerabilities
```

**Phase 3: Remediation (2-4 hours)**

1. **Emergency Code Fixes:**
   - **Missing expiration validation:** Deploy server-side expiration checks
   - **Algorithm confusion:** Explicitly reject "none" algorithm, enforce allowlist
   - **Signature bypass:** Fix validation logic, ensure signature checked on every request
   - **Weak keys:** Emergency key rotation to stronger algorithm

```python
# Example emergency patch for expired token validation
def validate_jwt_with_expiration_fix(token, public_key):
    """Fixed JWT validation with server-side expiration enforcement"""
    try:
        payload = jwt.decode(
            token,
            public_key,
            algorithms=["RS256"],  # Explicit allowlist (no "none")
            options={
                "verify_signature": True,  # ALWAYS verify
                "verify_exp": True,        # CRITICAL: server-side expiration
                "require": ["exp", "iat", "sub"]  # Required claims
            }
        )
        
        # Additional validation: reject future expiration beyond max lifetime
        max_lifetime = timedelta(hours=1)
        if payload["exp"] > (datetime.utcnow() + max_lifetime).timestamp():
            raise jwt.InvalidTokenError("Token lifetime exceeds maximum allowed")
        
        return payload
    except jwt.InvalidTokenError as e:
        logging.error(f"JWT validation failed: {str(e)}")
        return None
```

2. **Emergency Key Rotation (if keys compromised):**
```python
def emergency_key_rotation_incident_response(incident_id):
    """Emergency JWT signing key rotation"""
    # Generate new key pair
    new_private_key, new_public_key = generate_rsa_keypair(bits=2048)
    new_key_id = f"incident-{incident_id}-{datetime.utcnow().strftime('%Y%m%d%H%M')}"
    
    # Store new key
    store_signing_key(key_id=new_key_id, 
                     private_key=new_private_key,
                     public_key=new_public_key,
                     status="active")
    
    # Immediately retire compromised key (no grace period)
    current_key_id = get_current_key_id()
    update_key_status(current_key_id, status="compromised")
    
    # Set new key as current
    set_current_signing_key(new_key_id)
    
    # Global token invalidation (all old tokens invalid)
    set_global_revocation_time(datetime.utcnow())
    
    # Force all users to re-authenticate
    require_global_reauthentication()
    
    logging.critical(f"Emergency key rotation complete - Incident {incident_id}")
    notify_incident_team({
        'action': 'emergency_key_rotation',
        'incident_id': incident_id,
        'old_key': current_key_id,
        'new_key': new_key_id,
        'impact': 'all_users_must_reauthenticate'
    })
```

3. **Defense-in-Depth Enhancements:**
   - Implement [[mitigations/authentication-monitoring|authentication monitoring]] for bypass patterns
   - Deploy [[mitigations/token-lifecycle-management|token lifecycle management]]
   - Add token binding to prevent token theft
   - Implement MFA for privileged operations

**Phase 4: Recovery (4-24 hours)**

1. **Gradual Service Restoration:**
   - Deploy authentication fixes to staging environment
   - Validate fixes with security team (manual testing + automated)
   - Deploy to production with gradual rollout (canary → full)
   - Monitor authentication success rates and failure patterns

2. **User Communication:**
   - Notify affected users of security incident
   - Explain required re-authentication
   - Provide guidance on securing accounts (password change if credential theft suspected)
   - Offer security support contact

**Phase 5: Post-Incident Actions**

1. **Regulatory Notifications (if required):**
   - GDPR breach notification within 72 hours (if EU user data accessed)
   - CCPA notification if California resident data compromised
   - Industry-specific regulations (HIPAA, PCI-DSS, etc.)

2. **Forensic Preservation:**
   - Preserve authentication logs for legal/compliance purposes
   - Document all compromised tokens, affected users, data accessed
   - Maintain chain of custody for evidence

3. **Lessons Learned:**
   - Update [[mitigations/api-authentication-security|API authentication security]] implementation
   - Enhance [[mitigations/authentication-monitoring|authentication monitoring]] rules
   - Conduct security training on authentication vulnerabilities
   - Red team validation of authentication fixes

**Incident Metrics to Track:**
- Time to detection (from first bypass to alert)
- Time to containment (from alert to token revocation)
- Time to remediation (from detection to patch deployment)
- Affected users count
- Unauthorized data accessed/operations performed
- False positive rate (legitimate users locked out)

### Adversarial Robustness Incident Response

**Incident Type:** Adversarial Example Attacks, Evasion Attacks, Backdoor Activation

**Detection Signals:**
- Anomaly detection flagging unusual input patterns or confidence scores
- Ensemble disagreement spikes (models producing conflicting predictions)
- Sudden increase in misclassifications on specific input categories
- Activation pattern anomalies suggesting backdoor triggers
- Physical-world attacks (adversarial stickers, patches causing systematic misclassification)

**Phase 1: Immediate Containment**

1. **Route Suspicious Predictions to Human Review:**
```python
def containment_for_adversarial_attack(incident_id, affected_categories):
    """Emergency containment for adversarial example attack"""
    # Enable aggressive fallback to human review
    for category in affected_categories:
        set_human_review_threshold(
            category=category,
            confidence_threshold=0.95,  # Very high bar for automated decisions
            anomaly_threshold=0.5,     # Lower threshold for flagging
            reason=f"incident_{incident_id}_containment"
        )
    
    # Enable ensemble disagreement monitoring
    enable_feature("ensemble_disagreement_monitoring", severity="aggressive")
    
    # Alert analysts
    notify_security_team({
        'incident_type': 'adversarial_attack',
        'affected_categories': affected_categories,
        'containment_action': 'human_review_fallback_enabled'
    })
```

2. **Apply Input Transformations:**
   - Enable defensive input preprocessing (JPEG compression, noise injection)
   - Apply randomized transformations to disrupt adversarial perturbations
   - See [[mitigations/input-validation-transformation]] for implementation

3. **Activate Circuit Breakers (if needed):**
   - For physical-world attacks: temporarily disable affected camera feeds or sensors
   - For critical failures: route to fallback system or safe-mode operation

**Phase 2: Investigation - Adversarial Example Database**

**Adversarial Example Collection and Analysis:**

```python
class AdversarialExampleDatabase:
    """
    Centralized repository for adversarial examples discovered during incidents
    
    Purpose:
    - Catalog discovered attack patterns for future defense
    - Feed adversarial training pipelines
    - Enable attack technique analysis
    - Support threat intelligence sharing
    """
    
    def __init__(self):
        self.db = connect_to_adversarial_example_db()
        self.embeddings = connect_to_vector_store()
    
    def log_adversarial_example(self, incident_id, adversarial_input, metadata):
        """Record discovered adversarial example"""
        example_id = generate_example_id()
        
        example_record = {
            'id': example_id,
            'incident_id': incident_id,
            'timestamp': datetime.now(),
            'input': adversarial_input,
            'attack_type': metadata.get('attack_type'),  # e.g., 'FGSM', 'PGD', 'physical'
            'target_class': metadata.get('target_class'),
            'model_prediction': metadata.get('model_prediction'),
            'ground_truth': metadata.get('ground_truth'),
            'perturbation_type': metadata.get('perturbation_type'),
            'l_infinity_norm': calculate_perturbation_magnitude(adversarial_input, metadata.get('clean_input')),
            'transferability': None,  # Populated later via testing
            'discovery_method': metadata.get('discovery_method'),  # e.g., 'automated_detection', 'user_report'
            'severity': metadata.get('severity', 'medium')
        }
        
        # Store in database
        self.db.insert(example_record)
        
        # Store embedding for similarity search
        embedding = embed(adversarial_input)
        self.embeddings.add(example_id, embedding)
        
        return example_id
    
    def test_transferability(self, example_id, model_variants):
        """Test if adversarial example transfers across model architectures"""
        example = self.db.get(example_id)
        
        transferability_results = []
        for model in model_variants:
            prediction = model.predict(example['input'])
            fooled = (prediction != example['ground_truth'])
            
            transferability_results.append({
                'model': model.name,
                'fooled': fooled,
                'confidence': model.get_confidence(example['input'])
            })
        
        # Update example record
        self.db.update(example_id, {'transferability': transferability_results})
        
        return transferability_results
    
    def query_similar_examples(self, new_input, top_k=10):
        """Find similar adversarial examples in database"""
        embedding = embed(new_input)
        similar_ids = self.embeddings.search(embedding, k=top_k)
        
        similar_examples = [self.db.get(ex_id) for ex_id in similar_ids]
        return similar_examples
    
    def export_for_training(self, filters=None):
        """Export adversarial examples for adversarial training pipeline"""
        # Query examples matching filters
        examples = self.db.query(filters) if filters else self.db.get_all()
        
        # Format for training
        training_set = [
            {
                'input': ex['input'],
                'label': ex['ground_truth'],  # Correct label, NOT attacker's target
                'attack_type': ex['attack_type']
            }
            for ex in examples
        ]
        
        return training_set
```

**Attack Pattern Analysis:**

```python
def analyze_attack_patterns(incident_id):
    """Analyze adversarial attack patterns to inform defenses"""
    # Collect all adversarial examples from incident
    examples = adversarial_db.query(incident_id=incident_id)
    
    analysis = {
        'attack_types': Counter(ex['attack_type'] for ex in examples),
        'targeted_categories': Counter(ex['target_class'] for ex in examples),
        'perturbation_magnitudes': [ex['l_infinity_norm'] for ex in examples],
        'transferability_rate': calculate_transferability_rate(examples),
        'physical_world_attacks': [ex for ex in examples if ex['perturbation_type'] == 'physical']
    }
    
    # Identify if coordinated campaign
    if len(examples) > 100 and analysis['attack_types'].most_common(1)[0][1] > 80:
        analysis['coordinated_campaign'] = True
        analysis['primary_attack_type'] = analysis['attack_types'].most_common(1)[0][0]
    
    return analysis
```

**Phase 3: Remediation - Dynamic Defense Adaptation**

**Adaptive Defense Updates Based on Incident Analysis:**

```python
class DynamicDefenseAdaptation:
    """
    Dynamically update defensive controls based on discovered attacks
    """
    
    def adapt_defenses_post_incident(self, incident_analysis):
        """Update defenses based on incident learnings"""
        adaptations = []
        
        # 1. Update Adversarial Training Dataset
        if incident_analysis.get('adversarial_examples'):
            new_training_examples = adversarial_db.export_for_training(
                filters={'incident_id': incident_analysis['incident_id']}
            )
            
            # Schedule model retraining with new adversarial examples
            schedule_adversarial_training_update(
                new_examples=new_training_examples,
                priority='high' if incident_analysis.get('severity') == 'critical' else 'normal'
            )
            
            adaptations.append({
                'type': 'adversarial_training_update',
                'examples_added': len(new_training_examples),
                'attack_types': incident_analysis['attack_types']
            })
        
        # 2. Update Input Transformation Pipeline
        if 'perturbation_type' in incident_analysis:
            if incident_analysis['perturbation_type'] == 'high_frequency':
                # Add/strengthen JPEG compression
                update_preprocessing_config(
                    'jpeg_compression_quality', 
                    new_value=70  # More aggressive
                )
                adaptations.append({'type': 'preprocessing_update', 'change': 'jpeg_compression_strengthened'})
            
            elif incident_analysis['perturbation_type'] == 'physical':
                # Add physical transformation robustness
                update_preprocessing_config(
                    'random_transformations',
                    new_value=['rotation', 'scale', 'brightness', 'viewing_angle']
                )
                adaptations.append({'type': 'preprocessing_update', 'change': 'physical_transformations_added'})
        
        # 3. Update Anomaly Detection Rules
        attack_patterns = extract_attack_signatures(incident_analysis['adversarial_examples'])
        for pattern in attack_patterns:
            anomaly_detector.add_rule(
                rule_type='adversarial_pattern',
                pattern=pattern,
                threshold=0.8,
                description=f"Pattern learned from incident {incident_analysis['incident_id']}"
            )
        
        adaptations.append({
            'type': 'anomaly_detection_update',
            'patterns_added': len(attack_patterns)
        })
        
        # 4. Adjust Confidence Thresholds (if model showed poor calibration)
        if incident_analysis.get('high_confidence_misclassifications'):
            # Model too confident on adversarial examples
            for category in incident_analysis['affected_categories']:
                update_confidence_threshold(
                    category=category,
                    new_threshold=0.85,  # Increase from 0.7
                    reason='poor_calibration_on_adversarial_examples'
                )
            
            adaptations.append({
                'type': 'confidence_threshold_adjustment',
                'categories': incident_analysis['affected_categories']
            })
        
        # 5. Update Ensemble Configuration (if needed)
        if incident_analysis.get('transferability_rate', 1.0) < 0.5:
            # Low transferability suggests ensemble diversity could help
            recommendation = "Consider adding diverse model architectures to ensemble"
            adaptations.append({
                'type': 'ensemble_recommendation',
                'recommendation': recommendation
            })
        
        # Log all adaptations
        log_defense_adaptations(incident_analysis['incident_id'], adaptations)
        
        return adaptations

def extract_attack_signatures(adversarial_examples):
    """Extract statistical signatures of attack patterns for detection rules"""
    signatures = []
    
    for example in adversarial_examples:
        signature = {
            'input_characteristics': {
                'high_frequency_noise': analyze_frequency_spectrum(example['input']),
                'unusual_pixel_patterns': detect_pixel_pattern_anomalies(example['input']),
                'statistical_outliers': calculate_statistical_features(example['input'])
            },
            'confidence_pattern': example.get('model_confidence'),
            'prediction_entropy': example.get('prediction_entropy')
        }
        signatures.append(signature)
    
    return signatures
```

**Phase 4: Model Replacement (if needed)**

**Emergency Model Replacement for Severe Compromise:**

```python
def emergency_model_replacement(incident_id, reason, replacement_strategy):
    """
    Replace compromised or severely vulnerable model
    
    Strategies:
    - 'rollback': Revert to previous stable model version
    - 'hotfix': Deploy rapidly retrained model with adversarial examples
    - 'ensemble': Switch to ensemble-based prediction for robustness
    """
    logging.critical(f"EMERGENCY MODEL REPLACEMENT INITIATED: {reason}")
    
    if replacement_strategy == 'rollback':
        # Rollback to last known good version
        previous_version = get_previous_stable_model_version()
        deploy_model_version(previous_version, reason=f"incident_{incident_id}_rollback")
        
        replacement_details = {
            'strategy': 'rollback',
            'new_version': previous_version,
            'limitations': 'May lack recent training data updates'
        }
    
    elif replacement_strategy == 'hotfix':
        # Rapid adversarial training on discovered examples
        adversarial_examples = adversarial_db.export_for_training(
            filters={'incident_id': incident_id}
        )
        
        # Emergency fine-tune current model
        hotfix_model = fine_tune_with_adversarial_examples(
            base_model=get_current_model(),
            adversarial_examples=adversarial_examples,
            epochs=3,  # Quick iteration
            validation_required=True
        )
        
        # Validate before deployment
        if validate_hotfix_model(hotfix_model, incident_id):
            deploy_model(hotfix_model, version=f"hotfix-{incident_id}")
            replacement_details = {
                'strategy': 'hotfix',
                'adversarial_examples_count': len(adversarial_examples),
                'fine_tune_epochs': 3
            }
        else:
            # Fallback to rollback if hotfix fails validation
            return emergency_model_replacement(incident_id, reason, 'rollback')
    
    elif replacement_strategy == 'ensemble':
        # Deploy ensemble-based inference
        ensemble_models = load_diverse_model_ensemble()
        deploy_ensemble(ensemble_models)
        
        replacement_details = {
            'strategy': 'ensemble',
            'ensemble_size': len(ensemble_models),
            'aggregation': 'majority_vote'
        }
    
    # Audit log
    audit_log.record({
        'action': 'emergency_model_replacement',
        'incident_id': incident_id,
        'reason': reason,
        'details': replacement_details,
        'timestamp': datetime.now()
    })
    
    # Notify stakeholders
    notify_incident_team({
        'event': 'model_replaced',
        'incident_id': incident_id,
        'strategy': replacement_strategy,
        'details': replacement_details
    })
    
    return replacement_details

def validate_hotfix_model(model, incident_id):
    """Validate hotfix model before deployment"""
    # Test on adversarial examples from incident
    incident_examples = adversarial_db.query(incident_id=incident_id)
    
    success_count = 0
    for example in incident_examples:
        prediction = model.predict(example['input'])
        if prediction == example['ground_truth']:
            success_count += 1
    
    robustness_rate = success_count / len(incident_examples)
    
    # Also test clean accuracy doesn't degrade
    clean_accuracy = evaluate_on_validation_set(model)
    baseline_accuracy = get_baseline_accuracy()
    
    # Pass if adversarial robustness improved AND clean accuracy maintained
    passed = (robustness_rate > 0.90 and clean_accuracy >= baseline_accuracy - 0.02)
    
    return passed
```

**Integration with Model Version Management:**

See [[mitigations/model-rollback-procedures]] for detailed model replacement workflows and rollback procedures.

**Continuous Improvement Cycle:**

```python
def continuous_defense_improvement():
    """
    Ongoing defense improvement based on adversarial example database
    """
    # Weekly: Review new adversarial examples
    weekly_examples = adversarial_db.query(
        filters={'timestamp': {'$gte': datetime.now() - timedelta(days=7)}}
    )
    
    if len(weekly_examples) > 10:
        # Trigger adversarial training update
        schedule_adversarial_training_update(
            new_examples=weekly_examples,
            priority='normal'
        )
    
    # Monthly: Analyze attack trends
    monthly_analysis = analyze_monthly_attack_trends()
    if monthly_analysis['emerging_attack_types']:
        # Research and deploy defenses for new attack types
        research_new_defenses(monthly_analysis['emerging_attack_types'])
    
    # Quarterly: Full robustness evaluation
    quarterly_robustness_report = evaluate_comprehensive_robustness()
    if quarterly_robustness_report['vulnerabilities']:
        # Plan defensive improvements
        prioritize_vulnerability_remediation(quarterly_robustness_report)
```

> Source: [[techniques/adversarial-robustness]] (extracted from responsive controls)

## Limitations & Trade-offs

**Limitations:**

- **Reactive nature:** Incident response is inherently reactive; cannot prevent all incidents
- **False positives:** Automated containment may suspend legitimate users
- **Patch lag:** Time to develop and deploy patches creates vulnerability window
- **Coordination challenges:** Cross-functional response requires significant coordination overhead

**Trade-offs:**

- **Speed vs. Accuracy:** Rapid containment may have false positives; careful analysis takes time
- **Automation vs. Oversight:** Automated responses are fast but may lack context; manual review adds latency
- **Transparency vs. Security:** Public disclosure helps community but may inform attackers
- **Availability vs. Security:** Emergency rollbacks/circuit breakers improve security but degrade service

## Testing & Validation

**Incident response drills:**

1. **Tabletop exercises:** Quarterly simulated incidents to test playbooks
2. **Red team exercises:** Coordinate with [[mitigations/red-team-continuous-testing|red team]] to trigger realistic incidents
3. **Chaos engineering:** Intentionally inject failures to test detection and response
4. **Post-mortem reviews:** After every incident, validate response effectiveness

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Addressing GCG-style automated jailbreak vulnerability requires multi-layered defensive strategy. Rapid deployment of fine-tuning updates to patch discovered jailbreak vulnerabilities. Automated account suspension—immediately suspend accounts generating repeated policy violations or exhibiting jailbreak patterns. Incident response procedures—documented playbook for responding to large-scale jailbreak campaigns."
> — [[sources/bibliography#Generative AI Security]], p. 169

## Related

- **Mitigates**: [[techniques/jailbreak-policy-bypass]], [[techniques/prompt-injection]], [[techniques/system-prompt-leakage]], [[techniques/sensitive-info-disclosure]], [[techniques/agent-goal-hijack]], [[techniques/unsafe-tool-invocation]]
- **Combined with**: [[mitigations/anomaly-detection-architecture]] (detection), [[mitigations/red-team-continuous-testing]] (proactive discovery), [[mitigations/ai-threat-intelligence-sharing]] (coordinated response)
