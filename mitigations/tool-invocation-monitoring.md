---
title: "Tool Invocation Monitoring"
tags:
  - type/mitigation
  - target/agent
  - target/llm-app
  - source/generative-ai-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Tool Invocation Monitoring

## Summary

Tool invocation monitoring provides detective controls that identify suspicious or malicious tool usage patterns through comprehensive logging, behavioral anomaly detection, and correlation analysis. This control captures all tool invocations with full context (user, arguments, outcomes) and applies statistical analysis to detect privilege escalation attempts, cross-user data access, injection attacks, and coordinated exploitation campaigns. By continuously monitoring tool usage and alerting on anomalies, this mitigation enables rapid detection and response to tool abuse attacks.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/tool-privilege-escalation]] | Detects privilege escalation attempts through behavioral anomaly detection and privilege monitoring |
| | [[techniques/unsafe-tool-invocation]] | Identifies injection attacks through argument inspection and pattern matching |
| | [[techniques/agent-authorization-hijacking]] | Detects authorization bypass attempts through cross-user access monitoring |
| | [[techniques/prompt-injection]] | Identifies injection-driven tool abuse through correlation with prompt patterns |
| | [[techniques/react-security-risks]] | Continuous oversight of ReAct agent actions detects potentially dangerous tool invocations before execution |

## Implementation

### Core Monitoring Components

**1. Comprehensive Tool Invocation Logging**

Capture all tool invocations with complete context for forensic analysis:

```python
import json
import time
from datetime import datetime

class ToolInvocationLogger:
    """
    Comprehensive logging for all tool invocations
    
    Captures: user context, tool name, arguments, results, timing, errors
    """
    
    def __init__(self, log_backend):
        self.log_backend = log_backend
    
    def log_invocation(self, tool_name, arguments, user_context, result=None, error=None, duration_ms=None):
        """
        Log tool invocation with full context
        
        Args:
            tool_name: Name of invoked tool
            arguments: Tool arguments (sanitized for logging)
            user_context: User security context
            result: Tool execution result (optional)
            error: Error message if invocation failed (optional)
            duration_ms: Execution duration in milliseconds (optional)
        """
        log_entry = {
            'timestamp': datetime.utcnow().isoformat(),
            'event_type': 'tool_invocation',
            'tool_name': tool_name,
            'user_id': user_context.user_id,
            'user_roles': user_context.roles,
            'arguments': self._sanitize_arguments(arguments),
            'status': 'success' if result is not None else 'error',
            'duration_ms': duration_ms,
            'error': error,
            'session_id': user_context.session_id
        }
        
        # Don't log sensitive data in results (sanitize)
        if result:
            log_entry['result_summary'] = self._summarize_result(result)
        
        self.log_backend.write(log_entry)
    
    def _sanitize_arguments(self, arguments):
        """Remove sensitive data from arguments before logging"""
        sanitized = arguments.copy()
        
        # Remove passwords, tokens, secrets
        sensitive_keys = ['password', 'token', 'api_key', 'secret']
        for key in sensitive_keys:
            if key in sanitized:
                sanitized[key] = '[REDACTED]'
        
        return sanitized
    
    def _summarize_result(self, result):
        """Summarize result without logging sensitive data"""
        if isinstance(result, dict):
            return {
                'type': 'dict',
                'keys': list(result.keys()),
                'size': len(str(result))
            }
        elif isinstance(result, list):
            return {
                'type': 'list',
                'count': len(result),
                'size': len(str(result))
            }
        else:
            return {
                'type': type(result).__name__,
                'size': len(str(result))
            }
```

**2. Behavioral Anomaly Detection**

Detect unusual tool usage patterns that may indicate attacks:

```python
from collections import defaultdict
from datetime import datetime, timedelta

class ToolBehaviorMonitor:
    """
    Monitor tool usage patterns for anomalies
    
    Detects:
    - Unusual time-of-day usage
    - Frequency spikes
    - Unusual tool sequencing
    - Tools used outside normal user behavior
    """
    
    def __init__(self):
        self.user_baselines = defaultdict(lambda: {
            'tools_used': set(),
            'typical_times': [],
            'typical_frequency': {},
            'typical_sequences': []
        })
        self.recent_invocations = []
    
    def record_invocation(self, user_id, tool_name, timestamp):
        """Record invocation for behavioral analysis"""
        self.recent_invocations.append({
            'user_id': user_id,
            'tool_name': tool_name,
            'timestamp': timestamp
        })
        
        # Maintain sliding window (last 24 hours)
        cutoff = timestamp - timedelta(hours=24)
        self.recent_invocations = [
            inv for inv in self.recent_invocations 
            if inv['timestamp'] > cutoff
        ]
    
    def check_anomalies(self, user_id, tool_name, timestamp):
        """
        Check for behavioral anomalies
        
        Returns: (is_anomalous: bool, reasons: list)
        """
        anomalies = []
        
        # Check 1: Unusual time of day
        if self._is_unusual_time(user_id, timestamp):
            anomalies.append(f"Tool used at unusual time: {timestamp.hour}:00")
        
        # Check 2: Frequency spike
        if self._is_frequency_spike(user_id, tool_name):
            anomalies.append(f"Unusual frequency spike for {tool_name}")
        
        # Check 3: Never-before-used tool
        if tool_name not in self.user_baselines[user_id]['tools_used']:
            anomalies.append(f"First-time use of {tool_name} by user")
        
        # Check 4: Unusual tool sequence
        if self._is_unusual_sequence(user_id, tool_name):
            anomalies.append(f"Unusual tool sequencing detected")
        
        is_anomalous = len(anomalies) > 0
        
        return is_anomalous, anomalies
    
    def _is_unusual_time(self, user_id, timestamp):
        """Check if time is outside user's typical usage hours"""
        baseline = self.user_baselines[user_id]['typical_times']
        
        if not baseline:
            return False  # No baseline yet
        
        hour = timestamp.hour
        
        # Define "unusual" as outside 7am-11pm for most users
        # (Customize based on learned baseline)
        if hour < 7 or hour > 23:
            return True
        
        return False
    
    def _is_frequency_spike(self, user_id, tool_name):
        """Check if recent frequency exceeds normal baseline"""
        # Count recent invocations of this tool by this user
        recent_count = sum(
            1 for inv in self.recent_invocations
            if inv['user_id'] == user_id and inv['tool_name'] == tool_name
        )
        
        # Get baseline frequency (calls per hour)
        baseline_freq = self.user_baselines[user_id]['typical_frequency'].get(tool_name, 0)
        
        # Alert if frequency is 3x baseline
        if recent_count > baseline_freq * 3:
            return True
        
        return False
    
    def _is_unusual_sequence(self, user_id, tool_name):
        """Check if tool sequence matches known attack patterns"""
        # Get recent tool sequence for user
        recent_tools = [
            inv['tool_name'] for inv in self.recent_invocations[-5:]
            if inv['user_id'] == user_id
        ]
        recent_tools.append(tool_name)
        
        # Check for known attack sequences
        attack_sequences = [
            ['search_customer', 'query_database', 'send_email'],  # Data exfiltration
            ['read_file', 'read_file', 'send_email'],  # File exfiltration
            ['query_database', 'query_database', 'query_database'],  # Database scanning
        ]
        
        for attack_seq in attack_sequences:
            if self._sequence_matches(recent_tools, attack_seq):
                return True
        
        return False
    
    def _sequence_matches(self, actual_sequence, pattern_sequence):
        """Check if actual sequence contains pattern sequence"""
        if len(actual_sequence) < len(pattern_sequence):
            return False
        
        for i in range(len(actual_sequence) - len(pattern_sequence) + 1):
            if actual_sequence[i:i+len(pattern_sequence)] == pattern_sequence:
                return True
        
        return False
```

**3. Privilege Monitoring**

Alert when low-privilege users invoke high-privilege tools:

```python
class PrivilegeMonitor:
    """
    Monitor tool invocations for privilege escalation attempts
    
    Alerts when users invoke tools beyond their typical privilege level
    """
    
    TOOL_PRIVILEGE_LEVELS = {
        'read_user_data': 1,          # Low privilege
        'search_customer': 1,
        'update_ticket': 2,           # Medium privilege
        'send_email': 2,
        'query_database': 3,          # High privilege
        'run_admin_powershell': 4,    # Admin privilege
        'modify_system_config': 4
    }
    
    def check_privilege_escalation(self, user_context, tool_name):
        """
        Check if user is attempting to use tool above their privilege level
        
        Returns: (is_escalation: bool, severity: str)
        """
        tool_privilege = self.TOOL_PRIVILEGE_LEVELS.get(tool_name, 0)
        user_max_privilege = self._get_user_max_privilege(user_context)
        
        if tool_privilege > user_max_privilege:
            severity = 'critical' if tool_privilege == 4 else 'high'
            
            log_security_event('privilege_escalation_attempt', {
                'user_id': user_context.user_id,
                'user_privilege_level': user_max_privilege,
                'tool': tool_name,
                'tool_privilege_level': tool_privilege,
                'severity': severity
            })
            
            return True, severity
        
        return False, None
    
    def _get_user_max_privilege(self, user_context):
        """Determine user's maximum privilege level based on roles"""
        if 'system.admin' in user_context.roles:
            return 4
        elif 'helpdesk.admin' in user_context.roles:
            return 3
        elif 'helpdesk.user' in user_context.roles:
            return 2
        else:
            return 1
```

**4. Argument Inspection for Injection Patterns**

Scan tool arguments for injection attack patterns:

```python
import re

class ArgumentInspector:
    """
    Inspect tool arguments for injection patterns
    
    Detects SQL injection, path traversal, command injection
    """
    
    INJECTION_PATTERNS = {
        'sql_injection': [
            r"[';].*--",                    # SQL comments
            r"union\s+select",              # UNION-based injection
            r"drop\s+table",                # Destructive SQL
            r"'.*or.*'.*=.*'",              # OR-based injection
        ],
        'path_traversal': [
            r"\.\./",                       # Directory traversal
            r"\.\./\.\./",                  # Multi-level traversal
            r"^/etc/",                      # Absolute path to sensitive dir
        ],
        'command_injection': [
            r"[;&|`$]",                     # Shell metacharacters
            r"[<>]",                        # Redirection
            r"\$\(",                        # Command substitution
        ]
    }
    
    def inspect_arguments(self, tool_name, arguments):
        """
        Inspect arguments for injection patterns
        
        Returns: (suspicious: bool, detected_patterns: list)
        """
        detected = []
        
        # Convert arguments to searchable strings
        arg_strings = self._flatten_arguments(arguments)
        
        # Check all patterns
        for attack_type, patterns in self.INJECTION_PATTERNS.items():
            for pattern in patterns:
                for arg_string in arg_strings:
                    if re.search(pattern, arg_string, re.IGNORECASE):
                        detected.append({
                            'attack_type': attack_type,
                            'pattern': pattern,
                            'matched_in': arg_string[:100]  # First 100 chars
                        })
        
        if detected:
            log_security_event('injection_pattern_detected', {
                'tool': tool_name,
                'arguments': arguments,
                'detected_patterns': detected
            })
        
        return len(detected) > 0, detected
    
    def _flatten_arguments(self, arguments):
        """Convert arguments dict to list of searchable strings"""
        strings = []
        
        for key, value in arguments.items():
            if isinstance(value, str):
                strings.append(value)
            elif isinstance(value, (list, dict)):
                strings.append(str(value))
        
        return strings
```

**5. Cross-User Access Detection**

Monitor for unauthorized cross-user data access:

```python
class CrossUserAccessMonitor:
    """
    Monitor for cross-user data access attempts
    
    Alerts when one user's session accesses another user's data
    """
    
    def check_cross_user_access(self, user_context, tool_name, arguments):
        """
        Check if tool invocation accesses data belonging to other users
        
        Returns: (is_cross_access: bool, accessed_user_ids: list)
        """
        # Extract user IDs from arguments
        referenced_user_ids = self._extract_user_ids_from_arguments(arguments)
        
        # Check if any referenced users differ from invoking user
        cross_access_users = [
            uid for uid in referenced_user_ids
            if uid != user_context.user_id
        ]
        
        if cross_access_users:
            # Check if user has legitimate reason to access other users' data
            if not self._is_authorized_cross_access(user_context, cross_access_users):
                log_security_event('unauthorized_cross_user_access', {
                    'user_id': user_context.user_id,
                    'tool': tool_name,
                    'accessed_user_ids': cross_access_users
                })
                
                return True, cross_access_users
        
        return False, []
    
    def _extract_user_ids_from_arguments(self, arguments):
        """Extract user IDs from tool arguments"""
        user_ids = []
        
        # Common argument names containing user IDs
        user_id_keys = ['user_id', 'customer_id', 'target_user', 'email']
        
        for key, value in arguments.items():
            if key in user_id_keys and isinstance(value, str):
                user_ids.append(value)
        
        return user_ids
    
    def _is_authorized_cross_access(self, user_context, accessed_user_ids):
        """Check if user is authorized to access other users' data"""
        # Admins can access any user's data
        if 'helpdesk.admin' in user_context.roles:
            return True
        
        # Helpdesk users can access their assigned customers
        if 'helpdesk.user' in user_context.roles:
            assigned_customers = get_assigned_customers(user_context.user_id)
            return all(uid in assigned_customers for uid in accessed_user_ids)
        
        return False
```

**6. Correlation Rules and Alert Generation**

Correlate multiple signals to identify coordinated attacks:

```python
class ToolInvocationCorrelator:
    """
    Correlate tool invocations to detect attack patterns
    
    Combines signals from logging, anomaly detection, privilege monitoring,
    argument inspection to generate high-confidence alerts
    """
    
    def correlate_and_alert(self, invocation_event):
        """
        Analyze invocation event for correlated attack signals
        
        Generates alert if multiple suspicious signals detected
        """
        signals = []
        
        # Signal 1: Behavioral anomaly
        is_anomalous, anomaly_reasons = behavior_monitor.check_anomalies(
            invocation_event.user_id,
            invocation_event.tool_name,
            invocation_event.timestamp
        )
        if is_anomalous:
            signals.append({'type': 'behavioral_anomaly', 'reasons': anomaly_reasons})
        
        # Signal 2: Privilege escalation
        is_escalation, severity = privilege_monitor.check_privilege_escalation(
            invocation_event.user_context,
            invocation_event.tool_name
        )
        if is_escalation:
            signals.append({'type': 'privilege_escalation', 'severity': severity})
        
        # Signal 3: Injection patterns
        has_injection, patterns = argument_inspector.inspect_arguments(
            invocation_event.tool_name,
            invocation_event.arguments
        )
        if has_injection:
            signals.append({'type': 'injection_pattern', 'patterns': patterns})
        
        # Signal 4: Cross-user access
        is_cross_access, accessed_users = cross_user_monitor.check_cross_user_access(
            invocation_event.user_context,
            invocation_event.tool_name,
            invocation_event.arguments
        )
        if is_cross_access:
            signals.append({'type': 'cross_user_access', 'users': accessed_users})
        
        # Generate alert if multiple signals detected
        if len(signals) >= 2:
            self._generate_security_alert(invocation_event, signals)
        
        return signals
    
    def _generate_security_alert(self, invocation_event, signals):
        """Generate high-priority security alert"""
        alert = {
            'alert_type': 'tool_abuse_detected',
            'severity': 'high',
            'timestamp': invocation_event.timestamp,
            'user_id': invocation_event.user_id,
            'tool': invocation_event.tool_name,
            'signals': signals,
            'recommended_action': 'Suspend user session and investigate'
        }
        
        # Send alert to security team
        send_security_alert(alert)
        
        # Log for incident response
        log_security_event('tool_abuse_alert', alert)
```

### ReAct Agent Action Monitoring

**Specific considerations for monitoring ReAct (Reasoning and Acting) paradigm agents:**

ReAct agents generate reasoning traces (thoughts) and actions in an interleaved sequence. Monitoring must capture both the reasoning process and resulting tool invocations to detect malicious behavior early—ideally before actions execute.

> "Continuous oversight of the actions generated by the model ensures that potentially dangerous ones are detected and blocked before execution. This involves setting up robust monitoring systems and defining rules to identify and halt risky actions and have humans in the loop for some complex actions."
> — [[sources/bibliography#Generative AI Security]], p. 211 (§7.3.3)

**ReAct-Specific Monitoring Architecture:**

```python
class ReActAgentMonitor:
    """Monitor ReAct agent reasoning traces and actions"""
    
    def __init__(self, tool_risk_classifier, human_review_queue):
        self.tool_risk_classifier = tool_risk_classifier
        self.human_review_queue = human_review_queue
        self.action_log = []
    
    def monitor_react_trajectory(self, trajectory: list):
        """
        Monitor full ReAct trajectory (reasoning + actions)
        
        Args:
            trajectory: List of {'type': 'thought'|'action', 'content': str}
        
        Returns:
            Monitoring verdict with any blocks or escalations
        """
        verdict = {'allowed_actions': [], 'blocked_actions': [], 'escalated_actions': []}
        
        for step in trajectory:
            if step['type'] == 'thought':
                # Monitor reasoning for concerning patterns
                if self._contains_risky_reasoning(step['content']):
                    log_security_event('risky_reasoning_detected', {
                        'thought': step['content'],
                        'timestamp': datetime.utcnow()
                    })
            
            elif step['type'] == 'action':
                # Classify action risk level
                risk_assessment = self._assess_action_risk(step['content'])
                
                if risk_assessment['level'] == 'critical':
                    # Block immediately
                    verdict['blocked_actions'].append({
                        'action': step['content'],
                        'reason': risk_assessment['reason'],
                        'blocked_at': datetime.utcnow()
                    })
                    log_security_event('critical_action_blocked', verdict['blocked_actions'][-1])
                
                elif risk_assessment['level'] == 'high':
                    # Escalate to human review
                    verdict['escalated_actions'].append(step['content'])
                    self.human_review_queue.enqueue({
                        'action': step['content'],
                        'reasoning_context': self._extract_recent_thoughts(trajectory),
                        'risk_level': 'high',
                        'requires_approval': True
                    })
                
                else:
                    # Allow but log
                    verdict['allowed_actions'].append(step['content'])
                    self.action_log.append({
                        'action': step['content'],
                        'risk_level': risk_assessment['level'],
                        'timestamp': datetime.utcnow()
                    })
        
        return verdict
    
    def _assess_action_risk(self, action: str) -> dict:
        """Classify action risk using ML classifier + rule-based checks"""
        risk = {'level': 'low', 'reason': ''}
        
        # Critical patterns: immediate block
        critical_patterns = [
            r'delete.*database',
            r'DROP\s+TABLE',
            r'rm\s+-rf\s+/',
            r'transfer.*funds',
            r'expose.*credentials'
        ]
        
        for pattern in critical_patterns:
            if re.search(pattern, action, re.IGNORECASE):
                return {'level': 'critical', 'reason': f'Matched critical pattern: {pattern}'}
        
        # ML-based risk classification
        risk_score = self.tool_risk_classifier.predict_risk(action)
        
        if risk_score > 0.8:
            return {'level': 'high', 'reason': f'ML classifier risk score: {risk_score}'}
        elif risk_score > 0.5:
            return {'level': 'medium', 'reason': f'ML classifier risk score: {risk_score}'}
        
        return risk
    
    def _contains_risky_reasoning(self, thought: str) -> bool:
        """Detect risky patterns in reasoning traces"""
        risky_patterns = [
            'bypass security',
            'circumvent access control',
            'exfiltrate data',
            'privilege escalation'
        ]
        return any(pattern in thought.lower() for pattern in risky_patterns)
    
    def _extract_recent_thoughts(self, trajectory: list, n: int = 3) -> list:
        """Extract last N reasoning traces for context"""
        thoughts = [step['content'] for step in trajectory if step['type'] == 'thought']
        return thoughts[-n:]
```

**Human-in-the-Loop Integration:**

For complex or high-risk actions, escalate to human review before execution:

```python
def execute_react_action_with_review(action: str, reasoning_context: list):
    """Execute action with mandatory human review for high-risk operations"""
    
    # Check if action requires human approval
    if requires_human_review(action):
        approval_request = {
            'action': action,
            'reasoning_context': reasoning_context,
            'requester': 'react_agent',
            'timestamp': datetime.utcnow()
        }
        
        # Block execution until human approves
        approval_response = human_review_queue.request_approval(approval_request, timeout=300)
        
        if not approval_response['approved']:
            log_security_event('action_denied_by_human', {
                'action': action,
                'denial_reason': approval_response['reason']
            })
            raise ActionDeniedError(f"Human reviewer denied action: {approval_response['reason']}")
    
    # Execute action
    return execute_tool_action(action)
```

**Monitoring Metrics for ReAct Agents:**

- **Reasoning-to-action ratio:** Sudden drop may indicate bypassed reasoning process
- **Action risk distribution:** Track percentage of high/critical risk actions over time
- **Human review escalation rate:** Monitor trends in escalated actions
- **Blocked action patterns:** Identify systematic attempts to invoke risky tools
- **Reasoning-action correlation:** Detect mismatches between stated reasoning and executed actions

## Limitations & Trade-offs

**Limitations:**

- **Reactive, not preventive:** Monitoring detects attacks after they occur, cannot prevent initial attempts
- **False positives:** Legitimate unusual usage may trigger alerts
- **Baseline learning required:** Behavioral anomaly detection requires training period
- **Cannot detect all attacks:** Sophisticated attackers may evade pattern-based detection

**Trade-offs:**

- **Detection coverage vs. false positive rate:** More sensitive monitoring increases both
- **Real-time vs. batch processing:** Real-time detection adds latency, batch processing delays alerts
- **Logging detail vs. storage costs:** Comprehensive logging requires significant storage
- **Alert fatigue vs. missed detections:** Too many alerts overwhelm responders, too few miss attacks

**Bypass Scenarios:**

- **Slow attacks:** Attackers operating below frequency thresholds may evade detection
- **Legitimate tool usage:** Attackers with legitimate access may blend in
- **Novel patterns:** Completely new attack patterns not in correlation rules
- **Distributed attacks:** Coordinated attacks across multiple accounts harder to correlate

## Testing & Validation

**Functional Testing:**

1. **Logging completeness:** Verify all tool invocations are logged with full context
2. **Anomaly detection:** Test that unusual patterns trigger alerts
3. **Privilege monitoring:** Verify privilege escalation attempts are detected
4. **Correlation logic:** Test that multiple signals generate alerts

**Security Testing:**

1. **Attack simulation:** Execute tool abuse attacks and verify detection
2. **False positive testing:** Confirm legitimate unusual usage doesn't trigger excessive alerts
3. **Evasion testing:** Attempt to bypass monitoring through slow attacks or obfuscation
4. **Alert response:** Verify security team receives and can act on alerts

## Sources

> "Behavioral Anomaly Detection: Alert on unusual tool usage patterns (time, frequency, sequence). Privilege Monitoring: Flag when low-privilege users invoke high-privilege tools. Argument Inspection: Scan tool arguments for injection patterns (SQL, shell, path traversal). Cross-User Access Alerts: Detect when one user's session accesses another's data. Tool Invocation Logging: Comprehensive logs with user context, arguments, and outcomes. Correlation Rules: Alert on tool chains associated with known attack patterns."
> — Extracted from tool-privilege-escalation detective controls

> "Continuous oversight of the actions generated by the model ensures that potentially dangerous ones are detected and blocked before execution. This involves setting up robust monitoring systems and defining rules to identify and halt risky actions and have humans in the loop for some complex actions."
> — [[sources/bibliography#Generative AI Security]], p. 211 (§7.3.3 Security Considerations for ReAct)

## Related

- **Combines with:** [[mitigations/anomaly-detection-architecture]], [[mitigations/tool-argument-validation]], [[mitigations/incident-response-procedures]]
- **Feeds into:** [[mitigations/kill-switch-and-session-termination]] (automated response to detected attacks)
- **Supports:** Forensic investigation and incident response
