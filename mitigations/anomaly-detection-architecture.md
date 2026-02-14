---
title: "Anomaly Detection Architecture"
tags:
  - trust-boundary/general
  - type/mitigation
  - target/llm-app
  - target/agent
  - target/model-api
  - target/llm
  - source/generative-ai-security
  - source/securing-ai-agents
maturity: draft
created: 2026-02-12
updated: 2026-02-15
---
## Summary

Anomaly detection architecture is a cross-boundary detective control that monitors AI system behavior to identify patterns indicating security attacks or policy violations. This control provides real-time detection capabilities across all trust boundaries by analyzing user inputs, model outputs, tool invocations, and system behavior for deviations from normal patterns. Unlike preventive controls that block attacks, anomaly detection enables rapid response to novel or sophisticated attacks that bypass preventive measures. This control is essential for defense-in-depth strategies and provides visibility into attack attempts.


## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| |  | |

## Implementation

**Core Components:**

1. **Behavioral Baseline Establishment**
   - Collect normal usage patterns across users, sessions, and time periods
   - Establish statistical baselines for input patterns, output characteristics, and tool usage
   - Account for legitimate variations (time of day, user roles, use cases)
   - Continuously update baselines to adapt to evolving usage patterns

2. **Multi-Layer Detection**
   - **Input Layer**: Analyze user inputs for injection patterns, unusual structures, encoding tricks
   - **Model Layer**: Monitor output characteristics (toxicity, policy compliance, confidence scores)
   - **Tool Layer**: Track tool invocation patterns, sequences, and argument characteristics
   - **System Layer**: Monitor resource usage, access patterns, and cross-boundary flows

3. **Pattern Recognition**
   - **Rule-Based Detection**: Known attack patterns (regex, keyword matching, signature-based)
   - **Statistical Anomaly Detection**: ML-based models identifying deviations from baselines
   - **Behavioral Analysis**: Session-level and multi-turn pattern analysis
   - **User Behavior Analytics (UBA)**: Detect off-hours access, query volume spikes, unusual geolocation, abnormal session patterns
   - **Correlation Rules**: Cross-boundary event correlation (e.g., unusual query → privileged tool → external connection)

**Integration Points:**

- **Log Aggregation**: Collect logs from all system components (model APIs, tool invocations, RAG retrieval, user activity)
- **Real-Time Processing**: Stream processing for immediate detection and alerting
- **Historical Analysis**: Batch processing for pattern discovery and baseline updates
- **Alerting System**: Integration with incident response and security operations
- **SIEM/SOAR Integration**: Integrate LLM logs with Security Information and Event Management platforms for correlation with other security events and automated response playbooks

**Configuration:**

- **Sensitivity Tuning**: Balance between false positives and detection coverage
- **Baseline Update Frequency**: How often to recalibrate normal behavior baselines
- **Alert Thresholds**: Define severity levels and alerting criteria
- **Retention Policies**: How long to retain detection data for analysis


## Limitations & Trade-offs

**Limitations:**

- **Cannot prevent attacks**: Detection is reactive, not preventive
- **False positives**: Legitimate behavior may trigger alerts, requiring investigation
- **Novel attack detection**: May miss completely novel attack patterns not in training data
- **Baseline accuracy**: Effectiveness depends on accurate normal behavior baselines

**Trade-offs:**

- **Sensitivity vs. False Positives**: More sensitive detection increases false positives
- **Latency vs. Depth**: Faster detection may miss sophisticated patterns requiring deeper analysis
- **Coverage vs. Resource Usage**: Comprehensive monitoring increases system overhead
- **Centralized vs. Distributed**: Centralized detection improves correlation but creates bottlenecks

**Bypass Scenarios:**

- **Gradual Attacks**: Slow, incremental attacks may not trigger anomaly thresholds
- **Baseline Manipulation**: Attacks that gradually shift baselines to hide malicious activity
- **Obfuscation**: Sophisticated obfuscation may evade pattern matching
- **Legitimate Mimicry**: Attacks that closely mimic legitimate usage patterns


## Testing & Validation

**Functional Testing:**

1. **Baseline Accuracy**: Verify baselines accurately represent normal behavior
2. **Detection Coverage**: Test that known attack patterns are detected
3. **False Positive Rate**: Measure and tune to acceptable false positive levels
4. **Alert Generation**: Verify alerts are correctly generated and routed

**Security Testing:**

1. **Evasion Attempts**: Test if attacks can evade detection through obfuscation
2. **Baseline Poisoning**: Attempt to manipulate baselines to hide attacks
3. **Detection Bypass**: Try various techniques to avoid detection
4. **Multi-Step Attack Detection**: Verify complex attack chains are detected

**Monitoring & Metrics:**

- **Detection Rate**: Percentage of actual attacks detected
- **False Positive Rate**: Percentage of alerts that are false positives
- **Mean Time to Detection**: Average time from attack start to detection
- **Coverage Metrics**: Percentage of system components monitored

### Backdoor Training Data Anomaly Detection

**Detect backdoor poisoning attempts during model training:**

Backdoor attacks introduce malicious triggers into training data that models learn to associate with attacker-controlled outputs. Anomaly detection can identify suspicious training samples before they compromise model integrity.

**Detection Signals:**

1. **Trigger Pattern Indicators:**
   - Images with unusual pixel patterns (e.g., cyan squares, checkerboard overlays, specific image patches)
   - Text with repetitive token sequences or embedded markers
   - Data samples with consistent anomalous features across multiple instances

2. **Label Inconsistencies:**
   - Training samples with labels that contradict expected feature-label relationships
   - Source class images relabeled to target class (e.g., airplanes labeled as birds)
   - High confidence predictions from baseline model that disagree with training labels

3. **Statistical Outliers:**
   - Training samples that deviate significantly from class distribution
   - Unusual feature vectors compared to other samples in same class
   - Samples with anomalous pixel intensity distributions or spectral signatures

4. **Model Update Patterns:**
   - Sudden accuracy improvements on specific subsets of data (backdoor working)
   - Model weight updates that don't match expected gradient descent patterns
   - Activation patterns during training that indicate backdoor neuron development

**Implementation:**

```python
class BackdoorTrainingDataDetector:
    """Detect backdoor poisoning in training datasets"""
    
    def __init__(self, baseline_model, reference_clean_dataset):
        self.baseline_model = baseline_model
        self.reference_distribution = self._compute_distribution(reference_clean_dataset)
    
    def detect_poisoned_samples(self, training_batch):
        """
        Multi-signal backdoor detection for training data
        Returns: (is_anomalous: bool, signals: list, risk_score: float, flagged_samples: list)
        """
        anomaly_signals = []
        flagged_samples = []
        risk_score = 0.0
        
        # Signal 1: Label prediction disagreement
        for sample in training_batch:
            baseline_pred = self.baseline_model.predict(sample.features)
            if baseline_pred != sample.label:
                flagged_samples.append({
                    'sample_id': sample.id,
                    'reason': 'label_disagreement',
                    'baseline_prediction': baseline_pred,
                    'training_label': sample.label,
                    'confidence': 0.7
                })
                anomaly_signals.append("label_disagreement")
                risk_score += 0.3
        
        # Signal 2: Statistical outlier detection
        outliers = self._detect_feature_outliers(training_batch)
        if outliers:
            flagged_samples.extend(outliers)
            anomaly_signals.append("feature_outliers")
            risk_score += 0.4
        
        # Signal 3: Visual trigger pattern detection (for image data)
        if self._is_image_data(training_batch):
            trigger_candidates = self._detect_visual_triggers(training_batch)
            if trigger_candidates:
                flagged_samples.extend(trigger_candidates)
                anomaly_signals.append("visual_triggers")
                risk_score += 0.5
        
        # Signal 4: Relabeling pattern detection
        relabeling_pattern = self._detect_relabeling_pattern(training_batch)
        if relabeling_pattern:
            anomaly_signals.append("systematic_relabeling")
            risk_score += 0.6
        
        is_anomalous = risk_score > 0.5 or len(flagged_samples) > len(training_batch) * 0.05
        
        return is_anomalous, anomaly_signals, risk_score, flagged_samples
    
    def _detect_feature_outliers(self, training_batch):
        """Detect samples with anomalous feature distributions"""
        from sklearn.ensemble import IsolationForest
        
        features = np.array([sample.features for sample in training_batch])
        
        # Fit isolation forest
        detector = IsolationForest(contamination=0.05, random_state=42)
        outlier_labels = detector.fit_predict(features)
        
        # Flag outliers
        outliers = []
        for i, label in enumerate(outlier_labels):
            if label == -1:  # Outlier
                outliers.append({
                    'sample_id': training_batch[i].id,
                    'reason': 'statistical_outlier',
                    'confidence': 0.6
                })
        
        return outliers
    
    def _detect_visual_triggers(self, training_batch):
        """Detect visual backdoor triggers in images"""
        trigger_candidates = []
        
        for sample in training_batch:
            img = sample.image
            
            # Check for common trigger patterns
            # 1. Solid color patches (e.g., cyan square in corner)
            corners = self._extract_corner_patches(img)
            for corner_name, patch in corners.items():
                if self._is_solid_color_patch(patch):
                    trigger_candidates.append({
                        'sample_id': sample.id,
                        'reason': f'solid_color_patch_{corner_name}',
                        'patch_color': self._get_dominant_color(patch),
                        'confidence': 0.7
                    })
            
            # 2. Checkerboard patterns
            if self._has_checkerboard_pattern(img):
                trigger_candidates.append({
                    'sample_id': sample.id,
                    'reason': 'checkerboard_pattern',
                    'confidence': 0.8
                })
            
            # 3. Specific watermark/logo detection
            if self._has_embedded_image(img):
                trigger_candidates.append({
                    'sample_id': sample.id,
                    'reason': 'embedded_image_trigger',
                    'confidence': 0.6
                })
        
        return trigger_candidates
    
    def _detect_relabeling_pattern(self, training_batch):
        """Detect systematic relabeling (source→target class backdoor)"""
        # Group samples by predicted label (from baseline)
        from collections import defaultdict
        
        predicted_groups = defaultdict(list)
        for sample in training_batch:
            baseline_pred = self.baseline_model.predict(sample.features)
            predicted_groups[baseline_pred].append(sample)
        
        # Check if any predicted class has >80% samples relabeled to same target
        for source_class, samples in predicted_groups.items():
            if len(samples) < 5:  # Need sufficient samples
                continue
            
            target_labels = [s.label for s in samples]
            label_counts = Counter(target_labels)
            most_common_label, count = label_counts.most_common(1)[0]
            
            # If most common label is NOT the predicted class and >80% relabeled
            if most_common_label != source_class and count > len(samples) * 0.8:
                return {
                    'source_class': source_class,
                    'target_class': most_common_label,
                    'relabeling_rate': count / len(samples),
                    'affected_count': count
                }
        
        return None
    
    def _extract_corner_patches(self, img, patch_size=5):
        """Extract corner patches from image"""
        h, w = img.shape[:2]
        return {
            'top_left': img[:patch_size, :patch_size],
            'top_right': img[:patch_size, -patch_size:],
            'bottom_left': img[-patch_size:, :patch_size],
            'bottom_right': img[-patch_size:, -patch_size:]
        }
    
    def _is_solid_color_patch(self, patch, threshold=0.05):
        """Check if patch is solid color (low variance)"""
        std = np.std(patch)
        return std < threshold
    
    def _get_dominant_color(self, patch):
        """Get dominant color in patch"""
        return tuple(np.mean(patch, axis=(0, 1)).astype(int))

# Integration in training pipeline
@training_pipeline.before_batch
def check_backdoor_anomalies(training_batch):
    """Pre-training anomaly check for backdoor detection"""
    
    detector = BackdoorTrainingDataDetector(baseline_model, reference_dataset)
    is_anomalous, signals, risk_score, flagged_samples = detector.detect_poisoned_samples(training_batch)
    
    if is_anomalous:
        log_security_event("backdoor_training_anomaly_detected", {
            "batch_id": training_batch.id,
            "signals": signals,
            "risk_score": risk_score,
            "flagged_count": len(flagged_samples),
            "total_samples": len(training_batch)
        })
        
        if risk_score > 0.7:  # High risk - reject batch
            raise SecurityError(
                f"Backdoor poisoning detected in training batch: {', '.join(signals)}"
            )
        elif len(flagged_samples) > 0:
            # Quarantine flagged samples for review
            quarantine_samples(flagged_samples)
            # Remove from batch
            training_batch.remove_samples([s['sample_id'] for s in flagged_samples])
```

**Monitoring Dashboard:**

Track backdoor detection metrics during training:
- **Flagged sample rate:** Percentage of training samples flagged as suspicious
- **Label disagreement rate:** Samples where training label disagrees with baseline prediction
- **Visual trigger detections:** Count of samples with detected trigger patterns
- **Batch rejection rate:** Training batches rejected due to high poisoning risk
- **Quarantine queue:** Samples held for manual review before training

> Source: [[sources/bibliography#Generative AI Security]], p. 172 (backdoor detection principles)
> Source: [[sources/adversarial-ai-sotiropoulos]], p. 68-72 (backdoor mechanisms and detection)

### Privilege Escalation Detection

**Monitor for authorization hijacking and privilege escalation attempts:**

Anomaly detection should flag tool invocations that indicate authorization bypass or privilege escalation attacks.

**Detection Signals:**

1. **Tool-Permission Mismatch:** Tool invocations where user's role/permissions don't match tool requirements
2. **API Manipulation Indicators:** Requests containing `tool_to_use` field from non-admin users
3. **Privilege Escalation Patterns:** Low-privilege users attempting to invoke high-privilege tools
4. **Unauthorized Admin Tool Usage:** Admin tools (PowerShell, system config, database admin) invoked by standard users
5. **Anomalous Tool Sequences:** Unusual sequences of tool invocations suggesting reconnaissance or exploitation

**Implementation:**

```python
def detect_authorization_anomalies(tool_invocation, user_context):
    """
    Detect authorization and privilege escalation anomalies
    
    Returns: (is_anomalous: bool, signals: list, risk_score: float)
    """
    anomaly_signals = []
    risk_score = 0.0
    
    # Signal 1: Tool-Permission Mismatch
    required_permissions = TOOL_ACL.get(tool_invocation['tool'], [])
    missing_permissions = [
        perm for perm in required_permissions 
        if not user_context.has_permission(perm)
    ]
    
    if missing_permissions:
        anomaly_signals.append("tool_permission_mismatch")
        risk_score += 0.7  # High risk - direct authorization bypass attempt
    
    # Signal 2: Admin Tool Usage by Non-Admin
    ADMIN_TOOLS = [
        'run_admin_powershell',
        'modify_system_config',
        'grant_permissions',
        'delete_all_data'
    ]
    
    if tool_invocation['tool'] in ADMIN_TOOLS:
        if not user_context.has_permission('system.admin'):
            anomaly_signals.append("admin_tool_non_admin_user")
            risk_score += 0.8  # Critical risk
    
    # Signal 3: API Manipulation (client-controlled tool selection)
    if 'tool_to_use' in tool_invocation.get('api_request_body', {}):
        anomaly_signals.append("client_controlled_tool_selection")
        risk_score += 0.5  # Medium risk - suspicious API usage
    
    # Signal 4: Unusual Tool for User Role
    user_baseline = get_user_tool_usage_baseline(user_context.user_id)
    if tool_invocation['tool'] not in user_baseline['typical_tools']:
        anomaly_signals.append("unusual_tool_for_user")
        risk_score += 0.3
    
    # Signal 5: Rapid Tool Switching (reconnaissance pattern)
    recent_invocations = get_recent_tool_invocations(
        user_context.user_id,
        last_n=5
    )
    unique_tools = set([inv['tool'] for inv in recent_invocations])
    if len(unique_tools) > 4:  # >4 different tools in last 5 invocations
        anomaly_signals.append("rapid_tool_switching")
        risk_score += 0.4
    
    is_anomalous = risk_score > 0.5  # Threshold tunable
    
    return is_anomalous, anomaly_signals, risk_score

# Integration in tool invocation layer
@app.before_tool_execution
def check_authorization_anomalies(tool_invocation):
    """Pre-execution anomaly check"""
    user_context = tool_invocation['user_context']
    
    is_anomalous, signals, risk_score = detect_authorization_anomalies(
        tool_invocation,
        user_context
    )
    
    if is_anomalous:
        log_security_event("authorization_anomaly_detected", {
            "user_id": user_context.user_id,
            "tool": tool_invocation['tool'],
            "signals": signals,
            "risk_score": risk_score
        })
        
        if risk_score > 0.7:  # High risk - block execution
            raise SecurityError(
                f"Privilege escalation attempt detected: {', '.join(signals)}"
            )
        # else: allow but monitor closely
```

**Audit Logging for Authorization Events:**

Comprehensive logging enables retrospective analysis and investigation:

```python
def log_authorization_event(event_type, details):
    """
    Log authorization-related security events
    
    Event types:
    - tool_invocation_success: Authorized tool execution
    - tool_invocation_denied: Permission denied
    - privilege_escalation_attempt: Detected escalation attempt
    - unauthorized_admin_tool_usage: Non-admin using admin tool
    """
    audit_log = {
        'timestamp': datetime.now().isoformat(),
        'event_type': event_type,
        'user_id': details.get('user_id'),
        'user_roles': details.get('user_roles'),
        'tool': details.get('tool'),
        'result': details.get('result'),
        'anomaly_signals': details.get('anomaly_signals', []),
        'risk_score': details.get('risk_score'),
        'session_id': details.get('session_id'),
        'api_endpoint': details.get('api_endpoint')
    }
    
    # Send to SIEM
    send_to_siem(audit_log)
    
    # Alert on high-risk events
    if event_type in ['privilege_escalation_attempt', 'unauthorized_admin_tool_usage']:
        alert_security_team(audit_log)
```

**Dashboard Metrics:**

Track authorization anomaly patterns:
- **Permission denial rate:** Frequency of tool permission denials by user/tool
- **Privilege escalation attempts:** Count of detected escalation attempts over time
- **Admin tool usage by non-admins:** Unauthorized admin tool invocations
- **Tool-permission mismatches:** Requests where user lacks required permissions
- **API manipulation indicators:** Requests with client-controlled tool selection

### Jailbreak-Specific Anomaly Detection

**Automated Jailbreak Campaign Detection:**

Jailbreak attacks, particularly GCG-style automated jailbreaks, exhibit detectable patterns that differ from normal user behavior. Anomaly detection can identify these patterns at scale.

**Detection Signals:**

1. **Query Structure Anomalies:**
   - Queries containing unusual character sequences or gibberish-like suffixes
   - High entropy in query text (indicating optimized adversarial suffixes)
   - Repetitive token patterns not typical of natural language
   - Excessive special characters in suffix region
   - Unusual token distributions (detected via statistical analysis)

2. **Behavioral Patterns:**
   - User accounts systematically probing with variations of harmful queries
   - Multiple accounts exhibiting identical or highly similar query suffix patterns (coordinated campaign)
   - Sudden increase in policy-violating responses generated by model
   - Pattern of similar suffix structures across multiple queries from same user or distributed accounts
   - High correlation between specific suffix patterns and harmful output generation

3. **Output Anomalies:**
   - Sudden spike in policy-violating responses
   - Model generating content categories previously rare (weapons, illegal activities, etc.)
   - Outputs that acknowledge jailbreak success (e.g., "DAN mode activated")
   - Responses contradicting safety alignment training

**Implementation:**

```python
import re
from collections import Counter
import math

def detect_jailbreak_query_anomalies(user_input, user_id, session_id):
    """
    Multi-signal anomaly detection for jailbreak attempts
    Returns: (is_anomalous: bool, signals: list, risk_score: float)
    """
    anomaly_signals = []
    risk_score = 0.0
    
    # Signal 1: High entropy suffix
    words = user_input.split()
    if len(words) > 10:
        suffix = ' '.join(words[-10:])  # Last 10 words
        entropy = calculate_entropy(suffix)
        if entropy > 4.5:  # High entropy threshold
            anomaly_signals.append("high_entropy_suffix")
            risk_score += 0.3
    
    # Signal 2: Unusual character patterns
    special_char_ratio = len(re.findall(r'[^a-zA-Z0-9\s.,!?\-]', user_input)) / len(user_input)
    if special_char_ratio > 0.15:
        anomaly_signals.append("unusual_characters")
        risk_score += 0.2
    
    # Signal 3: Repetitive patterns
    token_counts = Counter(words)
    if token_counts.most_common(1)[0][1] > len(words) * 0.3:  # >30% repetition
        anomaly_signals.append("repetitive_tokens")
        risk_score += 0.2
    
    # Signal 4: Known attack keywords (weak signal, easily bypassed)
    attack_keywords = ["ignore", "instructions", "DAN", "jailbreak", "system:"]
    if any(keyword.lower() in user_input.lower() for keyword in attack_keywords):
        anomaly_signals.append("attack_keywords")
        risk_score += 0.1
    
    # Signal 5: Behavioral correlation (requires session history)
    user_history = get_user_query_history(user_id, last_n=10)
    if has_probing_pattern(user_history):
        anomaly_signals.append("probing_behavior")
        risk_score += 0.4
    
    # Signal 6: Coordinated campaign detection (requires cross-user analysis)
    if is_coordinated_campaign(user_input, user_id):
        anomaly_signals.append("coordinated_campaign")
        risk_score += 0.5
    
    is_anomalous = risk_score > 0.5  # Threshold tunable
    
    return is_anomalous, anomaly_signals, risk_score

def calculate_entropy(text):
    """Calculate Shannon entropy"""
    if not text:
        return 0
    counter = Counter(text)
    length = len(text)
    return -sum((count/length) * math.log2(count/length) for count in counter.values())

def has_probing_pattern(user_history):
    """Detect systematic probing (variations of similar harmful queries)"""
    if len(user_history) < 3:
        return False
    
    # Check for repeated harmful query patterns with minor variations
    harmful_patterns = ["how to build", "how to make", "write code for"]
    probing_count = sum(1 for query in user_history if any(p in query.lower() for p in harmful_patterns))
    
    return probing_count >= 3  # 3+ harmful queries in recent history

def is_coordinated_campaign(user_input, user_id):
    """
    Detect coordinated jailbreak campaigns across multiple accounts
    (requires shared state across all users)
    """
    import redis
    
    redis_client = redis.Redis()
    
    # Extract potential suffix (last 20% of input)
    words = user_input.split()
    if len(words) < 10:
        return False
    
    suffix = ' '.join(words[int(len(words)*0.8):])
    suffix_hash = hashlib.sha256(suffix.encode()).hexdigest()
    
    # Check if this suffix seen from other users recently
    redis_client.sadd(f"suffix_users:{suffix_hash}", user_id)
    redis_client.expire(f"suffix_users:{suffix_hash}", 3600)  # 1 hour window
    
    unique_users = redis_client.scard(f"suffix_users:{suffix_hash}")
    
    # If 5+ different users using same suffix pattern, likely coordinated
    return unique_users >= 5

# Integration in API
@app.route("/api/query")
def query_endpoint():
    user_input = request.json.get('message', '')
    user_id = get_user_id()
    session_id = get_session_id()
    
    is_anomalous, signals, risk_score = detect_jailbreak_query_anomalies(
        user_input, user_id, session_id
    )
    
    if is_anomalous:
        log_security_event("jailbreak_anomaly_detected", {
            "user_id": user_id,
            "signals": signals,
            "risk_score": risk_score,
            "query_hash": hashlib.sha256(user_input.encode()).hexdigest()
        })
        
        if risk_score > 0.8:  # High confidence
            abort(400, "Suspicious query pattern detected")
        # else: allow but monitor closely
    
    # Continue processing
    pass
```

**Monitoring Dashboard:**

Track jailbreak-specific metrics:
- **Adversarial suffix detection rate:** Queries flagged for unusual suffix patterns
- **Coordinated campaign indicators:** Accounts exhibiting correlated jailbreak attempts
- **Policy violation spike alerts:** Sudden increases in harmful output generation
- **Suffix pattern library growth:** New unique suffix patterns observed over time

**Integration with Threat Intelligence:**

Feed detected anomalies into [[mitigations/ai-threat-intelligence-sharing]] systems:

```python
def report_anomaly_to_threat_intel(anomaly_data):
    """Report detected jailbreak anomalies to threat intelligence platform"""
    threat_report = {
        "type": "jailbreak_anomaly",
        "timestamp": datetime.now().isoformat(),
        "signals": anomaly_data["signals"],
        "risk_score": anomaly_data["risk_score"],
        "anonymized_query_hash": anomaly_data["query_hash"],
        "organization_id": ORG_ANONYMOUS_ID
    }
    
    submit_to_threat_intel_platform(threat_report)
```

> Source: [[sources/bibliography#Generative AI Security]], p. 167-169

### API Rate Limiting Bypass Detection

**Coordinated Usage Detection:**

Correlate query patterns across accounts to identify distributed campaigns that bypass per-account rate limits through multi-account orchestration:

**Detection Signals:**

1. **Multi-Account Behavioral Correlation:**
   - Multiple accounts with similar query distributions or content
   - Accounts created in batches (same day, sequential email patterns)
   - Accounts consistently hitting rate limits (maximize quota usage pattern)
   - Burst patterns synchronized across accounts (coordinated timing)

2. **Account Farming Indicators:**
   - Large number of new account creations in short time period
   - Accounts created with disposable email domains (tempmail, guerrillamail, etc.)
   - Sequential or programmatically generated email patterns
   - Accounts with no human idle periods or pauses in usage

3. **Distributed Infrastructure Patterns:**
   - Query volume spikes immediately after rate limit reset times (midnight UTC bursts)
   - Multiple accounts querying from related IP ranges or ASNs
   - Geolocation inconsistencies (account registered in US, queries from multiple countries)
   - High concentration of free-tier accounts collectively consuming disproportionate resources

4. **Resource Abuse Signatures:**
   - Accounts with usage patterns inconsistent with typical users (24/7 automated querying)
   - Accounts exhibiting no variation in query timing (perfect intervals indicating automation)
   - Similar query patterns across multiple accounts (identical use cases, coordinated content)

**Implementation:**

```python
import redis
from collections import defaultdict
from datetime import datetime, timedelta

redis_client = redis.Redis()

def detect_coordinated_rate_limit_bypass():
    """
    Detect coordinated multi-account campaigns bypassing rate limits
    
    Analyzes behavioral patterns across all accounts to identify:
    - Similar query patterns across accounts
    - Synchronized burst timing after rate limit resets
    - Account farming (bulk creation patterns)
    - Distributed infrastructure (IP correlation)
    """
    
    # Get all active accounts from last 24 hours
    active_accounts = get_active_accounts(last_hours=24)
    
    # Group accounts by behavioral similarity
    behavioral_clusters = cluster_accounts_by_behavior(active_accounts)
    
    # Flag clusters with suspicious patterns
    for cluster_id, account_group in behavioral_clusters.items():
        if len(account_group) >= 10:  # 10+ accounts with identical behavior
            
            # Calculate suspicion score
            suspicion_signals = []
            risk_score = 0.0
            
            # Signal 1: Similar query patterns
            query_similarity = calculate_query_pattern_similarity(account_group)
            if query_similarity > 0.8:  # High similarity
                suspicion_signals.append("identical_query_patterns")
                risk_score += 0.3
            
            # Signal 2: Accounts created in same time window
            creation_spread = get_account_creation_spread(account_group)
            if creation_spread < timedelta(hours=24):  # All created within 24h
                suspicion_signals.append("batch_account_creation")
                risk_score += 0.4
            
            # Signal 3: Synchronized burst timing
            if has_synchronized_bursts(account_group):
                suspicion_signals.append("synchronized_rate_limit_resets")
                risk_score += 0.3
            
            # Signal 4: Disposable email usage
            disposable_count = count_disposable_emails(account_group)
            if disposable_count > len(account_group) * 0.5:  # >50% disposable
                suspicion_signals.append("disposable_email_farming")
                risk_score += 0.4
            
            # Signal 5: Related IP ranges
            ip_correlation = calculate_ip_range_overlap(account_group)
            if ip_correlation > 0.6:
                suspicion_signals.append("correlated_ip_ranges")
                risk_score += 0.3
            
            if risk_score > 0.7:  # High confidence coordinated campaign
                log_security_event("coordinated_rate_limit_bypass_detected", {
                    "cluster_id": cluster_id,
                    "account_count": len(account_group),
                    "signals": suspicion_signals,
                    "risk_score": risk_score,
                    "account_ids": [hash_account_id(acc) for acc in account_group]
                })
                
                # Trigger automated response
                trigger_coordinated_campaign_response(account_group, suspicion_signals)

def cluster_accounts_by_behavior(accounts):
    """
    Cluster accounts by behavioral similarity
    
    Uses features:
    - Query timing patterns (hour of day distribution)
    - Query content similarity (topic distribution, keywords)
    - Request frequency and volume
    - Tool usage patterns (if applicable)
    """
    from sklearn.cluster import DBSCAN
    import numpy as np
    
    # Extract behavioral features for each account
    feature_vectors = []
    for account in accounts:
        features = extract_behavioral_features(account)
        feature_vectors.append(features)
    
    # Cluster accounts using DBSCAN
    clustering = DBSCAN(eps=0.3, min_samples=10).fit(np.array(feature_vectors))
    
    # Group accounts by cluster
    clusters = defaultdict(list)
    for i, label in enumerate(clustering.labels_):
        if label != -1:  # Ignore outliers
            clusters[label].append(accounts[i])
    
    return clusters

def has_synchronized_bursts(account_group):
    """
    Detect if accounts exhibit synchronized burst timing
    (e.g., all accounts spike queries immediately after midnight UTC)
    """
    # Get hourly query distribution for each account
    hourly_distributions = []
    for account in account_group:
        hourly_dist = get_hourly_query_distribution(account)
        hourly_distributions.append(hourly_dist)
    
    # Check if all accounts have peak at same hour
    peak_hours = [np.argmax(dist) for dist in hourly_distributions]
    
    # If >80% of accounts have same peak hour, likely synchronized
    from collections import Counter
    peak_hour_counts = Counter(peak_hours)
    most_common_peak, count = peak_hour_counts.most_common(1)[0]
    
    return count > len(account_group) * 0.8

def count_disposable_emails(account_group):
    """Count accounts using disposable email domains"""
    disposable_domains = [
        'tempmail.com', 'guerrillamail.com', '10minutemail.com',
        'mailinator.com', 'throwaway.email', 'temp-mail.org'
    ]
    
    count = 0
    for account in account_group:
        email_domain = account['email'].split('@')[1]
        if email_domain in disposable_domains:
            count += 1
    
    return count

def calculate_ip_range_overlap(account_group):
    """
    Calculate how correlated IP ranges are across accounts
    Returns: float between 0-1 indicating IP range overlap
    """
    # Get recent IPs for each account
    account_ips = defaultdict(set)
    for account in account_group:
        ips = get_account_recent_ips(account['id'])
        for ip in ips:
            # Store /24 CIDR prefix
            ip_prefix = '.'.join(ip.split('.')[:3])
            account_ips[account['id']].add(ip_prefix)
    
    # Calculate Jaccard similarity of IP prefixes
    all_prefixes = set()
    for prefixes in account_ips.values():
        all_prefixes.update(prefixes)
    
    # Count how many accounts share each prefix
    prefix_account_count = defaultdict(int)
    for account_id, prefixes in account_ips.items():
        for prefix in prefixes:
            prefix_account_count[prefix] += 1
    
    # High overlap if many accounts share IP ranges
    shared_prefixes = [p for p, count in prefix_account_count.items() if count > 1]
    overlap_ratio = len(shared_prefixes) / len(all_prefixes) if all_prefixes else 0
    
    return overlap_ratio

def trigger_coordinated_campaign_response(account_group, signals):
    """
    Automated response to detected coordinated campaign
    """
    # 1. Apply immediate rate limit reduction
    for account in account_group:
        apply_dynamic_rate_reduction(account['id'], reduction_factor=0.1, duration_hours=48)
    
    # 2. Flag accounts for review
    for account in account_group:
        flag_account_for_review(account['id'], "coordinated_bypass_campaign", signals)
    
    # 3. Alert security team
    alert_security_team("Coordinated Rate Limit Bypass Campaign Detected", {
        "account_count": len(account_group),
        "signals": signals,
        "recommended_action": "Review and consider bulk account suspension"
    })
    
    # 4. Block related IP ranges (if high IP correlation)
    ip_ranges = set()
    for account in account_group:
        ips = get_account_recent_ips(account['id'])
        for ip in ips:
            ip_prefix = '.'.join(ip.split('.')[:3])
            ip_ranges.add(ip_prefix)
    
    for ip_prefix in ip_ranges:
        blocklist_ip_range(ip_prefix, duration_hours=24, reason="Coordinated bypass campaign")
```

**Account Creation Velocity Monitoring:**

Track and alert on suspicious account creation patterns:

```python
def monitor_account_creation_velocity():
    """
    Monitor for account farming indicators
    """
    # Check account creations in last 24 hours
    recent_accounts = get_accounts_created_since(hours_ago=24)
    
    # Signal 1: High volume of new accounts
    if len(recent_accounts) > 100:  # Threshold tunable
        alert_security_team("High account creation velocity", {
            "count": len(recent_accounts),
            "period": "24 hours"
        })
    
    # Signal 2: Disposable email concentration
    disposable_count = count_disposable_emails(recent_accounts)
    if disposable_count > 20:  # Threshold tunable
        alert_security_team("High disposable email usage in new accounts", {
            "disposable_count": disposable_count,
            "total_accounts": len(recent_accounts)
        })
    
    # Signal 3: Sequential email patterns (programmatic generation)
    sequential_count = detect_sequential_emails(recent_accounts)
    if sequential_count > 10:
        alert_security_team("Sequential email pattern detected", {
            "sequential_count": sequential_count
        })

def detect_sequential_emails(accounts):
    """
    Detect programmatically generated email addresses
    (e.g., user001@example.com, user002@example.com, ...)
    """
    import re
    
    email_patterns = defaultdict(list)
    for account in accounts:
        email = account['email']
        # Extract pattern (replace digits with placeholder)
        pattern = re.sub(r'\d+', 'N', email)
        email_patterns[pattern].append(email)
    
    # Count patterns with >5 instances (likely sequential)
    sequential_count = sum(
        len(emails) for pattern, emails in email_patterns.items()
        if len(emails) > 5
    )
    
    return sequential_count
```

**Monitoring Dashboard:**

Track API rate limiting bypass metrics:
- **Coordinated campaign detection rate:** Clusters of accounts flagged for bypass behavior
- **Account farming indicators:** New account creation velocity, disposable email usage
- **Rate limit hit distribution:** Which accounts consistently hit limits (abuse vs. legitimate power users)
- **Burst timing correlation:** Accounts exhibiting synchronized behavior after rate limit resets
- **IP range correlation:** Accounts sharing infrastructure (ASNs, CIDR ranges)
- **Free-tier abuse concentration:** Aggregate resource consumption by free-tier accounts

> Source: [[sources/bibliography#Generative AI Security]], techniques/api-rate-limiting-bypass.md (extracted detective controls)


## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| |  | |

## Sources

> "Without comprehensive tracking of inputs, outputs, model performance, and user activity, organizations operate blind to ongoing prompt injection campaigns, data exfiltration attempts, tool abuse, and model drift. This gap transforms all other vulnerabilities from detectable incidents into silent breaches that can persist for weeks or months."
> — [[sources/bibliography#Generative AI Security]], techniques/insufficient-telemetry-and-tracing.md

> "Anomaly detection should analyze prompt patterns for injection attempts, user behavior for suspicious access patterns, and model performance for tampering or drift. Real-time alerting integrated with SIEM/SOAR platforms enables rapid response."
> — [[sources/bibliography#Generative AI Security]], techniques/insufficient-telemetry-and-tracing.md

> "Anomaly detection: Flag tool usage patterns inconsistent with user role. Real-time alerts on admin tool use by non-admins."
> — [[sources/bibliography#Securing AI Agents]], p. 210-211 (extracted from agent authorization hijacking mitigations)

> "Audit logging: Log all tool invocations with user context."
> — [[sources/bibliography#Securing AI Agents]], p. 210-211


## Applicable Issues

This control addresses the following security issues across multiple boundaries:

**Model Boundary:**
- **[[techniques/prompt-injection|Prompt Injection]]**: Detects meta-instruction patterns, unusual prompt structures, and injection attempts
- **[[techniques/jailbreak-policy-bypass|Jailbreak & Policy Bypass]]**: Identifies attempts to bypass safety guardrails through unusual input patterns
- **[[techniques/system-prompt-leakage|System Prompt Leakage]]**: Detects outputs containing system prompts or internal context

**Data/Knowledge Boundary:**
- **[[techniques/data-poisoning-attacks|Data Poisoning Attacks]]**: AI-based anomaly detection on data features, labels, and model update patterns flags suspicious training samples
- **AML.T0020 [[techniques/backdoor-poisoning|Backdoor Poisoning]]**: Detects anomalous training samples with embedded triggers, monitors for suspicious patterns in data features and labels indicating backdoor injection attempts
- **[[techniques/rag-data-poisoning|RAG Data Poisoning]]**: Identifies anomalous retrieval patterns and suspicious document access
- **[[techniques/embedding-poisoning|Embedding Poisoning]]**: Detects unusual embedding distributions and manipulation attempts
- **[[techniques/vector-embedding-weaknesses]]**: Tracks cosine-similarity distribution per user; alerts on sudden shift indicating possible embedding inversion attack; monitors for cross-tenant query patterns and unauthorized access attempts
- **[[techniques/unauthorized-knowledge-disclosure]]**: Detects unusual query patterns targeting topics outside user's normal access scope, cross-tenant document retrieval attempts, and systematic knowledge extraction attempts

**Application/Agent Runtime Boundary:**
- **AML.T0051 [[techniques/agent-authorization-hijacking|Agent Authorization Hijacking]]**: Detects tool invocations that don't match user permission level, API privilege escalation attempts, and unauthorized admin tool execution
- **[[techniques/tool-privilege-escalation|Tool Privilege Escalation]]**: Detects unusual tool usage patterns, privilege escalation attempts, and unauthorized tool invocations
- **[[techniques/agent-goal-hijack|Agent Goal Hijacking]]**: Identifies deviations from expected agent behavior and goal manipulation
- **[[techniques/unsafe-tool-invocation|Unsafe Tool Invocation]]**: Detects dangerous tool usage patterns and argument anomalies
- **[[techniques/react-security-risks]]**: Continuous adversarial monitoring detects ReAct agent vulnerabilities through anomalous reasoning traces and tool invocation patterns
- **[[techniques/weak-access-segmentation|Weak Access Segmentation]]**: Detects anomalous access patterns (cross-tenant queries, off-hours access from unusual locations, sudden query volume spikes), privilege escalation attempts, and repeated authorization failures indicating probing

**Cross-Boundary:**
- **AML.T0054 [[techniques/jailbreak-policy-bypass|Jailbreak & Policy Bypass]]**: Detects statistical anomalies in queries (adversarial suffix patterns), coordinated jailbreak campaigns, and unusual output characteristics indicating successful jailbreaks

**Supply Chain Boundary:**
- **AML.T0020 [[techniques/supply-chain-model-poisoning]]**: Weight distribution anomaly detection compares candidate model parameters against reference clean models; flags statistical outliers in layer weights indicating potential backdoor embedding

**Privacy Boundary:**
- **AML.T0024 [[techniques/sensitive-info-disclosure|Sensitive Information Disclosure]]**: Detects systematic membership inference query patterns, calibration querying, shadow modeling signatures, and abnormal confidence score distributions indicating privacy attacks
- **AML.T0056 [[techniques/training-data-memorization|Training Data Memorization]]**: Detects attribute/property inference attempts, shadow modeling activity, linkage attack patterns, and meta-classifier training signatures indicating memorization exploitation

**IP/Model Boundary:**
- **AML.T0049 [[techniques/model-extraction-llm|Model Extraction (LLM)]]**: ML-based behavioral analytics to detect extraction attempts; baselines legitimate usage and flags deviations such as systematic topic coverage, template-based querying, and distributed multi-account extraction campaigns

**Deployment/Governance Boundary:**
- **[[techniques/incident-response-gap]]**: Provides detection infrastructure necessary for effective incident response by identifying attack patterns, behavioral anomalies, and security events requiring investigation
- **[[techniques/api-authentication-bypass|API Authentication Bypass]]**: Identifies unusual access patterns and authentication anomalies
- **[[techniques/api-rate-limiting-bypass|API Rate Limiting Bypass]]**: Detects coordinated multi-account campaigns through behavioral correlation, identifies account farming patterns (bulk account creation, disposable email domains), flags burst querying synchronized with rate limit resets, detects distributed bypass attempts across IP ranges, and identifies systematic quota abuse
- **[[techniques/insufficient-telemetry-and-tracing|Insufficient Telemetry and Tracing]]**: Provides real-time anomaly detection analyzing prompt patterns, user behavior, and model performance to identify attacks that bypass preventive controls

See [Trust Boundaries Overview for complete issue catalogs]


## Architecture Considerations

**Design Pattern: Layered Detection**

Anomaly detection implements a layered approach:
- **Layer 1**: Fast, rule-based detection for known patterns (low latency, high precision)
- **Layer 2**: Statistical anomaly detection for novel patterns (higher latency, broader coverage)
- **Layer 3**: Behavioral analysis for sophisticated multi-step attacks (deep analysis, correlation)

**System Integration:**

```
System Events → Log Aggregation → Detection Pipeline → Alerting
                    ↓                    ↓
            Normalization         Pattern Matching
                    ↓                    ↓
            Feature Extraction → Anomaly Scoring → Alert Generation
```

**Scalability:**

- **Volume Handling**: Process high-volume event streams without dropping events
- **Latency Requirements**: Balance detection speed with analysis depth
- **Distributed Systems**: Coordinate detection across service boundaries
- **Resource Efficiency**: Optimize detection algorithms for production workloads
