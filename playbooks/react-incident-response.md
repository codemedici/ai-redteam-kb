---
title: "ReAct for Incident Response"
tags:
  - type/playbook
  - target/generative-ai
  - source/generative-ai-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# ReAct for Incident Response

## Overview

ReAct (Reasoning and Acting) is a framework combining Chain of Thought (CoT) prompting with tool-based actions, enabling LLMs to alternate between reasoning and acting. The approach achieves more reliable, fact-based results by utilizing both internal knowledge and external information obtained during reasoning, enhancing interpretability and trustworthiness.

When applied to incident response, ReAct enables systematic investigation of security incidents (API key abuse, IAM anomalies, data breaches) through structured thought-action-observation loops, integrating live security tool outputs with LLM reasoning for evidence-backed recommendations.

## Scope & Prerequisites

**In Scope:**
- API security incidents (key abuse, unusual usage patterns)
- IAM anomalies (unauthorized access, privilege escalation)
- Real-time threat assessment with multi-source data correlation
- Automated security recommendation generation
- Incident triage and prioritization

**Prerequisites:**
- LLM with function calling capabilities (GPT-4, Claude, or equivalent)
- Access to security tools: API logs, threat databases, IP reputation services, user behavior analytics
- Pre-defined reasoning traces for common incident types
- Incident response playbook templates

**Out of Scope:**
- Full automated remediation (ReAct provides analysis and recommendations, not execution)
- Physical security incidents
- Source code vulnerability analysis
- Legal/compliance incident handling

## Methodology

### Phase 1: Define the Task

Clearly articulate the incident response objective.

**Example Task:** "Identify, assess, and mitigate API key abuse in real-time."

### Phase 2: Create Reasoning Traces

Lay down logical steps the framework should follow:
1. Identify abnormal API call patterns
2. Pinpoint the source (user account, IP address)
3. Assess data that might have been compromised
4. Recommend immediate security measures

### Phase 3: Incorporate External Information

Integrate real-time access to security data sources:
- API logs
- Transaction history
- Data usage metrics
- Machine learning-based anomaly detection tools

### Phase 4: Implement Acting Phase

Execute decided actions (queries, tool invocations):
- Invalidate abused API keys
- Block originating IP addresses
- Implement rate-limiting rules

### Phase 5: Integrate with Chain of Thought

Contextualize external data:
- Correlate abnormal API usage with known threat indicators
- Assess potential scale/impact based on historical data

### Phase 6: Test and Optimize

Rigorously test in controlled, simulated environment:
- Metrics: Detection accuracy, false positives, mitigation speed
- Optimize based on test results before production deployment

## Techniques Covered

| Technique | Relevance | ReAct Integration |
|-----------|-----------|-------------------|
| [[techniques/api-key-abuse\|API Key Abuse]] | Primary incident type | Multi-source correlation (logs, IP reputation, usage patterns) |
| Chain of Thought reasoning | Core to ReAct | Explicit reasoning traces before actions |
| External tool invocation | Core to ReAct | Search API logs, lookup threat databases, execute security actions |
| Multi-step reasoning | Essential for complex incidents | Thought → Action → Observation loop |

## Tooling

**Required:**
- **LLM with function calling:** GPT-4, Claude, or equivalent
- **Security Tools:**
  - API log aggregation (Splunk, Elastic, custom SIEM)
  - Threat intelligence databases (internal + external)
  - IP reputation services (AbuseIPDB, VirusTotal)
  - User behavior analytics platforms

**Implementation Framework:**
```python
# Pseudo-code for ReAct incident response
def react_incident_response(question):
    reasoning_chain = []
    
    while not resolved:
        # Thought phase
        thought = llm.generate_thought(question, reasoning_chain)
        reasoning_chain.append(thought)
        
        # Action phase
        action = determine_action(thought)
        reasoning_chain.append(action)
        
        # Observation phase
        observation = execute_action(action)
        reasoning_chain.append(observation)
        
        # Check if incident is resolved
        if is_resolved(observation):
            resolved = True
    
    return generate_final_recommendations(reasoning_chain)
```

## Deliverables

**Per Incident:**

1. **Reasoning Trace:**
   Complete thought-action-observation chain showing investigative process

2. **Evidence Package:**
   - API logs
   - Threat database lookups
   - IP reputation results
   - User activity analysis

3. **Threat Assessment:**
   - Affected accounts/resources
   - Threat level classification
   - Potential data exposure scope

4. **Actionable Recommendations:**
   - Immediate actions (suspend accounts, block IPs)
   - Short-term fixes (MFA enforcement, rate limiting)
   - Long-term improvements (architecture changes, monitoring enhancements)

**Example ReAct Output (API Key Abuse):**

```
Question: What is the nature of excessive API calls from IP 203.0.113.42?

Thought 1: Analyze API logs for this IP address
Action 1: Search[API logs for IP 203.0.113.42]
Observation 1: High number of data retrieval requests using API key with normally limited activity

Thought 2: Verify if this API key has previous security incidents
Action 2: Lookup[API key in threat database]
Observation 2: API key flagged once before for suspicious activities but later cleared

Thought 3: Assess scale of potential data exposure
Action 3: Search[Data accessed using suspicious API key]
Observation 3: Confidential user data accessed, including financial records

Thought 4: This is likely API key abuse with substantial risk. Immediate action needed.
Action 4: Execute[Invalidate API key; Block IP 203.0.113.42; Apply stricter rate-limiting]

Final Recommendation: Suspend affected API key, block source IP, conduct audit of accessed data, notify affected users if PII was compromised.
```

## Sources

> "The ReAct framework has shown significant potential in achieving more reliable and factual results, surpassing several state-of-the-art baselines in language understanding and decision-making tasks. Its fusion with the Chain of Thought (CoT) method enables the utilization of both internal knowledge and external information obtained during reasoning, thereby enhancing human interpretability and trustworthiness."
> 
> — [[sources/bibliography#Generative AI Security]], p. 292 (§9.2.9)

> "The proposed ReAct framework not only identifies the threat but also dives deep into the issue, using logical reasoning steps to probe the scale and severity of the incident. It consults external data sources for contextual understanding and acts decisively based on the analysis."
> 
> — [[sources/bibliography#Generative AI Security]], p. 293
