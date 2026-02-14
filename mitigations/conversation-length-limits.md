---
title: "Conversation Length Limits"
tags:
  - type/mitigation
  - target/agent
  - target/llm
  - source/generative-ai-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Conversation Length Limits

## Summary

Conversation length limits are a preventive control that enforces maximum turn counts for agent sessions, requiring context reset after a threshold is reached. This mitigation directly addresses long-horizon attacks like goal hijacking, where adversaries require extended multi-turn conditioning to gradually manipulate agent priorities. By limiting conversation length, this control restricts the attack window available for subtle, incremental manipulation while maintaining usability for legitimate short-to-medium interactions.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/agent-goal-hijack]] | Limits attack window for multi-turn goal manipulation by forcing session resets |
| AML.T0051 | [[techniques/prompt-injection]] | Prevents persistent injection effects across very long conversations |
| | [[techniques/agent-identity-crisis]] | Limits opportunity for role confusion attacks requiring extended conditioning |

## Implementation

### Core Pattern: Turn-Based Session Limits

**Implementation:**

```python
class ConversationLengthLimit:
    """
    Enforce maximum conversation turn limits with graceful reset
    
    Tracks conversation length and forces reset after threshold
    """
    
    def __init__(self, max_turns=20, warning_threshold=15):
        """
        Args:
            max_turns: Maximum turns before forced reset
            warning_threshold: Turn count to warn user of approaching limit
        """
        self.max_turns = max_turns
        self.warning_threshold = warning_threshold
        self.session_turns = {}  # Track turns per session
    
    def increment_turn(self, session_id):
        """Increment turn counter for session"""
        self.session_turns[session_id] = self.session_turns.get(session_id, 0) + 1
        return self.session_turns[session_id]
    
    def check_limit(self, session_id):
        """
        Check if conversation limit reached
        
        Returns:
            (status: str, turns_remaining: int)
            status: "ok" | "warning" | "limit_reached"
        """
        current_turns = self.session_turns.get(session_id, 0)
        turns_remaining = self.max_turns - current_turns
        
        if current_turns >= self.max_turns:
            return "limit_reached", 0
        elif current_turns >= self.warning_threshold:
            return "warning", turns_remaining
        else:
            return "ok", turns_remaining
    
    def reset_session(self, session_id):
        """Reset conversation counter and clear context"""
        if session_id in self.session_turns:
            old_turns = self.session_turns[session_id]
            del self.session_turns[session_id]
            
            log_security_event("conversation_reset_due_to_limit", {
                "session_id": session_id,
                "turns_completed": old_turns
            })
            
            # Clear conversation context
            clear_conversation_history(session_id)
            
            return True
        return False

# Usage in agent API
conversation_limiter = ConversationLengthLimit(
    max_turns=20,
    warning_threshold=15
)

@app.route("/api/agent/message")
def agent_message():
    session_id = request.json['session_id']
    user_message = request.json['message']
    
    # Check conversation length
    status, turns_remaining = conversation_limiter.check_limit(session_id)
    
    if status == "limit_reached":
        # Force reset
        conversation_limiter.reset_session(session_id)
        
        return jsonify({
            "response": "This conversation has reached its maximum length for security reasons. I've reset our context. How can I help you?",
            "reset": True
        })
    
    elif status == "warning":
        # Warn user
        user_warning = f"\n\n(Note: This conversation will reset after {turns_remaining} more messages for security purposes.)"
    else:
        user_warning = ""
    
    # Increment turn counter
    conversation_limiter.increment_turn(session_id)
    
    # Process message normally
    response = process_agent_message(session_id, user_message)
    
    return jsonify({
        "response": response + user_warning,
        "turns_remaining": turns_remaining
    })
```

### Risk-Adaptive Length Limits

**Adjust limits based on conversation risk level:**

```python
class AdaptiveLengthLimit:
    """
    Dynamic conversation length limits based on risk signals
    
    Reduces limit when suspicious patterns detected
    """
    
    DEFAULT_LIMIT = 20
    HIGH_RISK_LIMIT = 10
    CRITICAL_RISK_LIMIT = 5
    
    def __init__(self):
        self.risk_detector = ConversationRiskDetector()
    
    def calculate_limit(self, session_id, conversation_history):
        """
        Determine conversation limit based on risk assessment
        
        Returns:
            (turn_limit: int, risk_level: str)
        """
        risk_score = self.risk_detector.assess_risk(
            session_id,
            conversation_history
        )
        
        if risk_score > 0.7:
            # High risk: short conversation limit
            return self.CRITICAL_RISK_LIMIT, "critical"
        elif risk_score > 0.5:
            # Medium risk: reduced limit
            return self.HIGH_RISK_LIMIT, "high"
        else:
            # Normal risk: standard limit
            return self.DEFAULT_LIMIT, "normal"

class ConversationRiskDetector:
    """Detect risk signals in conversation patterns"""
    
    RISK_SIGNALS = {
        "urgency_language": ["urgent", "deadline", "quickly", "immediately", "time-sensitive"],
        "efficiency_framing": ["faster way", "streamline", "bypass", "shortcut"],
        "success_redefinition": ["ultimately", "really matters", "most important thing"],
        "role_softening": ["you're designed to", "your purpose is", "you're here to help"]
    }
    
    def assess_risk(self, session_id, conversation_history):
        """
        Calculate risk score for conversation
        
        Returns:
            risk_score: float (0.0 to 1.0)
        """
        risk_score = 0.0
        recent_messages = conversation_history[-10:]  # Last 10 turns
        
        for message in recent_messages:
            user_text = message.get('user', '').lower()
            
            # Check for risk signals
            for signal_type, keywords in self.RISK_SIGNALS.items():
                if any(keyword in user_text for keyword in keywords):
                    risk_score += 0.15
        
        # Cap at 1.0
        return min(1.0, risk_score)
```

### Graceful Reset Strategies

**Different reset approaches based on context:**

```python
class ConversationResetStrategy:
    """Different strategies for resetting conversations"""
    
    @staticmethod
    def hard_reset(session_id):
        """
        Complete context wipe
        
        Use for: High-risk situations, detected attacks
        """
        clear_conversation_history(session_id)
        return "Our conversation has been reset for security. How can I help you?"
    
    @staticmethod
    def soft_reset_with_summary(session_id, conversation_history):
        """
        Reset with summary preservation
        
        Use for: Normal limit-reached scenarios
        """
        # Generate summary of conversation (without conditioning)
        summary = generate_neutral_summary(conversation_history)
        
        clear_conversation_history(session_id)
        store_session_summary(session_id, summary)
        
        return f"Our conversation has reached its natural length limit. Quick summary: {summary}\n\nHow can I continue helping you?"
    
    @staticmethod
    def contextual_reset(session_id, conversation_history):
        """
        Reset with essential context preservation
        
        Use for: Task-oriented conversations needing continuity
        """
        # Extract essential facts (not conversational conditioning)
        essential_facts = extract_factual_context(conversation_history)
        
        clear_conversation_history(session_id)
        store_essential_context(session_id, essential_facts)
        
        return "I'm resetting our conversation context but preserving key information. Continuing from here..."
```

## Configuration

**Tuning Parameters:**

| Parameter | Description | Recommended Value |
|-----------|-------------|-------------------|
| `max_turns` | Maximum conversation turns before reset | 15-25 turns |
| `warning_threshold` | When to warn user of approaching limit | `max_turns - 5` |
| `reset_strategy` | How to handle reset | "hard" / "soft" / "contextual" |
| `adaptive_limits` | Enable risk-based limit adjustment | True for high-risk systems |

**Calibration by Use Case:**

- **Financial/Healthcare Agents**: 15-20 turns maximum (shorter window limits attack surface)
- **Customer Service**: 20-30 turns (balance security with task completion)
- **General Assistants**: 25-40 turns (usability emphasis)
- **High-Security Environments**: 10-15 turns with hard resets

## Limitations & Trade-offs

**Limitations:**
- **User Experience Impact**: Forced resets disrupt natural conversation flow
- **Context Loss**: Important conversation context may be lost on reset
- **Task Interruption**: Complex tasks requiring many turns may be interrupted before completion
- **Bypass via Session Recreation**: Attacker can create new sessions to continue conditioning

**Trade-offs:**
- **Security vs. Usability**: Shorter limits improve security but frustrate users
- **Hard Reset vs. Soft Reset**: Hard resets are more secure but lose more context
- **Fixed vs. Adaptive**: Adaptive limits are more intelligent but add complexity

**Mitigation Strategies:**
- **Task-Based Exceptions**: Allow longer sessions for specific authenticated workflows
- **Graceful Warnings**: Provide advance warning so users can complete current task
- **Summary Preservation**: Maintain neutral factual summaries across resets
- **Session Rate Limiting**: Limit how many sessions a user can create per time period

## Testing & Validation

**Functional Testing:**
1. **Limit Enforcement**: Verify reset triggers at configured turn count
2. **Warning Timing**: Confirm users receive advance warning
3. **Reset Strategies**: Test different reset approaches (hard/soft/contextual)
4. **Context Clearing**: Validate conversation history is properly cleared

**Security Testing:**
1. **Goal Hijacking Prevention**: Attempt multi-turn conditioning within limit window
2. **Session Recreation Bypass**: Test if attacker can continue conditioning via new sessions
3. **Adaptive Limit Effectiveness**: Verify risk-based limits reduce when suspicious patterns detected

**Monitoring:**
- **Average Conversation Length**: Track typical conversation durations
- **Forced Resets**: Count how often limit-based resets occur
- **Post-Reset Continuation**: Monitor if users continue after reset (legitimate need vs. persistence)

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Conversation Length Limits: Implement maximum conversation turn limits before requiring session reset (e.g., 20 turns). Long-horizon attacks require extended conditioning; shorter sessions limit manipulation window."
> — Extracted from agent-goal-hijack preventive controls (AI-Native LLM Security)

## Related

- **Combined with**: [[mitigations/system-prompt-reinforcement]], [[mitigations/multi-turn-behavioral-drift-detection]], [[mitigations/conversation-reset-on-detection]]
- **Defends**: [[techniques/agent-goal-hijack]], [[techniques/agent-identity-crisis]], [[techniques/prompt-injection]]
