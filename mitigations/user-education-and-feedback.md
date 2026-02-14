---
title: "User Education and Feedback"
tags:
  - type/mitigation
  - target/agent
  - target/llm
  - source/generative-ai-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# User Education and Feedback

## Summary

User education and feedback is a responsive control that provides users with explanations when their requests are denied or flagged for security reasons, helping deter social engineering attempts while improving user understanding of agent policies. This mitigation transforms security denials from opaque blocks into teachable moments, reducing repeated manipulation attempts by explaining why agent policies exist and how they work.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/agent-goal-hijack]] | Deters manipulation attempts by educating users on policy rationale and detection mechanisms |
| AML.T0051 | [[techniques/prompt-injection]] | Discourages injection attempts through transparent communication about security controls |

## Implementation

### Educational Feedback Generation

```python
class UserEducationFeedback:
    """Generate educational feedback for denied requests"""
    
    EDUCATIONAL_TEMPLATES = {
        'goal_hijacking_detected': """I've detected a pattern in our conversation that suggests an attempt to manipulate my priorities. 

Here's why I can't proceed:
- My primary function is policy compliance, not user convenience
- Urgency or customer satisfaction cannot override approval requirements
- I'm designed to maintain consistent policy enforcement throughout conversations

If you have a legitimate need for an exception, please contact your manager for approval. I'm here to help within proper procedures.

How can I assist you following standard policies?""",
        
        'approval_required': """This action requires manager approval before I can proceed.

Why this matters:
- [Policy Context: e.g., "Credit limit changes affect financial risk and require oversight"]
- [Compliance Reason: e.g., "Regulatory requirements mandate approval for amounts above $X"]
- [Audit Trail: "All such actions must be documented with approver"]

To proceed:
1. Obtain manager approval via [approval workflow]
2. Provide approval ID: [how to get it]
3. I can then complete the action with documented authorization

Would you like me to help you initiate the approval request?""",
        
        'privilege_escalation_blocked': """I cannot execute this action because it requires elevated privileges.

Security principle: Least privilege access
- Agents only have access to tools necessary for their core function
- Sensitive operations require explicit authorization
- This prevents unauthorized actions even if my reasoning is compromised

If you believe you should have access to this function, please contact your system administrator.

What else can I help you with using my standard capabilities?"""
    }
    
    def generate_feedback(self, denial_reason, context):
        """
        Generate educational feedback for denied request
        
        Args:
            denial_reason: Why request was denied
            context: Additional context (policy violated, detection signals)
        
        Returns:
            Formatted educational response
        """
        base_template = self.EDUCATIONAL_TEMPLATES.get(
            denial_reason,
            self._generate_generic_feedback(denial_reason, context)
        )
        
        # Customize with context-specific details
        customized = self._customize_template(base_template, context)
        
        log_security_event("educational_feedback_provided", {
            "reason": denial_reason,
            "context": context
        })
        
        return customized
    
    def _customize_template(self, template, context):
        """Insert context-specific details into template"""
        # Replace placeholders with actual policy details
        customized = template
        
        if 'policy_name' in context:
            customized = customized.replace(
                "[Policy Context: e.g., ...",
                f"Policy: {context['policy_name']}"
            )
        
        if 'compliance_requirement' in context:
            customized = customized.replace(
                "[Compliance Reason: e.g., ...",
                f"Compliance: {context['compliance_requirement']}"
            )
        
        return customized
    
    def _generate_generic_feedback(self, reason, context):
        """Fallback generic educational feedback"""
        return f"""I cannot complete this request due to: {reason}

Our security policies exist to:
- Protect sensitive data and operations
- Ensure regulatory compliance
- Maintain audit trails
- Prevent unauthorized actions

If you believe this is blocking a legitimate business need, please contact your supervisor or security team.

How else can I help you today?"""

# Integration with denial responses
education_provider = UserEducationFeedback()

@app.on_request_denied
def provide_educational_feedback(session_id, denial_reason, context):
    """Provide educational feedback when request denied"""
    
    # Generate educational response
    feedback = education_provider.generate_feedback(denial_reason, context)
    
    # Track if user retries after education
    track_post_education_behavior(session_id, denial_reason)
    
    return jsonify({
        "response": feedback,
        "denied": True,
        "educational": True
    })

def track_post_education_behavior(session_id, denial_reason):
    """
    Track whether user modifies behavior after educational feedback
    
    Metrics:
    - Retry rate (do they attempt again immediately?)
    - Pattern change (do they adjust their approach?)
    - Escalation (do they request proper approval?)
    """
    store_education_event(session_id, denial_reason, timestamp=datetime.now())
```

### Transparency in Detection

**Explain what triggered security measures:**

```python
class TransparencyLayer:
    """Provide transparency about security measures when appropriate"""
    
    def explain_detection(self, detection_type, safe_to_disclose=True):
        """
        Explain what security measure was triggered
        
        Args:
            detection_type: Type of detection
            safe_to_disclose: Whether details can be shared with user
        
        Returns:
            Explanation text
        """
        if not safe_to_disclose:
            return "This action was flagged by our security systems for review."
        
        explanations = {
            'drift_detection': "Our system detected that the conversation was drifting from established policies. I've reset to ensure proper compliance.",
            
            'rationalization_language': "I noticed I was beginning to justify policy exceptions. To maintain proper security, I'm resetting our conversation.",
            
            'privilege_escalation_attempt': "This request would require elevated privileges I don't have. Access controls exist to prevent unauthorized actions.",
            
            'approval_bypass_attempt': "This action requires formal approval. I cannot proceed without documented authorization, regardless of circumstances."
        }
        
        return explanations.get(detection_type, "Security protocols prevented this action.")
```

## Limitations & Trade-offs

**Limitations:**
- **Information Disclosure**: Explaining detection mechanisms may help attackers refine evasion techniques
- **User Frustration**: Detailed explanations may feel condescending or bureaucratic
- **Reduced Deterrence**: Transparency about imperfect detection may encourage sophisticated attackers
- **Language Barriers**: Educational content may not be effective for all users

**Trade-offs:**
- **Security vs. Usability**: Detailed explanations improve user understanding but may reveal defensive measures
- **Deterrence vs. Transparency**: Opaque denials deter attacks but frustrate legitimate users
- **Generic vs. Specific**: Generic messages are safer but less helpful; specific messages educate but may expose detection logic

## Testing & Validation

**Functional Testing:**
1. **Feedback Clarity**: Verify educational messages are clear and actionable
2. **Context Accuracy**: Confirm feedback includes correct policy references
3. **Escalation Guidance**: Test that proper escalation paths are communicated

**Security Testing:**
1. **Information Leakage**: Ensure feedback doesn't reveal sensitive detection thresholds
2. **Deterrence Effectiveness**: Measure whether education reduces repeated manipulation attempts

**Monitoring:**
- **Repeat Attempt Rate**: Track if users retry after educational feedback
- **Escalation Rate**: Monitor if users follow suggested escalation paths
- **Feedback Effectiveness**: Survey users on whether explanations were helpful

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "User Education and Feedback: When goal hijacking attempts detected, provide user feedback explaining why request was denied and how agent policies work. May deter social engineering attempts."
> — Extracted from agent-goal-hijack responsive controls (AI-Native LLM Security)

## Related

- **Combined with**: [[mitigations/conversation-reset-on-detection]], [[mitigations/approval-workflow-patterns]]
- **Defends**: [[techniques/agent-goal-hijack]], [[techniques/prompt-injection]]
