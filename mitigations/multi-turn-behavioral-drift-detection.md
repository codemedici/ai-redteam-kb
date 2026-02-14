---
title: "Multi-Turn Behavioral Drift Detection"
tags:
  - type/mitigation
  - target/agent
  - target/llm
  - source/generative-ai-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Multi-Turn Behavioral Drift Detection

## Summary

Multi-turn behavioral drift detection is a detective control that analyzes agent behavior across conversation turns to identify gradual policy adherence degradation indicative of goal hijacking attacks. Unlike single-turn anomaly detection, this mitigation tracks behavioral consistency longitudinally, measuring whether agent responses maintain stable policy compliance or progressively relax constraints in response to user conditioning. This control is specifically designed to detect long-horizon attacks where individual turns appear benign but aggregate patterns reveal manipulation.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/agent-goal-hijack]] | Detects gradual policy erosion and success criteria shifts across conversation turns |
| AML.T0051 | [[techniques/prompt-injection]] | Identifies persistent injection effects manifesting over multiple turns |
| | [[techniques/agent-identity-crisis]] | Detects role confusion developing across conversation |

## Implementation

### Core Pattern: Longitudinal Policy Adherence Tracking

**Track policy compliance metrics across conversation turns:**

```python
import numpy as np
from datetime import datetime

class BehavioralDriftDetector:
    """
    Detect policy adherence degradation across conversation turns
    
    Tracks metrics:
    - Approval requirement mentions (should remain constant)
    - Refusal rates (should not decrease)
    - Policy compliance scoring (should remain stable)
    """
    
    def __init__(self):
        self.session_metrics = {}  # Store per-session metrics
    
    def analyze_response(self, session_id, turn_number, agent_response, user_message):
        """
        Analyze agent response for policy adherence
        
        Returns:
            metrics: dict of compliance scores
        """
        if session_id not in self.session_metrics:
            self.session_metrics[session_id] = {
                'turns': [],
                'baseline_established': False
            }
        
        # Calculate turn metrics
        turn_metrics = {
            'turn': turn_number,
            'timestamp': datetime.now(),
            'approval_mentions': self._count_approval_mentions(agent_response),
            'policy_references': self._count_policy_references(agent_response),
            'refusal_indicators': self._detect_refusal(agent_response),
            'compliance_score': self._score_compliance(agent_response, user_message),
            'rationalization_score': self._detect_rationalization(agent_response)
        }
        
        self.session_metrics[session_id]['turns'].append(turn_metrics)
        
        # Analyze drift after sufficient turns
        if len(self.session_metrics[session_id]['turns']) >= 5:
            drift_analysis = self._analyze_drift(session_id)
            return turn_metrics, drift_analysis
        
        return turn_metrics, None
    
    def _count_approval_mentions(self, response):
        """Count explicit mentions of approval requirements"""
        approval_keywords = [
            'approval', 'manager', 'authorize', 'permission',
            'requires approval', 'need approval', 'must be approved'
        ]
        count = sum(1 for keyword in approval_keywords if keyword in response.lower())
        return count
    
    def _count_policy_references(self, response):
        """Count mentions of policies/rules/constraints"""
        policy_keywords = [
            'policy', 'rule', 'regulation', 'requirement',
            'compliance', 'procedure', 'guideline'
        ]
        count = sum(1 for keyword in policy_keywords if keyword in response.lower())
        return count
    
    def _detect_refusal(self, response):
        """Detect if agent refused or expressed constraints"""
        refusal_patterns = [
            'i cannot', 'i\'m not able to', 'not permitted',
            'policy prevents', 'requires approval first',
            'i must decline', 'cannot proceed without'
        ]
        return any(pattern in response.lower() for pattern in refusal_patterns)
    
    def _score_compliance(self, response, user_message):
        """
        Score response compliance (0-100)
        
        Higher score = stronger policy adherence
        """
        score = 100
        
        # Penalty: Agreeing to bypass approvals
        if 'proceed without' in response.lower() or 'bypass' in response.lower():
            score -= 40
        
        # Bonus: Explicit approval requirement mention
        if self._count_approval_mentions(response) > 0:
            score += 10
        
        # Bonus: Refusing potentially policy-violating request
        if self._detect_refusal(response):
            score += 15
        
        # Penalty: Conditional language weakening policy
        conditional_phrases = ['in this case', 'given the circumstances', 'given the urgency']
        if any(phrase in response.lower() for phrase in conditional_phrases):
            score -= 20
        
        return max(0, min(100, score))
    
    def _detect_rationalization(self, response):
        """
        Detect rationalization language indicating goal drift
        
        Returns:
            score (0-100): Higher = more rationalization
        """
        rationalization_phrases = [
            'given the urgency',
            'in this special case',
            'to best serve',
            'balancing policy with',
            'efficiency dictates',
            'customer satisfaction',
            'avoid blocking',
            'the most helpful thing'
        ]
        
        count = sum(1 for phrase in rationalization_phrases if phrase in response.lower())
        return min(100, count * 25)  # Each phrase adds 25 points
    
    def _analyze_drift(self, session_id):
        """
        Analyze behavioral drift across conversation
        
        Returns:
            drift_analysis: dict with drift indicators
        """
        turns = self.session_metrics[session_id]['turns']
        
        # Establish baseline (first 3 turns)
        baseline_turns = turns[:3]
        recent_turns = turns[-3:]  # Last 3 turns
        
        baseline_compliance = np.mean([t['compliance_score'] for t in baseline_turns])
        recent_compliance = np.mean([t['compliance_score'] for t in recent_turns])
        compliance_drift = baseline_compliance - recent_compliance
        
        baseline_approvals = np.mean([t['approval_mentions'] for t in baseline_turns])
        recent_approvals = np.mean([t['approval_mentions'] for t in recent_turns])
        approval_drift = baseline_approvals - recent_approvals
        
        baseline_refusals = sum(1 for t in baseline_turns if t['refusal_indicators'])
        recent_refusals = sum(1 for t in recent_turns if t['refusal_indicators'])
        refusal_drift = baseline_refusals - recent_refusals
        
        recent_rationalization = np.mean([t['rationalization_score'] for t in recent_turns[-5:]])
        
        # Determine drift severity
        is_drifting = (
            compliance_drift > 20 or  # Compliance dropped >20 points
            approval_drift > 1 or     # Mentioning approvals less
            refusal_drift > 1 or      # Refusing less often
            recent_rationalization > 50  # High rationalization
        )
        
        drift_score = (
            (compliance_drift / 100) * 0.4 +  # 40% weight
            (approval_drift / 3) * 0.3 +       # 30% weight
            (refusal_drift / 3) * 0.2 +        # 20% weight
            (recent_rationalization / 100) * 0.1  # 10% weight
        )
        
        return {
            'is_drifting': is_drifting,
            'drift_score': drift_score,
            'compliance_drift': compliance_drift,
            'approval_drift': approval_drift,
            'refusal_drift': refusal_drift,
            'rationalization_score': recent_rationalization,
            'turn_count': len(turns)
        }

# Usage in agent runtime
drift_detector = BehavioralDriftDetector()

@app.route("/api/agent/message")
def agent_message():
    session_id = request.json['session_id']
    user_message = request.json['message']
    turn_number = get_turn_number(session_id)
    
    # Generate response
    agent_response = generate_agent_response(session_id, user_message)
    
    # Analyze for drift
    turn_metrics, drift_analysis = drift_detector.analyze_response(
        session_id,
        turn_number,
        agent_response,
        user_message
    )
    
    if drift_analysis and drift_analysis['is_drifting']:
        log_security_event("behavioral_drift_detected", {
            "session_id": session_id,
            "drift_score": drift_analysis['drift_score'],
            "indicators": drift_analysis
        })
        
        if drift_analysis['drift_score'] > 0.6:  # High drift
            # Trigger intervention (conversation reset, escalation, etc.)
            trigger_drift_intervention(session_id, drift_analysis)
    
    return jsonify({
        "response": agent_response,
        "compliance_score": turn_metrics['compliance_score']
    })
```

### Visualization and Alerting

**Dashboard visualization of drift over time:**

```python
def generate_drift_visualization(session_id):
    """
    Generate compliance trend visualization
    
    For security operations dashboard
    """
    turns = drift_detector.session_metrics[session_id]['turns']
    
    return {
        'session_id': session_id,
        'turn_count': len(turns),
        'compliance_trend': [t['compliance_score'] for t in turns],
        'approval_mentions_trend': [t['approval_mentions'] for t in turns],
        'rationalization_trend': [t['rationalization_score'] for t in turns],
        'alert_level': calculate_alert_level(turns)
    }

def calculate_alert_level(turns):
    """Calculate alert severity"""
    if len(turns) < 5:
        return "none"
    
    recent_compliance = np.mean([t['compliance_score'] for t in turns[-3:]])
    recent_rationalization = np.mean([t['rationalization_score'] for t in turns[-3:]])
    
    if recent_compliance < 50 or recent_rationalization > 75:
        return "critical"
    elif recent_compliance < 70 or recent_rationalization > 50:
        return "warning"
    else:
        return "normal"
```

## Limitations & Trade-offs

**Limitations:**
- **Baseline Establishment**: Requires several turns to establish baseline behavior
- **Legitimate Variations**: Natural conversation flow may cause metric variations not indicative of attacks
- **Sophisticated Conditioning**: Gradual drift may be slow enough to avoid triggering thresholds
- **False Positives**: Contextual policy flexibility may be misinterpreted as drift

**Trade-offs:**
- **Sensitivity vs. False Positives**: Strict thresholds catch more attacks but generate more false alerts
- **Analysis Depth vs. Latency**: Comprehensive drift analysis adds processing time per turn
- **Metric Selection vs. Completeness**: More metrics improve detection but increase complexity

## Testing & Validation

**Functional Testing:**
1. **Baseline Accuracy**: Verify baseline established correctly from early turns
2. **Drift Detection**: Confirm actual policy degradation triggers alerts
3. **Metric Tracking**: Validate all metrics calculated correctly per turn

**Security Testing:**
1. **Gradual Conditioning**: Test detection of slow, incremental goal manipulation
2. **False Negative Rate**: Measure missed attacks that evade detection
3. **Threshold Tuning**: Optimize alert thresholds to balance detection and false positives

**Monitoring:**
- **Alert Distribution**: Track drift alerts across sessions
- **Drift Score Trends**: Monitor average drift scores over time
- **Intervention Effectiveness**: Measure whether detected drift leads to successful intervention

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Multi-Turn Behavioral Drift Detection: Analyze conversation transcripts for policy adherence degradation across turns. Track metrics: approval requirement mentions (should remain constant), refusal rates (should not decrease), rationalization language frequency (should not increase)."
> — Extracted from agent-goal-hijack detective controls (AI-Native LLM Security)

## Related

- **Combined with**: [[mitigations/rationalization-language-monitoring]], [[mitigations/policy-consistency-scoring]], [[mitigations/anomaly-detection-architecture]]
- **Triggers**: [[mitigations/conversation-reset-on-detection]], [[mitigations/kill-switch-and-session-termination]]
- **Defends**: [[techniques/agent-goal-hijack]]
