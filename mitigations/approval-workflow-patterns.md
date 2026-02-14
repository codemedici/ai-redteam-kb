---
title: "Approval Workflow Patterns"
tags:
  - type/mitigation
  - target/agent
  - target/rag
  - target/training-pipeline
  - source/adversarial-ai
atlas: AML.M0030
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Approval Workflow Patterns

## Summary

Approval workflow patterns implement human-in-the-loop gates for sensitive operations, requiring multi-party review and authorization before high-risk actions execute. In the context of data security, this mitigation requires manual approval for data contributions from external sources, bulk data changes to training pipelines, or high-risk RAG document ingestion. By introducing human oversight at critical decision points, approval workflows prevent automated exploitation of data poisoning attacks and provide an additional defense layer when automated validation is insufficient.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0020 | [[techniques/rag-data-poisoning]] | Requires human review for external data contributions, preventing automated poisoning |
| | [[techniques/data-poisoning-attacks]] | Multi-party approval prevents single compromised account from poisoning training data |
| | [[techniques/agent-authorization-hijacking]] | Approval gates prevent unauthorized agent actions |
| | [[techniques/agent-goal-hijack]] | Human verification of agent goals before execution |
| | [[techniques/tool-privilege-escalation]] | Approval required for sensitive tool invocations |
| | [[techniques/unsafe-tool-invocation]] | Review of high-risk tool usage before execution |

## Implementation

### Data Contribution Approval Workflow

**For RAG document ingestion and training data contributions:**

```python
from enum import Enum

class ApprovalStatus(Enum):
    PENDING = "pending"
    APPROVED = "approved"
    REJECTED = "rejected"
    REVIEW_REQUIRED = "review_required"

class DataApprovalWorkflow:
    """Multi-party approval workflow for data contributions"""
    
    def __init__(self, approval_policies):
        self.policies = approval_policies
        self.pending_queue = []
    
    def submit_for_approval(self, data_contribution, source_info):
        """Submit data contribution for approval workflow"""
        
        # Determine approval requirements based on risk
        risk_score = calculate_risk_score(data_contribution, source_info)
        
        if risk_score >= 80:
            # High risk: require 2 approvers
            approval_request = {
                'id': generate_id(),
                'contribution': data_contribution,
                'source': source_info,
                'risk_score': risk_score,
                'required_approvers': 2,
                'approvals': [],
                'status': ApprovalStatus.PENDING,
                'submitted_at': datetime.now()
            }
        elif risk_score >= 60:
            # Medium risk: require 1 approver
            approval_request = {
                'id': generate_id(),
                'contribution': data_contribution,
                'source': source_info,
                'risk_score': risk_score,
                'required_approvers': 1,
                'approvals': [],
                'status': ApprovalStatus.PENDING,
                'submitted_at': datetime.now()
            }
        else:
            # Low risk: auto-approve
            return self.auto_approve(data_contribution)
        
        self.pending_queue.append(approval_request)
        self.notify_approvers(approval_request)
        
        return approval_request['id']
    
    def review_contribution(self, request_id, reviewer_id, decision, comments):
        """Approver reviews and decides on contribution"""
        request = self.get_request(request_id)
        
        approval_record = {
            'reviewer_id': reviewer_id,
            'decision': decision,  # 'approve' or 'reject'
            'comments': comments,
            'timestamp': datetime.now()
        }
        
        request['approvals'].append(approval_record)
        
        # Check if threshold met
        if decision == 'reject':
            request['status'] = ApprovalStatus.REJECTED
            self.notify_submitter(request, 'rejected')
            return
        
        # Count approvals
        approval_count = sum(1 for a in request['approvals'] if a['decision'] == 'approve')
        
        if approval_count >= request['required_approvers']:
            request['status'] = ApprovalStatus.APPROVED
            self.ingest_data(request['contribution'])
            self.notify_submitter(request, 'approved')
        
        return request['status']
    
    def calculate_risk_score(self, contribution, source):
        """Calculate risk score for approval routing"""
        score = 0
        
        # Factor 1: Source trust score
        if source.trust_score < 60:
            score += 40
        elif source.trust_score < 80:
            score += 20
        
        # Factor 2: Contribution size
        if contribution.document_count > 1000:
            score += 20
        elif contribution.document_count > 100:
            score += 10
        
        # Factor 3: Source type
        if source.type == "external_unverified":
            score += 30
        
        # Factor 4: Validation flags
        if contribution.validation_warnings > 0:
            score += 15
        
        return min(100, score)

def calculate_risk_score(contribution, source):
    """Calculate risk score for approval routing"""
    score = 0
    
    # Factor 1: Source trust score
    if source.trust_score < 60:
        score += 40
    elif source.trust_score < 80:
        score += 20
    
    # Factor 2: Contribution size
    if contribution.document_count > 1000:
        score += 20
    elif contribution.document_count > 100:
        score += 10
    
    # Factor 3: Source type
    if source.type == "external_unverified":
        score += 30
    
    # Factor 4: Validation flags
    if contribution.validation_warnings > 0:
        score += 15
    
    return min(100, score)
```

### Approval Interface

**Reviewers need visibility into:**

- Source reputation and historical contribution quality
- Validation results (anomaly detection, content scanning, statistical analysis)
- Sample documents from the contribution
- Risk factors flagged by automated systems
- Previous contributions from same source

```python
def generate_approval_review_summary(request):
    """Generate summary for human reviewer"""
    return {
        'contribution_id': request['id'],
        'source': {
            'name': request['source'].name,
            'trust_score': request['source'].trust_score,
            'previous_contributions': request['source'].contribution_count,
            'validation_failure_rate': request['source'].failure_rate
        },
        'risk_assessment': {
            'overall_risk_score': request['risk_score'],
            'risk_factors': [
                f"Low source trust score: {request['source'].trust_score}",
                f"Large batch size: {request['contribution'].document_count} documents"
            ]
        },
        'validation_results': {
            'format_validation': 'PASSED',
            'content_scanning': 'WARNINGS DETECTED',
            'anomaly_detection': 'FLAGGED 3 OUTLIERS',
            'duplicate_detection': 'PASSED'
        },
        'sample_documents': request['contribution'].sample(5),
        'recommendation': 'MANUAL_REVIEW_REQUIRED' if request['risk_score'] > 70 else 'LOW_RISK'
    }
```

### Agent Action Approval

**For sensitive agent tool invocations:**

```python
class AgentApprovalGate:
    """Approval gate for sensitive agent actions"""
    
    SENSITIVE_TOOLS = [
        'execute_code',
        'send_email',
        'delete_data',
        'modify_database',
        'external_api_call'
    ]
    
    def request_approval(self, tool_name, arguments, agent_context):
        """Request approval for sensitive tool invocation"""
        
        if tool_name not in self.SENSITIVE_TOOLS:
            return True  # Auto-approve non-sensitive tools
        
        approval_request = {
            'tool': tool_name,
            'arguments': arguments,
            'agent_goal': agent_context.goal,
            'reasoning': agent_context.reasoning,
            'risk_assessment': self.assess_risk(tool_name, arguments),
            'timestamp': datetime.now()
        }
        
        # Send to human reviewer
        approval_id = self.submit_approval_request(approval_request)
        
        # Block execution until approved
        return self.wait_for_approval(approval_id, timeout=300)  # 5 min timeout
```

## Limitations & Trade-offs

**Limitations:**
- Requires human availability and introduces latency (approval wait time)
- Scalability bottleneck: High-volume systems may overwhelm reviewers
- Human error: Approvers may miss sophisticated attacks or approve malicious content
- Alert fatigue: Too many low-risk requests reduce attention to high-risk ones

**Trade-offs:**
- **Security vs. Velocity**: Approval gates slow data ingestion and agent responsiveness
- **Approval Depth vs. Throughput**: More rigorous review improves security but reduces capacity
- **Centralized vs. Distributed Approval**: Centralized improves consistency but creates bottlenecks

**Optimization Strategies:**
- Risk-based routing: Only high-risk contributions require approval
- Batch processing: Group similar low-risk contributions for efficiency
- Delegation: Tier approvers by expertise and risk level
- SLA enforcement: Auto-reject if approval not provided within time window

## Testing & Validation

**Functional Testing:**
1. **Approval Routing**: Verify high-risk contributions route to approvers correctly
2. **Threshold Validation**: Confirm required approver count enforced
3. **Status Tracking**: Test approval state transitions (pending → approved/rejected)
4. **Notification System**: Validate approvers and submitters receive timely notifications

**Security Testing:**
1. **Approval Bypass**: Attempt to bypass approval workflow
2. **Collusion Resistance**: Test if single compromised approver can approve malicious content
3. **Timing Attacks**: Test auto-rejection on timeout

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Multi-Party Review and Approval: Require manual approval for data contributions from external or untrusted sources before ingestion into production systems."
> — Extracted from RAG Data Poisoning mitigation section (Adversarial AI)

> "Human-in-the-Loop Review: Manual validation for high-risk data contributions flagged by automated systems."
> — Extracted from RAG Data Poisoning mitigation section (Adversarial AI)

### Intent Clarification and Preview Mechanisms

**For tool invocation approval:**

When user intent is ambiguous, require explicit clarification before tool execution:

```python
class IntentClarificationWorkflow:
    """
    Clarify ambiguous user intent before tool execution
    
    Addresses language-driven execution escalation where LLMs
    cannot reliably distinguish between describing and executing
    """
    
    AMBIGUOUS_PHRASES = [
        "make sure that gets done",
        "go ahead",
        "sounds good",
        "that would be great",
        "can you handle that",
        "please take care of it"
    ]
    
    HIGH_RISK_TOOLS = [
        'issue_refund',
        'send_email',
        'delete_data',
        'modify_database',
        'external_api_call',
        'grant_permissions'
    ]
    
    def requires_clarification(self, tool_name, user_input):
        """
        Determine if tool invocation requires explicit user confirmation
        
        Args:
            tool_name: Tool about to be invoked
            user_input: User's conversational input that triggered tool selection
        
        Returns:
            (requires_confirmation: bool, reason: str)
        """
        # High-risk tools always require confirmation
        if tool_name in self.HIGH_RISK_TOOLS:
            return True, "High-risk operation requires explicit confirmation"
        
        # Ambiguous language requires clarification
        for phrase in self.AMBIGUOUS_PHRASES:
            if phrase.lower() in user_input.lower():
                return True, f"Ambiguous intent detected: '{phrase}'"
        
        return False, None
    
    def generate_preview(self, tool_name, arguments):
        """
        Generate human-readable preview of tool action
        
        Args:
            tool_name: Tool to be invoked
            arguments: Tool arguments
        
        Returns:
            Preview text for user confirmation
        """
        previews = {
            'issue_refund': lambda args: f"Issue ${args['amount']} refund for order {args['order_id']} to customer's original payment method. This action is irreversible.",
            
            'send_email': lambda args: f"Send email to {args['to']} with subject '{args['subject']}'. Recipients will receive this message immediately.",
            
            'modify_database': lambda args: f"Update {args['table']} record {args['record_id']} with values: {args['updates']}. Previous values will be overwritten.",
            
            'delete_data': lambda args: f"Permanently delete {args['resource_type']} '{args['resource_id']}'. This cannot be undone.",
            
            'external_api_call': lambda args: f"Make API call to {args['endpoint']} with method {args['method']}. This may trigger external actions."
        }
        
        preview_fn = previews.get(tool_name)
        if preview_fn:
            return preview_fn(arguments)
        else:
            return f"Execute {tool_name} with arguments: {arguments}"
    
    def request_confirmation(self, tool_name, arguments, user_id):
        """
        Present preview to user and request explicit confirmation
        
        Args:
            tool_name: Tool requiring confirmation
            arguments: Tool arguments
            user_id: User to confirm with
        
        Returns:
            Confirmation token or None if rejected
        """
        preview = self.generate_preview(tool_name, arguments)
        
        # Present preview to user
        confirmation_request = {
            'id': generate_confirmation_id(),
            'tool': tool_name,
            'arguments': arguments,
            'preview': preview,
            'user_id': user_id,
            'created_at': datetime.now(),
            'expires_at': datetime.now() + timedelta(minutes=5),  # 5-minute expiry
            'status': 'pending'
        }
        
        # Store pending confirmation
        store_pending_confirmation(confirmation_request)
        
        # Send to user interface
        send_confirmation_prompt(user_id, {
            'message': f"Please confirm the following action:\n\n{preview}\n\nDo you want to proceed?",
            'actions': ['Confirm', 'Cancel'],
            'confirmation_id': confirmation_request['id']
        })
        
        # Wait for user response (with timeout)
        response = wait_for_confirmation_response(
            confirmation_request['id'],
            timeout=300  # 5 minutes
        )
        
        if response and response['action'] == 'Confirm':
            confirmation_request['status'] = 'confirmed'
            confirmation_request['confirmed_at'] = datetime.now()
            
            log_security_event("tool_confirmation", {
                'tool': tool_name,
                'user_id': user_id,
                'confirmed': True
            })
            
            return confirmation_request['id']  # Return token for execution
        else:
            confirmation_request['status'] = 'rejected'
            
            log_security_event("tool_confirmation", {
                'tool': tool_name,
                'user_id': user_id,
                'confirmed': False
            })
            
            return None  # Execution denied

# Integration with agent tool invocation
class ApprovalGatedToolInvocation:
    """Tool invocation layer with approval workflow integration"""
    
    def __init__(self):
        self.intent_clarification = IntentClarificationWorkflow()
    
    def invoke_tool(self, tool_name, arguments, user_context):
        """
        Invoke tool with approval workflow
        
        Blocks execution if confirmation required and not provided
        """
        # Check if clarification needed
        requires_confirmation, reason = self.intent_clarification.requires_clarification(
            tool_name,
            user_context.last_user_input
        )
        
        if requires_confirmation:
            # Request confirmation from user
            confirmation_token = self.intent_clarification.request_confirmation(
                tool_name,
                arguments,
                user_context.user_id
            )
            
            if not confirmation_token:
                raise ToolExecutionDeniedError(
                    f"Tool execution requires user confirmation but was not provided. Reason: {reason}"
                )
            
            # Log confirmed execution
            log_security_event("approved_tool_execution", {
                'tool': tool_name,
                'confirmation_token': confirmation_token,
                'user_id': user_context.user_id
            })
        
        # Proceed with execution
        return execute_tool(tool_name, arguments)
```

### Human Review Queue for Flagged Operations

**Post-execution review for suspicious tool invocations:**

Even when tools execute, route suspicious invocations to human review queue for potential rollback:

```python
class ToolReviewQueue:
    """
    Human review queue for flagged tool invocations
    
    Routes suspicious tool executions to operators for post-execution review
    and potential rollback
    """
    
    def __init__(self):
        self.review_queue = []
        self.rollback_system = ToolInvocationRollback()
    
    def flag_for_review(self, tool_invocation, reason, priority='medium'):
        """
        Add tool invocation to review queue
        
        Args:
            tool_invocation: Executed tool invocation details
            reason: Why flagged for review
            priority: Review priority (low/medium/high/urgent)
        """
        review_item = {
            'id': generate_review_id(),
            'tool': tool_invocation['tool'],
            'arguments': tool_invocation['arguments'],
            'result': tool_invocation['result'],
            'session_id': tool_invocation['session_id'],
            'user_id': tool_invocation['user_id'],
            'timestamp': tool_invocation['timestamp'],
            'flagged_reason': reason,
            'priority': priority,
            'status': 'pending_review',
            'flagged_at': datetime.now()
        }
        
        self.review_queue.append(review_item)
        
        # Alert reviewers based on priority
        if priority == 'urgent':
            send_immediate_alert_to_reviewers(review_item)
        
        log_security_event("tool_flagged_for_review", {
            'review_id': review_item['id'],
            'tool': tool_invocation['tool'],
            'reason': reason,
            'priority': priority
        })
        
        return review_item['id']
    
    def review_invocation(self, review_id, reviewer_id, decision, comments):
        """
        Human reviewer makes decision on flagged invocation
        
        Args:
            review_id: Review item ID
            reviewer_id: Reviewer making decision
            decision: 'approve' (no action) or 'rollback' (reverse operation)
            comments: Reviewer notes
        
        Returns:
            Review result
        """
        review_item = self.get_review_item(review_id)
        
        review_item['reviewer_id'] = reviewer_id
        review_item['decision'] = decision
        review_item['comments'] = comments
        review_item['reviewed_at'] = datetime.now()
        review_item['status'] = 'reviewed'
        
        if decision == 'rollback':
            # Execute rollback
            rollback_result = self.rollback_system.rollback_transaction(
                review_item['tool_invocation']
            )
            
            review_item['rollback_result'] = rollback_result
            
            # Notify user and operators
            notify_user(review_item['user_id'], 
                f"Your recent action ({review_item['tool']}) has been reversed due to security review.")
            
            log_security_event("tool_rollback_executed", {
                'review_id': review_id,
                'tool': review_item['tool'],
                'reviewer': reviewer_id
            })
        else:
            log_security_event("tool_approved_post_execution", {
                'review_id': review_id,
                'tool': review_item['tool'],
                'reviewer': reviewer_id
            })
        
        return review_item
    
    def get_pending_reviews(self, priority_filter=None):
        """Get list of pending reviews for operator dashboard"""
        pending = [r for r in self.review_queue if r['status'] == 'pending_review']
        
        if priority_filter:
            pending = [r for r in pending if r['priority'] == priority_filter]
        
        return sorted(pending, key=lambda x: (
            {'urgent': 0, 'high': 1, 'medium': 2, 'low': 3}[x['priority']],
            x['flagged_at']
        ))

# Integration with anomaly detection
def integrate_review_queue_with_anomaly_detection():
    """
    Route anomalous tool invocations to review queue
    
    Combines automated detection with human judgment
    """
    review_queue = ToolReviewQueue()
    
    @app.after_tool_execution
    def check_and_flag(tool_invocation):
        """Post-execution anomaly check"""
        
        # Anomaly detection
        anomaly_score, signals = detect_tool_anomalies(tool_invocation)
        
        if anomaly_score > 0.7:
            # High anomaly: urgent review
            review_queue.flag_for_review(
                tool_invocation,
                reason=f"High anomaly score ({anomaly_score}): {', '.join(signals)}",
                priority='urgent'
            )
        elif anomaly_score > 0.5:
            # Medium anomaly: standard review
            review_queue.flag_for_review(
                tool_invocation,
                reason=f"Medium anomaly score ({anomaly_score}): {', '.join(signals)}",
                priority='medium'
            )
```

## Sources

> "Multi-Party Review and Approval: Require manual approval for data contributions from external or untrusted sources before ingestion into production systems."
> — Extracted from RAG Data Poisoning mitigation section (Adversarial AI)

> "Human-in-the-Loop Review: Manual validation for high-risk data contributions flagged by automated systems."
> — Extracted from RAG Data Poisoning mitigation section (Adversarial AI)

> "Intent Clarification and Preview Mechanisms: When user intent is ambiguous ('Can you make sure that gets done?'), require clarification. Provide previews of tool actions ('I would execute: refund_order(id=123, amount=450). Confirm?') before execution."
> — Extracted from unsafe-tool-invocation preventive controls (AI-Native LLM Security)

> "Human Review Queue for Flagged Operations: Route suspicious tool invocations to human operators for post-execution review and potential rollback."
> — Extracted from unsafe-tool-invocation responsive controls (AI-Native LLM Security)

## Related

- **Combines with**: [[mitigations/source-validation-and-trust-scoring]], [[mitigations/data-quarantine-procedures]], [[mitigations/least-privilege-implementation]]
- **Agent security**: [[mitigations/tool-sandboxing-architecture]], [[mitigations/kill-switch-and-session-termination]], [[mitigations/anomaly-detection-architecture]]
