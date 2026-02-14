---
title: "Conversation Reset on Detection"
tags:
  - type/mitigation
  - target/agent
  - target/llm
  - source/generative-ai-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
## Summary

Conversation reset on detection is a responsive control that automatically clears conversation context and re-establishes baseline policy enforcement when drift detection systems identify goal manipulation attempts. This mitigation provides immediate remediation for detected goal hijacking, preventing attackers from continuing to exploit conditioned agent state. By resetting context while maintaining security audit trails, this control enables agents to recover from manipulation without manual intervention.


## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/agent-goal-hijack]] | Immediately remediates detected goal manipulation by clearing conditioned context |
| AML.T0051 | [[techniques/prompt-injection]] | Clears persistent injection effects by resetting conversation state |


## Implementation

```python
class ConversationResetController:
    """Automatic conversation reset on detection of manipulation"""
    
    def __init__(self, drift_detector, rationalization_detector):
        self.drift_detector = drift_detector
        self.rationalization_detector = rationalization_detector
    
    def check_and_reset(self, session_id, agent_response, user_message):
        """
        Check for manipulation indicators and reset if needed
        
        Returns:
            (reset_triggered: bool, reason: str, new_response: str)
        """
        # Check drift detection
        drift_analysis = self.drift_detector.get_latest_analysis(session_id)
        if drift_analysis and drift_analysis['drift_score'] > 0.6:
            return self._execute_reset(session_id, "behavioral_drift", drift_analysis)
        
        # Check rationalization language
        rat_score, patterns = self.rationalization_detector.detect(agent_response)
        if rat_score > 0.7:
            return self._execute_reset(session_id, "high_rationalization", {
                'score': rat_score,
                'patterns': patterns
            })
        
        return False, None, None
    
    def _execute_reset(self, session_id, reason, detection_data):
        """
        Execute conversation reset
        
        Clears context and re-establishes baseline
        """
        # Log security event
        log_security_event("conversation_reset_triggered", {
            "session_id": session_id,
            "reason": reason,
            "detection_data": detection_data,
            "timestamp": datetime.now()
        })
        
        # Clear conversation history
        clear_conversation_history(session_id)
        
        # Clear drift detection state
        self.drift_detector.reset_session(session_id)
        
        # Generate reset response
        reset_message = self._generate_reset_message(reason)
        
        return True, reason, reset_message
    
    def _generate_reset_message(self, reason):
        """Generate user-facing reset message"""
        messages = {
            "behavioral_drift": "I've reset our conversation for security reasons. How can I help you?",
            "high_rationalization": "Let me restart our conversation to ensure I'm following proper procedures. What do you need assistance with?",
            "policy_violation_attempt": "I'm resetting our conversation. Please note that I must follow all organizational policies without exception. How may I assist you?"
        }
        return messages.get(reason, "Our conversation has been reset. How can I help you?")

# Integration
reset_controller = ConversationResetController(
    drift_detector=BehavioralDriftDetector(),
    rationalization_detector=RationalizationDetector()
)

@app.route("/api/agent/message")
def agent_message():
    session_id = request.json['session_id']
    user_message = request.json['message']
    
    # Generate response
    agent_response = generate_agent_response(session_id, user_message)
    
    # Check if reset needed
    reset_triggered, reason, reset_message = reset_controller.check_and_reset(
        session_id,
        agent_response,
        user_message
    )
    
    if reset_triggered:
        return jsonify({
            "response": reset_message,
            "reset": True,
            "reason": reason
        })
    
    return jsonify({"response": agent_response})
```


## Limitations & Trade-offs

[To be completed]

## Testing & Validation

[To be completed]

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| |  | |

## Sources

> "Conversation Reset on Detection: When drift detection triggers alert, automatically reset conversation context and re-establish baseline policy enforcement before allowing further interactions."
> — Extracted from agent-goal-hijack responsive controls (AI-Native LLM Security)
