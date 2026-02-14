---
title: "Policy Consistency Scoring"
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

Policy consistency scoring is a detective control that automatically evaluates agent responses against compliance rubrics at each conversational turn, measuring adherence to organizational policies. This mitigation provides quantitative tracking of policy compliance over time, enabling detection of gradual degradation that may indicate goal hijacking. Unlike qualitative manual review, automated scoring allows real-time monitoring at scale, alerting when consistency scores decline across conversation turns.


## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/agent-goal-hijack]] | Detects declining policy adherence scores indicating successful goal manipulation |
| | [[techniques/agent-authorization-hijacking]] | Identifies authorization policy violations through scoring |


## Implementation

```python
class PolicyConsistencyScorer:
    """Automated policy compliance scoring for agent responses"""
    
    def __init__(self, policy_rubric):
        """
        Args:
            policy_rubric: Dict defining scoring criteria
        """
        self.rubric = policy_rubric
        self.session_scores = {}
    
    def score_response(self, session_id, turn, agent_response, context):
        """
        Score response against policy rubric
        
        Returns:
            score: float (0-100)
        """
        score = 100  # Start at perfect compliance
        violations = []
        
        # Check each policy criterion
        for criterion_name, criterion_def in self.rubric.items():
            violation, penalty = self._check_criterion(
                agent_response,
                context,
                criterion_def
            )
            
            if violation:
                score -= penalty
                violations.append({
                    'criterion': criterion_name,
                    'penalty': penalty,
                    'description': violation
                })
        
        # Store score history
        if session_id not in self.session_scores:
            self.session_scores[session_id] = []
        
        self.session_scores[session_id].append({
            'turn': turn,
            'score': max(0, score),
            'violations': violations
        })
        
        return max(0, score), violations
    
    def _check_criterion(self, response, context, criterion):
        """Check if response violates specific policy criterion"""
        response_lower = response.lower()
        
        # Required patterns (must be present)
        if 'required_patterns' in criterion:
            for pattern in criterion['required_patterns']:
                if pattern not in response_lower:
                    return f"Missing required pattern: {pattern}", criterion['penalty']
        
        # Forbidden patterns (must not be present)
        if 'forbidden_patterns' in criterion:
            for pattern in criterion['forbidden_patterns']:
                if pattern in response_lower:
                    return f"Contains forbidden pattern: {pattern}", criterion['penalty']
        
        # Custom validation function
        if 'validator' in criterion:
            is_valid, message = criterion['validator'](response, context)
            if not is_valid:
                return message, criterion['penalty']
        
        return None, 0  # No violation

# Example rubric for financial services agent
financial_policy_rubric = {
    'approval_requirement': {
        'description': 'Must mention approval for credit limit changes',
        'required_patterns': ['approval', 'manager'],
        'penalty': 30,
        'validator': lambda resp, ctx: (
            ('credit' not in resp.lower() or 'approval' in resp.lower()),
            "Credit changes mentioned without approval requirement"
        )
    },
    'no_bypass_language': {
        'description': 'Must not suggest bypassing procedures',
        'forbidden_patterns': ['bypass', 'skip', 'workaround', 'shortcut'],
        'penalty': 40
    },
    'documentation_requirement': {
        'description': 'Must mention documentation for changes',
        'required_patterns': ['document', 'record', 'log'],
        'penalty': 20
    }
}

scorer = PolicyConsistencyScorer(financial_policy_rubric)

@app.after_agent_response
def score_compliance(session_id, turn, agent_response, context):
    """Score each response for policy compliance"""
    score, violations = scorer.score_response(session_id, turn, agent_response, context)
    
    # Alert on declining scores
    if len(scorer.session_scores[session_id]) >= 3:
        recent_scores = [s['score'] for s in scorer.session_scores[session_id][-3:]]
        avg_recent = np.mean(recent_scores)
        
        if avg_recent < 70:  # Threshold
            alert_security("declining_policy_compliance", {
                "session_id": session_id,
                "average_score": avg_recent,
                "recent_violations": violations
            })
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

> "Policy Consistency Scoring: Automated scoring of agent responses against compliance rubrics at each turn. Declining scores across conversation indicate manipulation."
> — Extracted from agent-goal-hijack detective controls (AI-Native LLM Security)
