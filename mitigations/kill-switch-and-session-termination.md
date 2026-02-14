---
title: "Kill Switch And Session Termination"
tags:
  - trust-boundary/agent-runtime
  - type/mitigation
  - target/agent
  - target/llm-app
  - source/ai-native-llm-security
atlas: AML.M0033
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Kill Switch And Session Termination

## Summary

Kill switch and session termination controls provide emergency stop capabilities for AI systems, enabling rapid shutdown of compromised sessions, runaway agents, or ongoing attacks. These responsive controls serve as a last line of defense when preventive measures fail, allowing operators to immediately halt dangerous operations before irreversible damage occurs. By providing manual and automated termination mechanisms, this mitigation limits the blast radius of successful attacks and prevents escalation of compromised agent behavior.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/unsafe-tool-invocation]] | Immediately stops execution of dangerous tool invocations in progress |
| | [[techniques/agent-goal-hijack]] | Terminates sessions where agent goals have been manipulated |
| | [[techniques/tool-privilege-escalation]] | Halts sessions attempting unauthorized privilege escalation |
| | [[techniques/prompt-injection]] | Stops sessions exhibiting injection-driven malicious behavior |
| | [[techniques/agent-authorization-hijacking]] | Terminates sessions with compromised authorization context |

## Implementation

### Kill Switch Mechanisms

**1. Manual Kill Switch (Emergency Override)**

Provide operators with immediate session termination capability:

```python
class SessionKillSwitch:
    """Manual kill switch for emergency session termination"""
    
    def __init__(self, session_manager):
        self.session_manager = session_manager
        self.terminated_sessions = set()
    
    def kill_session(self, session_id, operator_id, reason):
        """
        Emergency termination of active session
        
        Args:
            session_id: Session to terminate
            operator_id: Operator authorizing kill
            reason: Justification for termination
        
        Returns:
            Termination result
        """
        # Validate operator authority
        if not self.is_authorized_operator(operator_id):
            raise UnauthorizedError("Kill switch requires operator privileges")
        
        # Terminate session
        session = self.session_manager.get_session(session_id)
        
        # Stop all active operations
        session.cancel_all_operations()
        
        # Revoke all tool access
        session.revoke_all_tools()
        
        # Mark session as terminated
        session.status = "KILLED"
        session.termination_reason = reason
        session.terminated_by = operator_id
        session.terminated_at = datetime.now()
        
        # Record termination
        self.terminated_sessions.add(session_id)
        
        log_security_event("session_killed", {
            'session_id': session_id,
            'operator': operator_id,
            'reason': reason,
            'active_tools': session.active_tools,
            'pending_operations': len(session.pending_operations)
        })
        
        return {
            'status': 'terminated',
            'session_id': session_id,
            'terminated_at': session.terminated_at
        }
    
    def is_authorized_operator(self, operator_id):
        """Check if operator has kill switch authority"""
        # In production: check against RBAC system
        return operator_id in AUTHORIZED_OPERATORS
```

**2. Automated Session Termination on Abuse**

Automatically terminate sessions exhibiting dangerous patterns:

```python
class AutomatedSessionTermination:
    """Automatic session termination on detected abuse"""
    
    TERMINATION_RULES = [
        {
            'name': 'rapid_tool_chaining',
            'condition': lambda session: len(session.tool_history) > 10 and
                        (datetime.now() - session.tool_history[0]['timestamp']).seconds < 60,
            'reason': 'Rapid tool invocation sequence detected'
        },
        {
            'name': 'privilege_escalation_attempt',
            'condition': lambda session: any(
                tool['name'] in ['modify_permissions', 'grant_access'] 
                for tool in session.tool_history
            ),
            'reason': 'Privilege escalation tool invocation detected'
        },
        {
            'name': 'policy_violation_threshold',
            'condition': lambda session: session.policy_violations >= 5,
            'reason': 'Policy violation threshold exceeded'
        },
        {
            'name': 'suspicious_argument_pattern',
            'condition': lambda session: session.anomaly_score > 0.9,
            'reason': 'High anomaly score in tool arguments'
        }
    ]
    
    def check_and_terminate(self, session):
        """
        Check if session should be automatically terminated
        
        Returns: (should_terminate: bool, reason: str)
        """
        for rule in self.TERMINATION_RULES:
            if rule['condition'](session):
                # Automatic termination
                self.terminate_session(session, rule['reason'])
                return True, rule['reason']
        
        return False, None
    
    def terminate_session(self, session, reason):
        """Execute automatic termination"""
        session.cancel_all_operations()
        session.revoke_all_tools()
        session.status = "AUTO_TERMINATED"
        session.termination_reason = reason
        session.terminated_at = datetime.now()
        
        # Alert operators
        send_alert("session_auto_terminated", {
            'session_id': session.id,
            'user_id': session.user_id,
            'reason': reason,
            'tool_history': session.tool_history[-10:]  # Last 10 tools
        })
        
        log_security_event("auto_termination", {
            'session_id': session.id,
            'reason': reason,
            'policy_violations': session.policy_violations,
            'anomaly_score': session.anomaly_score
        })
```

**3. Progressive Restriction (Before Full Termination)**

Gradually restrict session capabilities before full kill:

```python
class ProgressiveRestriction:
    """
    Progressive restriction of session capabilities
    
    Stages:
    1. Warning (alert user, no restrictions)
    2. Read-only mode (disable write tools)
    3. Quarantine (disable all tools, require manual review)
    4. Termination (full kill switch)
    """
    
    RESTRICTION_LEVELS = {
        'WARNING': {'write_tools': True, 'read_tools': True, 'alert': True},
        'READ_ONLY': {'write_tools': False, 'read_tools': True, 'alert': True},
        'QUARANTINE': {'write_tools': False, 'read_tools': False, 'alert': True},
        'TERMINATED': {'write_tools': False, 'read_tools': False, 'alert': True}
    }
    
    def __init__(self):
        self.session_restrictions = {}
    
    def escalate_restrictions(self, session_id, trigger_reason):
        """
        Progressively escalate session restrictions
        
        Args:
            session_id: Session to restrict
            trigger_reason: What triggered escalation
        """
        current_level = self.session_restrictions.get(session_id, 'NORMAL')
        
        # Determine next restriction level
        escalation_path = ['WARNING', 'READ_ONLY', 'QUARANTINE', 'TERMINATED']
        
        if current_level == 'NORMAL':
            next_level = 'WARNING'
        elif current_level in escalation_path:
            current_index = escalation_path.index(current_level)
            next_level = escalation_path[min(current_index + 1, len(escalation_path) - 1)]
        else:
            next_level = 'WARNING'
        
        # Apply restrictions
        self.apply_restriction_level(session_id, next_level, trigger_reason)
        
        return next_level
    
    def apply_restriction_level(self, session_id, level, reason):
        """Apply specific restriction level to session"""
        self.session_restrictions[session_id] = level
        restrictions = self.RESTRICTION_LEVELS[level]
        
        session = get_session(session_id)
        
        # Disable write tools if restricted
        if not restrictions['write_tools']:
            session.disable_write_tools()
        
        # Disable all tools if quarantined
        if not restrictions['read_tools']:
            session.disable_all_tools()
        
        # Alert user and operators
        if restrictions['alert']:
            notify_user(session.user_id, f"Session restricted to {level}: {reason}")
            alert_operators({
                'session_id': session_id,
                'restriction_level': level,
                'reason': reason
            })
        
        # Full termination
        if level == 'TERMINATED':
            session.cancel_all_operations()
            session.status = 'TERMINATED'
        
        log_security_event("session_restriction", {
            'session_id': session_id,
            'level': level,
            'reason': reason
        })
```

### Operator Dashboard for Kill Switch

**Real-time monitoring and manual controls:**

```python
class KillSwitchDashboard:
    """Operator dashboard for session monitoring and kill switch"""
    
    def get_suspicious_sessions(self):
        """
        Identify sessions requiring operator attention
        
        Returns list of sessions with:
        - High anomaly scores
        - Multiple policy violations
        - Unusual tool usage patterns
        - Recent security alerts
        """
        sessions = get_active_sessions()
        suspicious = []
        
        for session in sessions:
            risk_score = self.calculate_session_risk(session)
            
            if risk_score > 0.7:
                suspicious.append({
                    'session_id': session.id,
                    'user_id': session.user_id,
                    'risk_score': risk_score,
                    'policy_violations': session.policy_violations,
                    'anomaly_score': session.anomaly_score,
                    'tool_history': session.tool_history[-5:],  # Last 5 tools
                    'recommended_action': self.recommend_action(risk_score)
                })
        
        return sorted(suspicious, key=lambda x: x['risk_score'], reverse=True)
    
    def calculate_session_risk(self, session):
        """Calculate overall risk score for session"""
        score = 0.0
        
        # Policy violations
        score += min(session.policy_violations * 0.1, 0.4)
        
        # Anomaly score
        score += session.anomaly_score * 0.3
        
        # Tool usage patterns
        if len(session.tool_history) > 20:
            score += 0.2
        
        # Recent security alerts
        score += len(session.security_alerts) * 0.05
        
        return min(score, 1.0)
    
    def recommend_action(self, risk_score):
        """Recommend action based on risk score"""
        if risk_score > 0.9:
            return "IMMEDIATE_TERMINATION"
        elif risk_score > 0.7:
            return "QUARANTINE"
        elif risk_score > 0.5:
            return "READ_ONLY"
        else:
            return "MONITOR"
```

### Integration with Tool Invocation Layer

**Enforce kill switch at tool execution boundary:**

```python
class ToolInvocationLayer:
    """Tool invocation with kill switch integration"""
    
    def __init__(self):
        self.kill_switch = SessionKillSwitch()
        self.auto_termination = AutomatedSessionTermination()
        self.progressive_restriction = ProgressiveRestriction()
    
    def invoke_tool(self, session_id, tool_name, arguments):
        """
        Invoke tool with kill switch checks
        """
        session = get_session(session_id)
        
        # Check if session is terminated
        if session.status in ['KILLED', 'TERMINATED', 'AUTO_TERMINATED']:
            raise SessionTerminatedError(
                f"Session {session_id} has been terminated: {session.termination_reason}"
            )
        
        # Check if session is quarantined
        restriction_level = self.progressive_restriction.session_restrictions.get(
            session_id, 'NORMAL'
        )
        
        if restriction_level == 'QUARANTINE':
            raise SessionQuarantinedError("Session in quarantine - manual review required")
        
        if restriction_level == 'READ_ONLY' and is_write_tool(tool_name):
            raise ToolAccessDeniedError("Session restricted to read-only mode")
        
        # Check if auto-termination should trigger
        should_terminate, reason = self.auto_termination.check_and_terminate(session)
        if should_terminate:
            raise SessionTerminatedError(f"Session auto-terminated: {reason}")
        
        # Execute tool (if all checks pass)
        try:
            result = execute_tool(tool_name, arguments)
            
            # Record invocation in session history
            session.tool_history.append({
                'tool': tool_name,
                'arguments': arguments,
                'timestamp': datetime.now(),
                'result': 'success'
            })
            
            return result
            
        except Exception as e:
            # Record failed invocation
            session.tool_history.append({
                'tool': tool_name,
                'arguments': arguments,
                'timestamp': datetime.now(),
                'result': 'failed',
                'error': str(e)
            })
            raise
```

### Automated Tool Invocation Rollback

**Rollback mechanisms for reversing unauthorized operations:**

```python
class ToolInvocationRollback:
    """
    Rollback system for reversing unsafe tool invocations
    
    Maintains transaction log of tool operations with rollback procedures
    """
    
    def __init__(self):
        self.rollback_registry = {
            'send_email': self.rollback_send_email,
            'modify_database': self.rollback_database_modification,
            'delete_file': self.rollback_file_deletion,
            'grant_permissions': self.rollback_permission_grant,
            # ... more tool-specific rollback handlers
        }
        self.transaction_log = []
    
    def record_invocation(self, session_id, tool_name, arguments, result):
        """
        Record tool invocation with rollback metadata
        """
        transaction = {
            'id': generate_transaction_id(),
            'session_id': session_id,
            'tool': tool_name,
            'arguments': arguments,
            'result': result,
            'timestamp': datetime.now(),
            'rollback_data': self.capture_rollback_data(tool_name, arguments, result)
        }
        
        self.transaction_log.append(transaction)
        return transaction['id']
    
    def rollback_session(self, session_id, reason):
        """
        Rollback all operations from terminated session
        
        Args:
            session_id: Session to rollback
            reason: Reason for rollback
        
        Returns:
            List of rollback results
        """
        # Get all transactions for session
        session_transactions = [
            t for t in self.transaction_log
            if t['session_id'] == session_id
        ]
        
        # Rollback in reverse chronological order
        rollback_results = []
        for transaction in reversed(session_transactions):
            try:
                result = self.rollback_transaction(transaction)
                rollback_results.append({
                    'transaction_id': transaction['id'],
                    'tool': transaction['tool'],
                    'status': 'success',
                    'result': result
                })
            except Exception as e:
                rollback_results.append({
                    'transaction_id': transaction['id'],
                    'tool': transaction['tool'],
                    'status': 'failed',
                    'error': str(e)
                })
        
        log_security_event("session_rollback", {
            'session_id': session_id,
            'reason': reason,
            'transactions_rolled_back': len(rollback_results),
            'failures': len([r for r in rollback_results if r['status'] == 'failed'])
        })
        
        return rollback_results
    
    def rollback_transaction(self, transaction):
        """Execute rollback for specific transaction"""
        tool_name = transaction['tool']
        
        if tool_name in self.rollback_registry:
            rollback_handler = self.rollback_registry[tool_name]
            return rollback_handler(transaction)
        else:
            raise RollbackNotSupportedError(f"No rollback handler for {tool_name}")
    
    def capture_rollback_data(self, tool_name, arguments, result):
        """Capture data needed for rollback"""
        if tool_name == 'modify_database':
            # Capture original database state before modification
            return {
                'table': arguments['table'],
                'record_id': arguments['record_id'],
                'original_values': get_database_snapshot(arguments['table'], arguments['record_id'])
            }
        elif tool_name == 'send_email':
            # Email cannot be unsent, but record for audit
            return {
                'recipients': arguments['to'],
                'subject': arguments['subject'],
                'message_id': result.get('message_id')
            }
        # ... more tool-specific rollback data capture
        
        return {}
    
    def rollback_send_email(self, transaction):
        """
        Rollback email send (best effort)
        
        Cannot unsend emails, but can:
        1. Send recall request (Exchange/Outlook)
        2. Send follow-up clarification email
        3. Flag message for recipients
        """
        rollback_data = transaction['rollback_data']
        
        # Send recall request (if supported by email system)
        try:
            send_recall_request(rollback_data['message_id'])
        except:
            pass
        
        # Send follow-up clarification
        send_email(
            to=rollback_data['recipients'],
            subject=f"DISREGARD: {rollback_data['subject']}",
            body="Please disregard the previous email. It was sent in error due to a security incident."
        )
        
        return "Email recall attempted, clarification sent"
    
    def rollback_database_modification(self, transaction):
        """Rollback database modification"""
        rollback_data = transaction['rollback_data']
        
        # Restore original values
        restore_database_record(
            table=rollback_data['table'],
            record_id=rollback_data['record_id'],
            values=rollback_data['original_values']
        )
        
        return f"Database record {rollback_data['record_id']} restored"
    
    def rollback_file_deletion(self, transaction):
        """Rollback file deletion (if backup exists)"""
        arguments = transaction['arguments']
        
        # Restore from backup/trash
        restore_file_from_backup(arguments['file_path'])
        
        return f"File {arguments['file_path']} restored from backup"
    
    def rollback_permission_grant(self, transaction):
        """Rollback permission grant"""
        arguments = transaction['arguments']
        
        # Revoke granted permissions
        revoke_permissions(
            user=arguments['user'],
            resource=arguments['resource'],
            permissions=arguments['permissions']
        )
        
        return f"Permissions revoked for {arguments['user']}"
```

## Limitations & Trade-offs

**Limitations:**

- **Cannot undo all operations:** Some actions (emails sent, external API calls) cannot be fully reversed
- **Reactive control:** Kill switch activates after attack has begun, not before
- **Human reaction time:** Manual kill switch requires operator to detect and respond (may be too slow for automated attacks)
- **Operational disruption:** Terminating sessions may impact legitimate ongoing operations

**Trade-offs:**

- **Security vs. Availability:** Aggressive auto-termination improves security but may disrupt legitimate users
- **Granularity vs. Simplicity:** Fine-grained progressive restriction is more user-friendly but more complex than binary kill switch
- **Automated vs. Manual:** Automated termination is faster but may have false positives; manual requires operator availability

**Bypass Scenarios:**

- **Rapid attacks:** Fast-moving attacks may complete before kill switch activates
- **Distributed attacks:** Attacks spread across many sessions may not trigger single-session thresholds
- **Irreversible operations:** Some tool invocations cannot be rolled back after execution

## Testing & Validation

**Functional Testing:**

1. **Manual Kill Switch:** Verify operators can successfully terminate sessions on demand
2. **Automated Termination:** Test that rule-based auto-termination triggers correctly
3. **Progressive Restriction:** Validate escalation path (warning → read-only → quarantine → termination)
4. **Rollback Mechanisms:** Test rollback handlers for each supported tool

**Security Testing:**

1. **Attack Scenarios:** Simulate attacks and verify kill switch activates in time
2. **Bypass Attempts:** Test if attackers can evade termination mechanisms
3. **Rollback Completeness:** Verify rollback successfully reverses unauthorized operations
4. **False Positive Rate:** Measure frequency of legitimate sessions incorrectly terminated

**Monitoring & Metrics:**

- **Termination Rate:** Frequency of manual and automated session terminations
- **Time to Termination:** Average time from attack detection to session kill
- **Rollback Success Rate:** Percentage of operations successfully reversed
- **False Positives:** Legitimate sessions incorrectly terminated

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Kill switches for tool execution: Capability to immediately disable specific tools or all tool execution when abuse detected. Session termination on tool abuse: Automatically end sessions when unsafe tool invocation patterns detected. Progressive restriction: When suspicious patterns detected, temporarily reduce user's tool access to read-only until manual review confirms legitimacy."
> — Extracted from unsafe-tool-invocation responsive controls (AI-Native LLM Security)

## Related

- **Combines with:** [[mitigations/anomaly-detection-architecture]], [[mitigations/incident-response-procedures]], [[mitigations/approval-workflow-patterns]]
- **Complements:** [[mitigations/tool-sandboxing-architecture]], [[mitigations/least-privilege-implementation]]
