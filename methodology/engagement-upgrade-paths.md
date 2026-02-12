---
id: engagement-upgrade-paths
title: "Engagement Upgrade Paths & Follow-On Triggers"
sidebar_label: "Upgrade Paths"
sidebar_position: 3
description: Ethical guidance for recommending follow-on engagements based on findings, client needs, and value delivery.
---

# Engagement Upgrade Paths & Follow-On Triggers

## Purpose

This document provides guidance on **when and how to recommend follow-on engagements** based on findings from initial assessments, client requests, or discovered gaps. The goal is to serve client needs genuinely and ethically, not to manufacture upsells.

**Core Principle**: Only recommend additional work if it provides clear, measurable value to the client's security posture. Respect client autonomy and budget constraints.

---

## Ethical Framework for Recommendations

### Do Recommend When:
- **Findings reveal concentrated risk** in a specific area requiring deeper investigation
- **Client explicitly requests** ongoing support or specialized testing
- **Remediation validation is necessary** to confirm critical/high findings are resolved
- **New capabilities or changes** introduce additional attack surface after initial engagement
- **Compliance requirements** demand additional evidence or testing depth

### Do NOT Recommend When:
- Findings are low-severity and remediation is straightforward (client can self-remediate)
- Client has limited budget and must prioritize fixing existing issues
- Initial engagement already covered the area thoroughly
- Recommendation serves revenue goals but not genuine client security improvement
- Client has not yet implemented remediations from the initial engagement

### Communication Principles:
1. **Transparency**: Clearly state why you're recommending additional work
2. **Client Autonomy**: Frame as an option, not a requirement
3. **No Manufactured Urgency**: Avoid fear-based language or artificial deadlines
4. **Honest Scope**: Don't oversell impact; be realistic about what additional work will achieve
5. **Allow Decline**: Make it easy for clients to say "not right now" without pressure

---

## Follow-On Engagement Catalog

Reference these add-ons when recommending follow-on work (see Pricing & Packaging Guide for costs):

1. **Retest Engagement** (post-remediation validation)
2. **Extended Agent Security Deep Dive** (multi-agent systems, complex tool chains)
3. **RAG Security Review** (dedicated data/knowledge boundary testing)
4. **Purple Team Workshop** (collaborative defense building)
5. **Continuous Monitoring Setup** (ongoing detection and alerting)
6. **Governance & Compliance Deep Dive** (NIST AI RMF, EU AI Act, SOC 2)
7. **Training Pipeline Security Assessment** (ML platform, fine-tuning, data provenance)

---

## Upgrade Path Matrix

### Path 1: Critical Agent Security Findings → Extended Agent Deep Dive

**Trigger Conditions:**
- ATER engagement found **3+ Critical/High severity issues** in Application/Agent Runtime boundary
- Tool privilege escalation exploits demonstrated
- Multi-step agent workflows showing goal hijacking vulnerabilities
- Client uses complex tool chains (10+ tools) or multi-agent orchestration
- Findings indicate deeper architectural issues requiring specialized expertise

**Recommended Follow-On:**
**Extended Agent Security Deep Dive** (+5 days)

**Business Justification:**
"The initial engagement identified significant vulnerabilities in your agent's tool invocation and workflow logic. Given the complexity of your multi-agent system and the critical nature of these findings, a dedicated deep dive would allow us to:
- Test all 15+ tool interactions comprehensively (initial engagement sampled 5)
- Analyze multi-agent coordination security
- Validate agent memory persistence and state management
- Develop agent-specific secure design patterns for your architecture

This investment reduces the risk of tool-based data breaches or unauthorized actions that could impact your production deployment."

**Communication Template:**
```
Subject: Follow-Up Recommendation: Extended Agent Security Testing

Hi [Client],

Based on the findings from the AI Threat Exposure Review, I wanted to flag an opportunity to strengthen your agent security posture further.

We identified [X] critical tool privilege escalation vulnerabilities and [Y] goal hijacking scenarios during initial testing. Given your system's complexity (15+ tools, multi-agent workflows), we tested a representative sample, but there's additional risk surface we didn't fully explore.

I'd recommend an **Extended Agent Security Deep Dive** (additional 5 days) to:
- Test all tool interactions comprehensively
- Analyze multi-agent coordination security
- Develop agent-specific mitigations tailored to your architecture

This is optional, but given the severity of the initial findings and the business-critical nature of your agent workflows, it would provide significant risk reduction before production launch.

Happy to discuss whether this makes sense for your timeline and budget. No pressure—if you'd prefer to focus on remediating the existing findings first, that's completely reasonable.

Let me know your thoughts.

Best,
[Your Name]
```

---

### Path 2: RAG Poisoning or Retrieval Manipulation Found → RAG Security Review

**Trigger Conditions:**
- ATER engagement found exploitable RAG poisoning or retrieval hijacking
- Client's system heavily relies on RAG for business-critical decisions
- Knowledge base contains sensitive or proprietary information
- Multi-tenant RAG architecture with isolation concerns
- Client plans to expand knowledge corpus significantly post-launch

**Recommended Follow-On:**
**RAG Security Review** (+4 days)

**Business Justification:**
"Your RAG architecture is central to the application's value proposition. The initial engagement demonstrated that malicious document injection can corrupt outputs, but we didn't exhaustively test embedding integrity, cross-tenant isolation, or advanced retrieval manipulation techniques.

A dedicated RAG Security Review would:
- Test all embedding poisoning vectors
- Validate tenant isolation across the vector database
- Analyze semantic hijacking and ranking exploitation
- Provide secure RAG design patterns specific to your knowledge sources

This is particularly valuable given your plan to onboard 50+ new customers, each with isolated knowledge bases."

**Communication Template:**
```
Subject: Optional Add-On: Dedicated RAG Security Testing

Hi [Client],

During the AI Threat Exposure Review, we successfully demonstrated RAG poisoning by injecting malicious documents into your knowledge base. Given how central RAG is to your application, I wanted to suggest a **RAG Security Review** (additional 4 days) to go deeper.

This would allow us to:
- Exhaustively test embedding integrity and poisoning resistance
- Validate cross-tenant isolation (critical for your multi-customer deployment)
- Analyze retrieval ranking manipulation and semantic hijacking techniques
- Provide tailored secure design patterns for your vector database and indexing pipeline

This is absolutely optional. If your priority is fixing the identified findings first and monitoring RAG behavior post-launch, that's a completely valid approach. But if you want higher confidence before onboarding 50+ customers, this investment would be worthwhile.

Let me know if you'd like to discuss scope and timing.

Best,
[Your Name]
```

---

### Path 3: Remediation Complete → Retest Engagement

**Trigger Conditions:**
- Client has remediated **Critical or High severity findings** from initial engagement
- Client requires verification for compliance, audit, or customer assurance
- Fixes involve significant architectural changes (not just config tweaks)
- Client wants formal validation letter for stakeholders
- 2-4 weeks have passed since initial engagement report delivery

**Recommended Follow-On:**
**Retest Engagement** (30-40% of original engagement cost)

**Business Justification:**
"You've invested significant engineering effort into remediating the critical prompt injection and tool privilege escalation findings. A retest engagement provides:
- Independent verification that fixes are effective
- Identification of any residual risk or incomplete mitigations
- Formal validation letter for customers, auditors, or board
- Confidence that remediation didn't introduce new vulnerabilities

This is especially valuable if you're using security validation as part of customer assurance or regulatory compliance."

**Communication Template:**
```
Subject: Retest Engagement to Validate Remediation

Hi [Client],

Congratulations on completing remediation of the critical findings from the AI Threat Exposure Review. I wanted to offer a **Retest Engagement** to formally validate that the fixes are effective and provide independent verification.

This would involve:
- Re-testing all Critical and High severity findings
- Confirming mitigations are properly implemented
- Identifying any residual risk or edge cases
- Delivering a formal validation letter (useful for customer security questionnaires or compliance audits)

Retest engagements are typically [cost] (30-40% of the original engagement) and take [X] days.

This is optional—if you're confident in your internal validation and don't need external verification for stakeholders, that's perfectly reasonable. Let me know if formal validation would be valuable for your use case.

Best,
[Your Name]
```

---

### Path 4: Client Requests Ongoing Support → Continuous Monitoring Setup

**Trigger Conditions:**
- Client asks: "How do we detect these attacks in production?"
- ATER findings include **Deployment/Governance gaps** (insufficient telemetry, weak alerting)
- Client is launching to production and wants ongoing detection capability
- Client security team lacks AI-specific SIEM rules or monitoring dashboards
- Client explicitly requests help with operationalizing security

**Recommended Follow-On:**
**Continuous Monitoring Setup** (+3 days)

**Business Justification:**
"The engagement identified gaps in your ability to detect prompt injection, tool abuse, and data exfiltration in production. Continuous Monitoring Setup helps you operationalize the findings by:
- Designing AI-specific monitoring dashboards (prompt anomaly detection, tool invocation patterns)
- Configuring alerting rules for your existing SIEM/SOAR platform
- Creating runbooks for responding to AI security incidents
- Training your SOC team on AI-specific attack patterns

This turns the security assessment into sustained capability, rather than a point-in-time snapshot."

**Communication Template:**
```
Subject: Operationalizing AI Security: Continuous Monitoring Setup

Hi [Client],

One of the findings from the AI Threat Exposure Review was that your current telemetry wouldn't detect the prompt injection or tool abuse attacks we demonstrated. Now that you're launching to production, you might benefit from **Continuous Monitoring Setup** (additional 3 days).

This would involve:
- Designing AI-specific monitoring dashboards (integrated with your existing SIEM)
- Configuring alerting rules for prompt anomalies, tool misuse, and data exfiltration patterns
- Creating incident response runbooks for your SOC team
- Training your security team on AI-specific attack indicators

This is optional, but it would help you sustain the security improvements from this engagement and detect threats in production going forward.

Let me know if this aligns with your operational security goals.

Best,
[Your Name]
```

---

### Path 5: Compliance or Regulatory Requirement → Governance & Compliance Deep Dive

**Trigger Conditions:**
- Client operates in regulated industry (healthcare, finance, government)
- Client is preparing for SOC 2 Type II audit and needs AI-specific controls validated
- Client subject to **EU AI Act** (high-risk AI system classification)
- ATER findings flagged governance gaps but didn't deeply assess compliance alignment
- Client explicitly asks: "Does this satisfy NIST AI RMF requirements?"

**Recommended Follow-On:**
**Governance & Compliance Deep Dive** (+4 days)

**Business Justification:**
"The initial engagement focused on exploitability of technical vulnerabilities. A Governance & Compliance Deep Dive provides:
- Detailed NIST AI RMF assessment with gap analysis
- EU AI Act readiness review (for high-risk AI systems)
- SOC 2 Type II control validation specific to AI components
- Audit-ready documentation mapping controls to compliance frameworks

This serves stakeholders who need evidence of due diligence for regulators, auditors, or enterprise customers."

**Communication Template:**
```
Subject: Compliance Add-On: NIST AI RMF & Regulatory Readiness

Hi [Client],

Based on our conversation about your upcoming SOC 2 audit and EU AI Act obligations, I wanted to suggest a **Governance & Compliance Deep Dive** (additional 4 days) as a follow-on to the AI Threat Exposure Review.

This would provide:
- Detailed NIST AI RMF gap analysis (GOVERN/MAP/MEASURE/MANAGE functions)
- EU AI Act readiness assessment (high-risk AI system classification and obligations)
- SOC 2 Type II control validation specific to your LLM application
- Audit-ready documentation for regulators and enterprise customer security questionnaires

This goes beyond the technical findings to provide the governance evidence your auditors and compliance team need.

Let me know if this would be valuable for your regulatory context.

Best,
[Your Name]
```

---

### Path 6: Training Pipeline or Fine-Tuning Concerns → Training Pipeline Security Assessment

**Trigger Conditions:**
- Client uses custom fine-tuned models or proprietary training pipelines
- ATER engagement found training data memorization issues
- Client concerned about data provenance, model versioning, or supply chain security
- Client plans to fine-tune on customer data (multi-tenant training)
- ML platform security not covered in initial ATER engagement (out of scope)

**Recommended Follow-On:**
**Training Pipeline Security Assessment** (+4 days)

**Business Justification:**
"The AI Threat Exposure Review focused on production model behavior, but your fine-tuning pipeline introduces additional risk surface. A Training Pipeline Security Assessment would:
- Validate data provenance and integrity controls
- Assess fine-tuning dataset security (PII, backdoors, poisoning)
- Review model versioning and supply chain security
- Test training infrastructure access controls

This is critical if you're fine-tuning on customer data or deploying custom models to regulated environments."

**Communication Template:**
```
Subject: Follow-Up Recommendation: Training Pipeline Security Assessment

Hi [Client],

During the AI Threat Exposure Review, you mentioned your team fine-tunes models on customer data. The initial engagement focused on production model behavior, but the training pipeline itself represents significant risk surface we didn't assess.

I'd recommend a **Training Pipeline Security Assessment** (additional 4 days) to:
- Validate data provenance and integrity controls
- Assess fine-tuning dataset security (PII leakage, poisoning risk)
- Review model versioning, artifact storage, and supply chain security
- Test training infrastructure access controls

This is especially important given your multi-tenant fine-tuning approach and regulatory obligations around customer data handling.

This is optional, but it would address a gap that could lead to model backdoors, data leakage, or compliance issues.

Let me know if you'd like to discuss scope.

Best,
[Your Name]
```

---

### Path 7: Client Security Team Wants Hands-On Training → Purple Team Workshop

**Trigger Conditions:**
- Client security team asks: "Can you teach us how to test for these issues?"
- Client wants to build internal capability for ongoing AI security testing
- Client wants to improve detection and response for AI-specific threats
- ATER engagement delivered findings, but client team needs practical skills
- Client has internal red team or AppSec team eager to learn

**Recommended Follow-On:**
**Purple Team Workshop** (+2 days)

**Business Justification:**
"The AI Threat Exposure Review provided findings and recommendations, but your security team wants to build internal capability to test and detect AI threats going forward. A Purple Team Workshop:
- Provides hands-on training on AI security testing techniques
- Demonstrates live attacks and detection strategies
- Develops SIEM rules for AI-specific threats together
- Conducts tabletop incident response exercise for AI security events

This empowers your team to sustain security improvements and adapt as your AI system evolves."

**Communication Template:**
```
Subject: Building Internal Capability: Purple Team Workshop

Hi [Client],

I noticed your security team was very engaged during the final readout and asked great questions about testing techniques and detection strategies. If you're interested in building internal capability, I'd recommend a **Purple Team Workshop** (additional 2 days).

This would involve:
- Hands-on training on prompt injection, RAG poisoning, and agent testing techniques
- Live demonstration of attacks and defense strategies
- Collaborative SIEM rule development for AI-specific threats
- Tabletop incident response exercise for AI security events

This empowers your team to test future AI features, validate fixes, and detect threats in production without always requiring external consultants.

This is optional, but it's a great investment if you're planning to expand AI capabilities and want to build lasting security expertise internally.

Let me know if this interests your team.

Best,
[Your Name]
```

---

## Decision Framework: When to Recommend Follow-On Work

Use this decision tree after delivering the initial engagement report:

```
1. Are there Critical/High findings in a concentrated area (e.g., agent security)?
   YES → Consider Extended Deep Dive for that area
   NO → Proceed to next question

2. Did findings reveal governance or compliance gaps relevant to client's regulatory context?
   YES → Consider Governance & Compliance Deep Dive if client is in regulated industry
   NO → Proceed to next question

3. Has client completed remediation of Critical/High findings?
   YES → Offer Retest Engagement for validation
   NO → Wait for remediation before recommending retest

4. Did client ask: "How do we detect this in production?" or "How do we sustain this?"
   YES → Recommend Continuous Monitoring Setup
   NO → Proceed to next question

5. Did client ask: "Can you train our team?" or express interest in building internal capability?
   YES → Recommend Purple Team Workshop
   NO → Proceed to next question

6. Does client use fine-tuning or custom training pipelines not covered in initial ATER?
   YES → Consider Training Pipeline Security Assessment if relevant to risk profile
   NO → Proceed to next question

7. Does client have upcoming major changes (new features, architecture updates, launch to new markets)?
   YES → Discuss future re-assessment timeline (not immediate upsell)
   NO → Thank client, offer to stay in touch for future needs
```

---

## Timing Recommendations

**Do NOT recommend follow-on work immediately** in these scenarios:
- During the final readout presentation (client needs time to digest findings)
- Before client has had a chance to review the full report
- If client is clearly overwhelmed by existing findings

**DO recommend follow-on work at these trigger points:**
- **1-2 weeks after report delivery**: Client has reviewed findings, initial questions answered
- **During remediation check-in**: Client mentions challenges or areas of concern
- **Post-remediation**: Client reports fixes complete and asks about validation
- **When client proactively asks**: "What's next?" or "Can you help with [specific area]?"

---

## Communicating Costs Transparently

When recommending follow-on work, always:
- **State the cost upfront** (even if approximate): "This would be approximately £[X]"
- **Explain what's included**: "This includes [X] days of testing, [Y] deliverables"
- **Reference the add-ons menu**: "You can see full details in the Add-Ons Menu we provided"
- **Allow time to consider**: "No rush—take a few days to discuss internally"
- **Offer alternatives**: "If budget is a constraint right now, we could also [simpler option]"

**Example:**
"The Extended Agent Deep Dive would be approximately £[X] for 5 additional days of testing, including comprehensive tool chain analysis and tailored mitigation guidance. This is optional, but given the critical findings, it's worth considering. Happy to send a formal proposal if you'd like to explore it."

---

## Ethical Red Lines - Do NOT Recommend If:

1. **Client Cannot Afford It**: If client is clearly budget-constrained, do not push additional work. Offer free resources or open-source tools instead.

2. **Findings Don't Justify It**: If initial engagement found only Low/Medium severity issues, do not manufacture urgency for deep dives.

3. **Client Hasn't Fixed Initial Findings**: Do not recommend new testing if client hasn't addressed critical findings from the first engagement (exception: retest to validate fixes).

4. **You're Uncertain of Value**: If you're not confident the additional work will provide meaningful security improvement, don't recommend it.

5. **Client Explicitly Declined**: If client said "not interested in additional work," respect that decision. Offer to revisit in 3-6 months if circumstances change.

---

## Sample "No Pressure" Closing Language

Always make it easy for clients to decline without feeling judged:

**Option 1: Soft Offer**
"This is completely optional. If your priority right now is implementing the existing recommendations, that's the right choice. We can always revisit this in a few months if needs change."

**Option 2: Acknowledging Constraints**
"I know budget and bandwidth are always limited. If this doesn't fit right now, no problem at all. The initial engagement findings give you a strong foundation to work from."

**Option 3: Client Autonomy**
"You know your priorities and constraints better than I do. I'm happy to provide details if this interests you, but equally happy to stay in touch and support you with the remediations you're already tackling."

**Option 4: Transparent Motivation**
"I'm recommending this because I genuinely believe it would reduce risk given what we found, not because I'm trying to upsell. If it doesn't make sense for your situation, that's completely fine."

---

## Post-Engagement Follow-Up Cadence

**Recommended touchpoints** (without being pushy):

1. **Week 1 Post-Report**: Send thank-you email, offer Q&A session if needed
2. **Week 3-4 Post-Report**: Check in: "How's remediation going? Any questions or blockers?"
3. **Week 8-10 Post-Report**: If client completed remediations, offer retest engagement
4. **3-6 Months Post-Engagement**: Periodic check-in: "Any new AI features or changes that might benefit from security review?"
5. **Annual Cadence**: Suggest annual re-assessment for production systems (normal security hygiene)

**Always allow opt-out**: "Let me know if you'd prefer I not follow up—no offense taken!"

---

## Summary: Guiding Principles

1. **Serve the Client**: Recommend work that genuinely improves their security posture
2. **Respect Autonomy**: Make it easy to say no without pressure or guilt
3. **Be Transparent**: Explain why you're recommending additional work
4. **No Manufactured Urgency**: Avoid fear-based selling or artificial deadlines
5. **Acknowledge Constraints**: Budget and bandwidth are real; don't ignore them
6. **Offer Alternatives**: If a full engagement is too much, suggest smaller steps
7. **Build Long-Term Relationships**: Reputation and trust matter more than any single upsell

**When in doubt, ask yourself**: "If I were the client, would this recommendation feel genuinely helpful or like a sales tactic?"

If the answer is "sales tactic," don't make the recommendation.
