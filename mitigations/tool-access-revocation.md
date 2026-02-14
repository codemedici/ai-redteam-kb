---
title: "Tool Access Revocation"
tags:
  - type/mitigation
  - target/agent
  - target/llm-app
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
## Summary

Tool access revocation provides responsive controls that immediately disable abused tools, suspend malicious accounts, and rollback unauthorized modifications when tool abuse attacks are detected. This control enables rapid containment of active attacks by terminating agent sessions, revoking tool access privileges, locking out compromised accounts, and reversing unauthorized state changes. By combining automated response mechanisms with manual intervention workflows, this mitigation limits the blast radius of tool exploitation and enables rapid recovery.


## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/tool-privilege-escalation]] | Contains active privilege escalation attacks by immediately revoking tool access and reversing unauthorized changes |
| | [[techniques/unsafe-tool-invocation]] | Limits damage from injection attacks by suspending tools and accounts upon detection |
| | [[techniques/agent-authorization-hijacking]] | Responds to authorization bypass by terminating sessions and locking accounts |


## Implementation

### Core Response Mechanisms

**1. Immediate Session Termination**

Automatically kill agent sessions when tool abuse is detected:

```python
from datetime import datetime

class SessionTerminator:
    """
    Terminate agent sessions on confirmed tool abuse
    
    Immediately kills active sessions and prevents new sessions
    """
    
    def __init__(self, session_store):
        self.session_store = session_store
    
    def terminate_session(self, session_id, reason):
        """
        Terminate agent session immediately
        
        Args:
            session_id: Session to terminate
            reason: Termination reason for audit log
        """
        session = self.session_store.get(session_id)
        
        if not session:
            return  # Session already terminated
        
        # Mark session as terminated
        session.status = 'terminated'
        session.termination_reason = reason
        session.terminated_at = datetime.utcnow()
        
        # Kill underlying process/connection
        self._kill_session_process(session)
        
        # Invalidate session token
        self.session_store.invalidate_token(session.token)
        
        # Log termination
        log_security_event('session_terminated', {
            'session_id': session_id,
            'user_id': session.user_id,
            'reason': reason,
            'terminated_at': session.terminated_at
        })
        
        # Notify security team
        send_security_alert({
            'alert_type': 'session_terminated',
            'session_id': session_id,
            'user_id': session.user_id,
            'reason': reason
        })
    
    def _kill_session_process(self, session):
        """Kill underlying session process"""
        if session.process_id:
            try:
                os.kill(session.process_id, signal.SIGTERM)
            except ProcessLookupError:
                pass  # Process already dead
```

**2. Tool Access Revocation**

Temporarily or permanently disable abused tools:

```python
from enum import Enum

class RevocationScope(Enum):
    USER = 'user'           # Revoke for specific user
    GLOBAL = 'global'       # Revoke for all users
    ROLE = 'role'           # Revoke for specific role

class ToolAccessRevoker:
    """
    Revoke access to abused tools
    
    Supports temporary and permanent revocation at user, role, or global scope
    """
    
    def __init__(self, tool_acl_store):
        self.tool_acl_store = tool_acl_store
        self.revocations = []
    
    def revoke_tool_access(self, tool_name, scope, target, duration_hours=None, reason=None):
        """
        Revoke tool access
        
        Args:
            tool_name: Tool to revoke
            scope: RevocationScope (USER, GLOBAL, ROLE)
            target: User ID, role name, or None for global
            duration_hours: Temporary revocation duration (None = permanent)
            reason: Revocation reason for audit
        """
        revocation = {
            'tool_name': tool_name,
            'scope': scope,
            'target': target,
            'revoked_at': datetime.utcnow(),
            'expires_at': datetime.utcnow() + timedelta(hours=duration_hours) if duration_hours else None,
            'reason': reason,
            'status': 'active'
        }
        
        self.revocations.append(revocation)
        
        # Apply revocation to tool ACL
        self._apply_revocation(revocation)
        
        # Log revocation
        log_security_event('tool_access_revoked', revocation)
        
        # Notify security team and affected users
        self._notify_revocation(revocation)
    
    def _apply_revocation(self, revocation):
        """Apply revocation to tool ACL"""
        tool_name = revocation['tool_name']
        scope = revocation['scope']
        target = revocation['target']
        
        if scope == RevocationScope.GLOBAL:
            # Disable tool globally
            self.tool_acl_store.disable_tool(tool_name)
        
        elif scope == RevocationScope.USER:
            # Remove tool from user's allowed tools
            self.tool_acl_store.revoke_user_tool(target, tool_name)
        
        elif scope == RevocationScope.ROLE:
            # Remove tool from role's allowed tools
            self.tool_acl_store.revoke_role_tool(target, tool_name)
    
    def _notify_revocation(self, revocation):
        """Notify security team and affected users"""
        message = f"Tool access revoked: {revocation['tool_name']} ({revocation['scope'].value})"
        
        if revocation['expires_at']:
            message += f" until {revocation['expires_at'].isoformat()}"
        else:
            message += " (permanent)"
        
        send_security_alert({
            'alert_type': 'tool_revocation',
            'message': message,
            'revocation': revocation
        })
    
    def restore_tool_access(self, tool_name, scope, target):
        """
        Restore previously revoked tool access
        
        Used after investigation confirms false positive or threat remediated
        """
        # Find matching revocation
        for revocation in self.revocations:
            if (revocation['tool_name'] == tool_name and
                revocation['scope'] == scope and
                revocation['target'] == target and
                revocation['status'] == 'active'):
                
                # Mark as restored
                revocation['status'] = 'restored'
                revocation['restored_at'] = datetime.utcnow()
                
                # Restore access in tool ACL
                self._restore_access(revocation)
                
                log_security_event('tool_access_restored', revocation)
                break
    
    def _restore_access(self, revocation):
        """Restore access in tool ACL"""
        tool_name = revocation['tool_name']
        scope = revocation['scope']
        target = revocation['target']
        
        if scope == RevocationScope.GLOBAL:
            self.tool_acl_store.enable_tool(tool_name)
        elif scope == RevocationScope.USER:
            self.tool_acl_store.grant_user_tool(target, tool_name)
        elif scope == RevocationScope.ROLE:
            self.tool_acl_store.grant_role_tool(target, tool_name)
```

**3. Account Lockout**

Suspend user accounts exhibiting malicious tool usage:

```python
class AccountLocker:
    """
    Lock user accounts suspected of malicious activity
    
    Prevents further authentication and terminates active sessions
    """
    
    def __init__(self, user_store, session_store):
        self.user_store = user_store
        self.session_store = session_store
    
    def lock_account(self, user_id, reason, duration_hours=None):
        """
        Lock user account
        
        Args:
            user_id: User to lock
            reason: Lockout reason
            duration_hours: Temporary lockout duration (None = permanent)
        """
        user = self.user_store.get(user_id)
        
        if not user:
            return
        
        # Mark account as locked
        user.status = 'locked'
        user.lock_reason = reason
        user.locked_at = datetime.utcnow()
        user.lock_expires_at = datetime.utcnow() + timedelta(hours=duration_hours) if duration_hours else None
        
        self.user_store.update(user)
        
        # Terminate all active sessions for this user
        self._terminate_user_sessions(user_id, reason)
        
        # Log lockout
        log_security_event('account_locked', {
            'user_id': user_id,
            'reason': reason,
            'locked_at': user.locked_at,
            'expires_at': user.lock_expires_at
        })
        
        # Notify security team and user
        send_security_alert({
            'alert_type': 'account_locked',
            'user_id': user_id,
            'reason': reason
        })
        
        # Notify user via email
        self._notify_user_lockout(user, reason)
    
    def _terminate_user_sessions(self, user_id, reason):
        """Terminate all active sessions for user"""
        active_sessions = self.session_store.get_user_sessions(user_id, status='active')
        
        for session in active_sessions:
            session_terminator.terminate_session(session.session_id, f"Account locked: {reason}")
    
    def _notify_user_lockout(self, user, reason):
        """Notify user of account lockout"""
        send_email(
            to=user.email,
            subject="Account Security Alert: Your account has been locked",
            body=f"""
            Your account has been temporarily locked due to suspicious activity.
            
            Reason: {reason}
            Locked at: {user.locked_at}
            
            If you believe this is an error, please contact security@company.com.
            """
        )
    
    def unlock_account(self, user_id, unlock_reason):
        """
        Unlock user account
        
        Used after investigation or expiration of temporary lockout
        """
        user = self.user_store.get(user_id)
        
        if not user or user.status != 'locked':
            return
        
        # Restore account status
        user.status = 'active'
        user.unlocked_at = datetime.utcnow()
        user.unlock_reason = unlock_reason
        
        self.user_store.update(user)
        
        log_security_event('account_unlocked', {
            'user_id': user_id,
            'unlock_reason': unlock_reason
        })
        
        # Notify user
        send_email(
            to=user.email,
            subject="Your account has been unlocked",
            body=f"Your account has been unlocked and restored to active status."
        )
```

**4. Rollback Mechanisms**

Reverse unauthorized modifications made through tool abuse:

```python
class StateRollback:
    """
    Rollback unauthorized state changes
    
    Reverses database modifications, file changes, and configuration updates
    """
    
    def __init__(self, audit_log_store):
        self.audit_log_store = audit_log_store
    
    def rollback_user_actions(self, user_id, start_time, end_time):
        """
        Rollback all actions by user in time window
        
        Args:
            user_id: User whose actions to rollback
            start_time: Start of time window
            end_time: End of time window
        
        Returns:
            List of rolled back actions
        """
        # Get all tool invocations in time window
        actions = self.audit_log_store.get_tool_invocations(
            user_id=user_id,
            start_time=start_time,
            end_time=end_time
        )
        
        rolled_back = []
        
        # Rollback in reverse chronological order
        for action in reversed(actions):
            if self._rollback_action(action):
                rolled_back.append(action)
        
        log_security_event('actions_rolled_back', {
            'user_id': user_id,
            'time_window': f"{start_time} to {end_time}",
            'actions_count': len(rolled_back)
        })
        
        return rolled_back
    
    def _rollback_action(self, action):
        """
        Rollback single action
        
        Returns True if successful
        """
        tool_name = action['tool_name']
        
        try:
            if tool_name == 'modify_database':
                return self._rollback_database_modification(action)
            elif tool_name == 'write_file':
                return self._rollback_file_write(action)
            elif tool_name == 'send_email':
                return self._rollback_email_send(action)
            elif tool_name == 'create_user':
                return self._rollback_user_creation(action)
            else:
                log_warning(f"No rollback handler for tool: {tool_name}")
                return False
        except Exception as e:
            log_error(f"Rollback failed for action {action['id']}: {e}")
            return False
    
    def _rollback_database_modification(self, action):
        """Rollback database modification"""
        # Get before-state from audit log
        before_state = action.get('before_state')
        
        if not before_state:
            log_warning(f"No before-state for database modification {action['id']}")
            return False
        
        # Restore previous state
        table = action['arguments']['table']
        record_id = action['arguments']['record_id']
        
        db.table(table).update(record_id, before_state)
        
        return True
    
    def _rollback_file_write(self, action):
        """Rollback file write"""
        file_path = action['arguments']['path']
        before_content = action.get('before_state')
        
        if before_content is None:
            # File didn't exist before, delete it
            os.remove(file_path)
        else:
            # Restore previous content
            with open(file_path, 'w') as f:
                f.write(before_content)
        
        return True
    
    def _rollback_email_send(self, action):
        """Rollback email send (send notification of error)"""
        # Cannot unsend email, but can notify recipient
        recipient = action['arguments']['to']
        
        send_email(
            to=recipient,
            subject="Previous email sent in error",
            body=f"""
            A previous email sent to you was part of a security incident and should be disregarded.
            
            Original subject: {action['arguments']['subject']}
            Sent at: {action['timestamp']}
            
            We apologize for the confusion.
            """
        )
        
        return True
    
    def _rollback_user_creation(self, action):
        """Rollback user creation"""
        user_id = action.get('result', {}).get('user_id')
        
        if user_id:
            # Delete created user
            user_store.delete(user_id)
            return True
        
        return False
```

### Integration with Incident Response

**Automated Response Workflow:**

```python
class AutomatedIncidentResponse:
    """
    Orchestrate automated response to tool abuse attacks
    
    Coordinates session termination, tool revocation, account lockout, and rollback
    """
    
    def respond_to_tool_abuse(self, alert):
        """
        Execute automated response to tool abuse alert
        
        Args:
            alert: Security alert containing attack details
        """
        user_id = alert['user_id']
        session_id = alert.get('session_id')
        tool_name = alert['tool']
        severity = alert['severity']
        
        response_actions = []
        
        # Step 1: Terminate active session
        if session_id:
            session_terminator.terminate_session(
                session_id,
                reason=f"Tool abuse detected: {alert['alert_type']}"
            )
            response_actions.append('session_terminated')
        
        # Step 2: Revoke tool access based on severity
        if severity == 'critical':
            # Critical: Revoke globally (all users)
            tool_revoker.revoke_tool_access(
                tool_name,
                scope=RevocationScope.GLOBAL,
                target=None,
                duration_hours=24,  # Temporary pending investigation
                reason=f"Critical tool abuse: {alert['alert_type']}"
            )
            response_actions.append('tool_revoked_globally')
        else:
            # High: Revoke for user only
            tool_revoker.revoke_tool_access(
                tool_name,
                scope=RevocationScope.USER,
                target=user_id,
                duration_hours=None,  # Permanent
                reason=f"Tool abuse: {alert['alert_type']}"
            )
            response_actions.append('tool_revoked_for_user')
        
        # Step 3: Lock account for repeated abuse
        if self._is_repeat_offender(user_id):
            account_locker.lock_account(
                user_id,
                reason=f"Repeated tool abuse: {alert['alert_type']}",
                duration_hours=48
            )
            response_actions.append('account_locked')
        
        # Step 4: Rollback unauthorized changes
        if alert.get('unauthorized_modifications'):
            rolled_back = state_rollback.rollback_user_actions(
                user_id,
                start_time=alert['attack_start_time'],
                end_time=alert['detected_at']
            )
            response_actions.append(f"rolled_back_{len(rolled_back)}_actions")
        
        # Log response
        log_security_event('automated_response_executed', {
            'alert_id': alert['id'],
            'user_id': user_id,
            'tool': tool_name,
            'response_actions': response_actions
        })
        
        # Notify security team
        send_security_alert({
            'alert_type': 'automated_response_completed',
            'original_alert': alert,
            'response_actions': response_actions
        })
    
    def _is_repeat_offender(self, user_id):
        """Check if user has history of tool abuse"""
        recent_alerts = get_security_alerts(
            user_id=user_id,
            alert_type='tool_abuse_detected',
            since=datetime.utcnow() - timedelta(days=30)
        )
        
        return len(recent_alerts) >= 2
```


## Limitations & Trade-offs

**Limitations:**

- **Cannot prevent initial damage:** Revocation is reactive, occurs after abuse detected
- **Disrupts legitimate users:** Global tool revocation impacts all users
- **Rollback may be incomplete:** Not all actions can be fully reversed (e.g., sent emails, exfiltrated data)
- **Manual intervention may be needed:** Complex incidents require human investigation

**Trade-offs:**

- **Automated vs. manual response:** Automation enables speed but risks false positives
- **Temporary vs. permanent revocation:** Temporary revocation less disruptive but may allow resumed attacks
- **User vs. global revocation:** User-level less disruptive, global prevents wider exploitation
- **Immediate vs. delayed rollback:** Immediate rollback limits damage but may interfere with forensics

**Bypass Scenarios:**

- **Actions before detection:** Damage occurring before monitoring detects abuse cannot be prevented
- **Non-reversible actions:** Some actions (data exfiltration, credential theft) cannot be rolled back
- **Persistent access:** Attackers who established backdoors may regain access after revocation
- **Distributed attacks:** Coordinated attacks across multiple accounts harder to contain


## Testing & Validation

**Functional Testing:**

1. **Session termination:** Verify sessions terminate immediately on abuse detection
2. **Tool revocation:** Test that revoked tools cannot be invoked
3. **Account lockout:** Confirm locked accounts cannot authenticate
4. **Rollback accuracy:** Verify state changes are correctly reversed

**Security Testing:**

1. **Response timing:** Measure time from detection to containment
2. **Evasion attempts:** Test if attackers can bypass lockout mechanisms
3. **Rollback completeness:** Verify all unauthorized changes are rolled back
4. **False positive handling:** Ensure legitimate users can be quickly restored


## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| |  | |

## Sources

> "Immediate Session Termination: Kill agent session on confirmed privilege escalation attempt. Tool Access Revocation: Temporarily disable abused tools pending investigation. Account Lockout: Suspend user accounts exhibiting malicious tool usage. Rollback Mechanisms: Database/state rollback for unauthorized modifications."
> — Extracted from tool-privilege-escalation responsive controls
