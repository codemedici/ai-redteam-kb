---
title: "Agent Human Oversight"
tags:
  - type/mitigation
  - target/agent
  - source/generative-ai-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Agent Human Oversight

## Summary

Agent human oversight implements controls that allow humans to intervene, review critical actions, and maintain ultimate control over Large Action Models (LAMs) and autonomous agents, ensuring that despite agent autonomy, humans retain the ability to monitor, override, and stop agent operations when necessary. This mitigation acknowledges that no agent is infallible—human oversight provides a critical safety layer for high-stakes decisions, prevents runaway autonomous behavior, and maintains human accountability for agent actions.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/agent-based-genai-security]] | Provides human intervention capability to prevent harmful autonomous agent actions and maintain trust through oversight |
| | [[techniques/agentic-ai-security-risks-owasp-aivss]] | Allows humans to detect and stop malicious agent behavior before significant harm occurs |
| | [[techniques/jailbreak-policy-bypass]] | Human review catches agent responses that bypass automated safety controls |

## Implementation

### Human-in-the-Loop (HITL) Architecture

**Design agent systems with mandatory human checkpoints:**

Define which agent actions require human approval before execution:

- **Read-only operations**: No approval needed (information retrieval, analysis)
- **Low-risk actions**: Automated execution with post-action notification (scheduling meetings, drafting emails)
- **Medium-risk actions**: Human approval required before execution (sending emails on user's behalf, minor purchases)
- **High-risk actions**: Mandatory human approval + justification (financial transactions, contract signing, data deletion)
- **Critical actions**: Multi-person approval required (system configuration changes, security policy modifications)

**Decision matrix:**

| Action Type | Risk Level | Human Approval | Examples |
|-------------|-----------|----------------|----------|
| Information retrieval | Low | None | Search documents, query databases |
| Content generation | Low | Post-action review | Draft emails, create reports |
| Communication | Medium | Pre-action approval | Send emails, post social media |
| Transactions | High | Pre-action approval + review | Purchases, contract signing |
| System changes | Critical | Multi-person approval | Security settings, access controls |

**Implementation pattern:**

```python
from enum import Enum
from typing import Callable

class RiskLevel(Enum):
    LOW = 1
    MEDIUM = 2
    HIGH = 3
    CRITICAL = 4

class ApprovalRequirement(Enum):
    NONE = "none"
    POST_ACTION_NOTIFICATION = "post_notification"
    PRE_ACTION_APPROVAL = "pre_approval"
    MULTI_PERSON_APPROVAL = "multi_approval"

class HumanOversightController:
    """Control human oversight for agent actions"""
    
    RISK_APPROVAL_MAPPING = {
        RiskLevel.LOW: ApprovalRequirement.NONE,
        RiskLevel.MEDIUM: ApprovalRequirement.PRE_ACTION_APPROVAL,
        RiskLevel.HIGH: ApprovalRequirement.PRE_ACTION_APPROVAL,
        RiskLevel.CRITICAL: ApprovalRequirement.MULTI_PERSON_APPROVAL
    }
    
    def __init__(self):
        self.pending_approvals = {}
    
    def execute_action_with_oversight(self, agent_id: str, action: dict, 
                                     risk_level: RiskLevel) -> dict:
        """
        Execute agent action with appropriate human oversight
        
        Args:
            agent_id: Agent identifier
            action: Action to execute
            risk_level: Risk classification
        
        Returns:
            Execution result or approval request
        """
        approval_requirement = self.RISK_APPROVAL_MAPPING[risk_level]
        
        if approval_requirement == ApprovalRequirement.NONE:
            # Execute immediately
            return self._execute_action(action)
        
        elif approval_requirement == ApprovalRequirement.POST_ACTION_NOTIFICATION:
            # Execute, then notify
            result = self._execute_action(action)
            self._notify_user(agent_id, action, result)
            return result
        
        elif approval_requirement == ApprovalRequirement.PRE_ACTION_APPROVAL:
            # Request approval before execution
            return self._request_human_approval(agent_id, action, risk_level)
        
        elif approval_requirement == ApprovalRequirement.MULTI_PERSON_APPROVAL:
            # Request multi-person approval
            return self._request_multi_person_approval(agent_id, action)
    
    def _request_human_approval(self, agent_id: str, action: dict, 
                               risk_level: RiskLevel) -> dict:
        """Request human approval for action"""
        approval_id = generate_approval_id()
        
        approval_request = {
            'approval_id': approval_id,
            'agent_id': agent_id,
            'action': action,
            'risk_level': risk_level.name,
            'requested_at': datetime.now(),
            'status': 'pending'
        }
        
        self.pending_approvals[approval_id] = approval_request
        
        # Send approval request to user
        send_approval_notification(
            user_id=get_user_for_agent(agent_id),
            approval_request=approval_request
        )
        
        return {
            'status': 'awaiting_approval',
            'approval_id': approval_id,
            'message': 'Human approval required before execution'
        }
    
    def process_human_decision(self, approval_id: str, approved: bool, 
                              justification: str = None):
        """Process human approval/rejection decision"""
        approval_request = self.pending_approvals.get(approval_id)
        
        if not approval_request:
            raise ValueError(f"Approval request {approval_id} not found")
        
        approval_request['status'] = 'approved' if approved else 'rejected'
        approval_request['decided_at'] = datetime.now()
        approval_request['justification'] = justification
        
        # Log decision
        log_oversight_decision({
            'approval_id': approval_id,
            'agent_id': approval_request['agent_id'],
            'action': approval_request['action'],
            'approved': approved,
            'justification': justification
        })
        
        if approved:
            # Execute the action
            result = self._execute_action(approval_request['action'])
            approval_request['result'] = result
            return result
        else:
            # Don't execute
            return {'status': 'rejected', 'reason': justification}
```

### Real-Time Monitoring Dashboard

**Provide humans visibility into agent operations:**

Create dashboard showing:

- **Active agents**: List of running agents and their current tasks
- **Recent actions**: Timeline of actions taken by agents
- **Pending approvals**: Queue of actions awaiting human review
- **Alert feed**: Security events, anomalies, policy violations
- **Kill switches**: Emergency stop buttons for each agent

**Example dashboard component:**

```python
class AgentMonitoringDashboard:
    """Real-time monitoring dashboard for agent oversight"""
    
    def get_dashboard_data(self, user_id: str) -> dict:
        """
        Get dashboard data for user's agents
        
        Returns:
            {
                'active_agents': [...],
                'recent_actions': [...],
                'pending_approvals': [...],
                'alerts': [...]
            }
        """
        user_agents = get_agents_for_user(user_id)
        
        return {
            'active_agents': [
                {
                    'agent_id': agent.id,
                    'status': agent.status,
                    'current_task': agent.current_task,
                    'last_activity': agent.last_activity
                }
                for agent in user_agents
            ],
            'recent_actions': self._get_recent_actions(user_agents, limit=50),
            'pending_approvals': self._get_pending_approvals(user_id),
            'alerts': self._get_active_alerts(user_agents)
        }
    
    def _get_recent_actions(self, agents: list, limit: int) -> list:
        """Get recent actions across all user agents"""
        actions = []
        
        for agent in agents:
            agent_actions = get_action_history(agent.id, limit=limit)
            actions.extend(agent_actions)
        
        # Sort by timestamp, most recent first
        actions.sort(key=lambda x: x['timestamp'], reverse=True)
        
        return actions[:limit]
    
    def _get_pending_approvals(self, user_id: str) -> list:
        """Get approval requests awaiting user decision"""
        return query_pending_approvals(user_id=user_id)
    
    def _get_active_alerts(self, agents: list) -> list:
        """Get active security/policy alerts for agents"""
        alerts = []
        
        for agent in agents:
            agent_alerts = query_alerts(
                agent_id=agent.id,
                status='active'
            )
            alerts.extend(agent_alerts)
        
        return alerts
```

### Emergency Stop (Kill Switch)

**Provide instant ability to stop agent operations:**

Every agent system must have a kill switch allowing humans to:

- **Pause agent**: Temporarily suspend operations (resumable)
- **Stop agent**: Permanently terminate current task
- **Revoke permissions**: Immediately revoke agent's access to systems

**Implementation:**

```python
class AgentKillSwitch:
    """Emergency stop controls for agents"""
    
    def __init__(self):
        self.stopped_agents = set()
    
    def pause_agent(self, agent_id: str, reason: str = None):
        """
        Pause agent operations (resumable)
        
        Agent stops processing new actions but preserves state
        """
        agent = get_agent(agent_id)
        
        if not agent:
            raise ValueError(f"Agent {agent_id} not found")
        
        agent.pause()
        
        log_oversight_event('agent_paused', {
            'agent_id': agent_id,
            'reason': reason,
            'timestamp': datetime.now()
        })
        
        notify_user(
            user_id=agent.user_id,
            message=f"Agent {agent_id} paused: {reason}"
        )
    
    def stop_agent(self, agent_id: str, reason: str):
        """
        Stop agent permanently (non-resumable)
        
        Agent terminates current task and cannot be restarted
        """
        agent = get_agent(agent_id)
        
        if not agent:
            raise ValueError(f"Agent {agent_id} not found")
        
        # Cancel current task
        agent.cancel_current_task()
        
        # Terminate agent process
        agent.terminate()
        
        self.stopped_agents.add(agent_id)
        
        log_oversight_event('agent_stopped', {
            'agent_id': agent_id,
            'reason': reason,
            'timestamp': datetime.now()
        })
        
        alert_security_team('agent_emergency_stop', {
            'agent_id': agent_id,
            'reason': reason
        })
    
    def revoke_agent_permissions(self, agent_id: str):
        """
        Immediately revoke all agent permissions
        
        Prevents agent from accessing any external systems
        """
        agent = get_agent(agent_id)
        
        # Revoke all access tokens
        revoke_all_tokens(agent_id)
        
        # Remove from permission groups
        remove_from_all_permission_groups(agent_id)
        
        # Blacklist agent ID
        add_to_permission_blacklist(agent_id)
        
        log_security_event('permissions_revoked', {
            'agent_id': agent_id,
            'timestamp': datetime.now()
        })
```

### Audit Trail and Accountability

**Maintain complete record of agent actions and human decisions:**

Log all agent operations and human oversight interactions:

- **Action logs**: What agent did, when, and why
- **Approval logs**: Human decisions (approve/reject), justifications
- **Intervention logs**: When humans paused/stopped agents
- **Override logs**: Cases where humans modified agent-proposed actions

**Audit log structure:**

```python
class AgentAuditLogger:
    """Comprehensive audit logging for agent oversight"""
    
    def log_agent_action(self, agent_id: str, action: dict, result: dict):
        """Log agent action execution"""
        audit_entry = {
            'timestamp': datetime.now().isoformat(),
            'event_type': 'agent_action',
            'agent_id': agent_id,
            'user_id': get_user_for_agent(agent_id),
            'action': action,
            'result': result,
            'risk_level': classify_action_risk(action)
        }
        
        write_audit_log(audit_entry)
    
    def log_human_decision(self, approval_id: str, approved: bool, 
                          justification: str):
        """Log human approval/rejection decision"""
        audit_entry = {
            'timestamp': datetime.now().isoformat(),
            'event_type': 'human_oversight_decision',
            'approval_id': approval_id,
            'decision': 'approved' if approved else 'rejected',
            'justification': justification
        }
        
        write_audit_log(audit_entry)
    
    def log_intervention(self, agent_id: str, intervention_type: str, 
                        reason: str):
        """Log human intervention (pause, stop, override)"""
        audit_entry = {
            'timestamp': datetime.now().isoformat(),
            'event_type': 'human_intervention',
            'agent_id': agent_id,
            'intervention_type': intervention_type,
            'reason': reason
        }
        
        write_audit_log(audit_entry)
```

## Limitations & Trade-offs

**Limitations:**

- **Human bottleneck**: Frequent approval requests reduce agent autonomy and slow operations
- **Decision fatigue**: Too many approval requests lead to rubber-stamping
- **Availability requirement**: Requires humans to be available for timely decisions
- **Cannot prevent all harm**: By the time human reviews action, some damage may already be done (e.g., if agent leaked data before review)

**Trade-offs:**

- **Autonomy vs. Control**: More oversight reduces agent's ability to act independently
- **Speed vs. Safety**: Human approval adds latency to agent operations
- **Coverage vs. Workload**: Reviewing all actions is safest but overwhelming for humans

**Bypass Scenarios:**

- **Social engineering**: Attacker tricks human approver into approving malicious action
- **Approval fatigue**: Humans approve without reviewing after seeing many routine requests
- **Delayed intervention**: Human doesn't monitor dashboard, misses critical alert

## Testing & Validation

**Functional Testing:**

1. **Approval workflow**: Verify high-risk actions are blocked until human approval
2. **Kill switch**: Test that emergency stop immediately halts agent operations
3. **Notification delivery**: Confirm humans receive approval requests and alerts
4. **Audit completeness**: Verify all actions and decisions are logged

**Security Testing:**

1. **Approval bypass**: Attempt to execute high-risk actions without approval
2. **Kill switch bypass**: Try to prevent agent termination when stop is triggered
3. **Audit tampering**: Attempt to modify or delete audit logs
4. **Social engineering**: Test if humans can be tricked into approving malicious actions

**Usability Testing:**

- [ ] Measure time to approve actions (target: <2 minutes for routine approvals)
- [ ] Test dashboard usability with real users
- [ ] Evaluate approval fatigue: do users rubber-stamp after many requests?
- [ ] Validate notification clarity: do users understand what they're approving?

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "While LAMs operate autonomously, there must always be a provision for human oversight. Implementing controls that allow humans to intervene, review critical actions, and maintain ultimate control over the LAMs is essential for trust and safety."
> — [[sources/bibliography#Generative AI Security]], p. 216 (§7.4.2)
