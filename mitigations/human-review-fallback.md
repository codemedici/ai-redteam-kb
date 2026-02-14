---
title: "Human Review Fallback"
tags:
  - type/mitigation
  - target/ml-model
  - target/llm
  - target/agent
  - target/cv
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Human Review Fallback

## Summary

Human review fallback routes low-confidence or anomalous predictions to human analysts for final decision, providing a critical safety layer when automated defenses are uncertain or when the cost of misprediction is high. This mitigation acknowledges that no AI system achieves perfect adversarial robustness—when the model or defensive systems detect potential attacks or uncertainty, deferring to human judgment prevents damage while maintaining operational continuity. Human review is particularly valuable for adversarial scenarios where automated systems may be fooled but human perception remains reliable (e.g., adversarial examples that are obvious to humans but fool models).

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0015 | [[techniques/adversarial-robustness]] | Routes predictions flagged by anomaly detection or confidence thresholds to human review, preventing damage from successful evasion attacks |
| AML.T0043 | [[techniques/adversarial-examples-evasion-attacks]] | Human analysts can identify adversarial perturbations that fool automated systems |
| | [[techniques/backdoor-poisoning]] | Manual review of suspicious predictions can catch backdoor activations |
| | [[techniques/prompt-injection]] | Human review validates responses flagged as potential injection attempts |
| AML.T0054 | [[techniques/jailbreak-policy-bypass]] | Routes outputs flagged by safety monitors to human moderators for policy compliance validation |

## Implementation

### Triggering Conditions

**Route to human review when:**

```python
def should_route_to_human_review(prediction, confidence, anomaly_score, context):
    """
    Determine if prediction should be escalated to human review
    
    Returns: (should_route: bool, reason: str)
    """
    # Condition 1: Low confidence prediction
    if confidence < CONFIDENCE_THRESHOLD:  # e.g., 0.7
        return True, f"Low confidence: {confidence:.2f}"
    
    # Condition 2: Anomaly detection triggered
    if anomaly_score > ANOMALY_THRESHOLD:  # e.g., 0.8
        return True, f"Anomaly detected: score {anomaly_score:.2f}"
    
    # Condition 3: High-stakes decision
    if context.get('decision_impact') == 'critical':
        return True, "Critical decision context"
    
    # Condition 4: Ensemble disagreement
    if context.get('ensemble_disagreement', 0) > 0.3:
        return True, f"Ensemble disagreement: {context['ensemble_disagreement']:.2f}"
    
    # Condition 5: Sensitive prediction category
    SENSITIVE_CATEGORIES = ['medical_diagnosis', 'legal_decision', 'financial_approval']
    if prediction['category'] in SENSITIVE_CATEGORIES:
        return True, f"Sensitive category: {prediction['category']}"
    
    # Condition 6: Policy violation detected by output filters
    if context.get('policy_violation_detected'):
        return True, "Potential policy violation"
    
    # Condition 7: Adversarial input indicators
    if context.get('adversarial_indicators', []):
        return True, f"Adversarial indicators: {context['adversarial_indicators']}"
    
    return False, "No review needed"
```

### Human Review Queue Architecture

**Implementation pattern:**

```python
class HumanReviewQueue:
    """Manage human review queue with priority and SLA tracking"""
    
    def __init__(self):
        self.queue = PriorityQueue()
        self.pending_reviews = {}
        self.analysts = []
    
    def enqueue_for_review(self, prediction_request, reason, priority='normal'):
        """Add prediction to human review queue"""
        review_item = {
            'id': generate_review_id(),
            'timestamp': datetime.now(),
            'input': prediction_request['input'],
            'model_prediction': prediction_request['prediction'],
            'model_confidence': prediction_request['confidence'],
            'reason': reason,
            'context': prediction_request['context'],
            'priority': priority,
            'sla_deadline': self.calculate_sla(priority)
        }
        
        # Priority levels: critical (immediate), high (1h), normal (4h), low (24h)
        priority_value = {
            'critical': 0,
            'high': 1,
            'normal': 2,
            'low': 3
        }[priority]
        
        self.queue.put((priority_value, review_item))
        self.pending_reviews[review_item['id']] = review_item
        
        # Alert analysts for critical items
        if priority == 'critical':
            self.alert_on_call_analyst(review_item)
        
        return review_item['id']
    
    def assign_to_analyst(self, analyst_id):
        """Assign next item from queue to analyst"""
        if self.queue.empty():
            return None
        
        _, review_item = self.queue.get()
        review_item['assigned_to'] = analyst_id
        review_item['assigned_at'] = datetime.now()
        
        return review_item
    
    def submit_analyst_decision(self, review_id, decision, justification):
        """Record analyst's decision"""
        review_item = self.pending_reviews.get(review_id)
        if not review_item:
            raise ValueError(f"Review ID {review_id} not found")
        
        review_item['analyst_decision'] = decision
        review_item['justification'] = justification
        review_item['completed_at'] = datetime.now()
        review_item['review_duration'] = (
            review_item['completed_at'] - review_item['assigned_at']
        ).total_seconds()
        
        # Archive completed review
        archive_review(review_item)
        del self.pending_reviews[review_id]
        
        # Feed decision back to model for learning
        self.update_model_with_feedback(review_item)
        
        return decision
    
    def calculate_sla(self, priority):
        """Calculate SLA deadline based on priority"""
        sla_hours = {
            'critical': 0.25,  # 15 minutes
            'high': 1,
            'normal': 4,
            'low': 24
        }
        return datetime.now() + timedelta(hours=sla_hours[priority])
    
    def monitor_sla_violations(self):
        """Alert on pending reviews exceeding SLA"""
        now = datetime.now()
        violations = [
            item for item in self.pending_reviews.values()
            if now > item['sla_deadline'] and not item.get('analyst_decision')
        ]
        
        if violations:
            alert_management("SLA violations detected", {
                'count': len(violations),
                'items': violations
            })
        
        return violations
```

### Analyst Interface Design

**Critical UI/UX requirements:**

```python
class AnalystReviewInterface:
    """UI for human analysts reviewing flagged predictions"""
    
    def display_review_item(self, review_item):
        """
        Present review item to analyst with all relevant context
        
        Display elements:
        - Original input (with highlighting if adversarial indicators present)
        - Model prediction + confidence
        - Reason for escalation
        - Similar historical cases (if available)
        - Relevant policy guidelines
        - Decision options (approve, reject, modify, escalate)
        """
        return {
            'input_display': self.format_input(review_item['input']),
            'prediction_display': self.format_prediction(review_item['model_prediction']),
            'confidence_visualization': self.visualize_confidence(review_item['model_confidence']),
            'escalation_reason': review_item['reason'],
            'context_panel': self.render_context(review_item['context']),
            'similar_cases': self.find_similar_cases(review_item),
            'policy_reference': self.get_relevant_policies(review_item),
            'decision_buttons': ['Approve', 'Reject', 'Modify', 'Escalate'],
            'justification_field': 'text_input'
        }
    
    def format_input(self, input_data):
        """Format input for analyst review with adversarial indicator highlighting"""
        if 'adversarial_indicators' in input_data.get('metadata', {}):
            # Highlight suspicious patterns
            return self.highlight_suspicious_patterns(input_data)
        return input_data
    
    def find_similar_cases(self, review_item):
        """Retrieve similar historical cases to aid analyst decision"""
        # Use embedding similarity to find past reviews
        similar_cases = query_review_archive(
            embedding=embed(review_item['input']),
            limit=5
        )
        return similar_cases
```

### Integration with User Experience

**Customer-facing fallback patterns:**

```python
def handle_prediction_with_fallback(user_request):
    """
    Handle prediction request with human review fallback
    """
    # Attempt model prediction
    prediction = model.predict(user_request['input'])
    confidence = model.get_confidence(user_request['input'])
    
    # Check anomaly detection
    anomaly_score = anomaly_detector.score(user_request['input'])
    
    # Determine if human review needed
    needs_review, reason = should_route_to_human_review(
        prediction, confidence, anomaly_score, user_request['context']
    )
    
    if needs_review:
        # Route to human review
        review_id = human_review_queue.enqueue_for_review(
            {
                'input': user_request['input'],
                'prediction': prediction,
                'confidence': confidence,
                'context': user_request['context']
            },
            reason=reason,
            priority=determine_priority(user_request)
        )
        
        # User experience options:
        # Option 1: Synchronous wait (if low SLA)
        if user_request.get('wait_for_review'):
            decision = wait_for_analyst_decision(review_id, timeout=300)  # 5 min max
            return decision if decision else fallback_safe_response()
        
        # Option 2: Asynchronous notification
        else:
            return {
                'status': 'pending_review',
                'review_id': review_id,
                'estimated_wait': estimate_review_time(reason),
                'message': 'Your request is being reviewed by our team. You will be notified when complete.'
            }
    
    # No review needed, return prediction
    return {
        'status': 'completed',
        'prediction': prediction,
        'confidence': confidence
    }

def fallback_safe_response():
    """Return safe default response when human review times out"""
    return {
        'status': 'review_timeout',
        'action': 'deny',  # Conservative default
        'message': 'Unable to complete your request at this time. Please contact support.'
    }
```

### Feedback Loop for Model Improvement

**Use human decisions to improve model:**

```python
def update_model_with_feedback(review_item):
    """
    Incorporate analyst decisions into model training
    """
    # Create training example from review
    training_example = {
        'input': review_item['input'],
        'model_prediction': review_item['model_prediction'],
        'human_decision': review_item['analyst_decision'],
        'confidence': review_item['model_confidence'],
        'timestamp': review_item['completed_at']
    }
    
    # Cases where human decision differs from model
    if training_example['model_prediction'] != training_example['human_decision']:
        # Add to adversarial training dataset
        if 'adversarial' in review_item['reason'].lower():
            add_to_adversarial_training_set(training_example)
        
        # Add to general correction dataset
        add_to_correction_training_set(training_example)
    
    # Periodically retrain model on human-corrected examples
    if should_trigger_retraining():
        schedule_model_retraining()
```

## Limitations & Trade-offs

**Limitations:**
- **Scalability:** Human review doesn't scale to high-volume production systems
- **Latency:** Introduces delay in prediction delivery (minutes to hours)
- **Cost:** Human analyst time is expensive
- **Consistency:** Human decisions may vary across analysts or over time
- **Availability:** Requires 24/7 on-call coverage for critical systems

**Trade-offs:**
- **Throughput vs. Safety:** Routing predictions to humans reduces throughput but increases safety
- **Automation vs. Accuracy:** More aggressive fallback thresholds improve accuracy but reduce automation benefits
- **Cost vs. Risk:** Must balance analyst cost against risk of misprediction
- **User Experience vs. Security:** Delays may frustrate users but prevent harmful outputs

**When NOT to use:**
- Real-time systems requiring sub-second responses (autonomous vehicles, HFT)
- High-volume, low-stakes predictions (product recommendations, spam filtering)
- Systems where human judgment is no better than model (specialized technical domains)

## Testing & Validation

**Functional Testing:**
1. **Queue functionality:** Verify items enqueued, assigned, and completed correctly
2. **SLA tracking:** Confirm deadline calculation and violation alerts
3. **Priority handling:** Validate critical items escalated appropriately
4. **Feedback loop:** Test that analyst decisions feed back to training pipeline

**Security Testing:**
1. **False negative rate:** Measure how many adversarial examples reach human review
2. **Analyst effectiveness:** Validate that humans correctly identify adversarial inputs
3. **Queue poisoning:** Attempt to overwhelm queue with low-priority items to delay critical reviews
4. **Bypass attempts:** Test if attackers can avoid triggering human review conditions

**Performance Testing:**
1. **Queue capacity:** Stress test with high review volumes
2. **SLA compliance:** Measure actual review times vs. SLA targets
3. **Analyst workload:** Ensure sustainable workload per analyst

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Fallback to Human Review: Route low-confidence or anomalous inputs to human analysts for final decision."
> — [[techniques/adversarial-robustness]] (extracted from responsive controls)

## Related

- **Combines with:** [[mitigations/anomaly-detection-architecture]], [[mitigations/output-monitoring-validation]], [[mitigations/ensemble-methods]]
- **Enables:** Safe operation under uncertainty, continuous learning from human expertise
- **Triggered by:** [[mitigations/anomaly-detection-architecture]], confidence thresholds, policy violation detection
