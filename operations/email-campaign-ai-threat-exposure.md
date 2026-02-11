---
id: email-campaign-ai-threat-exposure
title: "Email Campaign: AI Threat Exposure Review"
sidebar_label: "Email Campaign Source"
sidebar_position: 2
description: Source material for outbound email campaigns promoting AI Threat Exposure Review engagements.
---

# Email Campaign: AI Threat Exposure Review

## Campaign Objective

This email sequence generates qualified interest in AI Threat Exposure Review engagements by:

1. **Reintroducing** AI security as a distinct discipline (not just "another pentest")
2. **Framing the problem** in terms of architectural trust boundaries, not abstract risks
3. **Offering a concrete engagement** with clear scope and deliverables
4. **Creating low-friction next steps** (discovery call, referral, opt-out)

**Target Outcome**: Schedule discovery calls with security leaders or product owners responsible for AI systems in production or pre-deployment.

**Campaign Duration**: 4 emails over 3-4 weeks.

## Audience Segmentation Guidance

Tailor messaging based on recipient role and organizational context:

### Security Leaders (CISO, Security Directors, AppSec Managers)

- **Pain Points**: Board pressure on AI governance, unclear how to assess AI risks, traditional pentesting misses AI-specific attacks
- **Language**: Focus on "trust boundaries," "NIST AI RMF compliance," "exploitable vulnerabilities"
- **Hook**: "Your traditional pentest won't find prompt injection or RAG poisoning"

### Engineering Leaders (VP Engineering, AI/ML Team Leads, Platform Architects)

- **Pain Points**: Rapid AI adoption without security validation, agent tool misuse risks, model leakage concerns
- **Language**: Focus on "model behavior," "tool privilege escalation," "RAG security"
- **Hook**: "We test what happens when your agent gets hijacked, not whether your API has CORS enabled"

### Product Leaders (CPO, Product Managers for AI Features)

- **Pain Points**: Launch delays from security concerns, competitive pressure to ship AI, unclear security requirements
- **Language**: Focus on "pre-deployment validation," "OWASP compliance," "audit-ready evidence"
- **Hook**: "Ship your AI feature with defensible security evidence, not crossed fingers"

## 4-Email Sequence

### Email 1: Reintroduction + Framing

**Purpose**: Remind recipient of your expertise and introduce AI red teaming as distinct from traditional pentesting.

**Timing**: Send immediately or as first touch.

**Subject Options:**
- "AI security isn't just another pentest"
- "Why your security team might miss AI risks"
- "The trust boundaries your AI pentest probably skipped"

**Body:**

---

Subject: AI security isn't just another pentest

[First Name],

Most security teams approach AI systems the same way they approach web apps: run a scanner, check for OWASP Top 10, call it done.

That approach misses the attacks that matter:

- **Prompt injection** that bypasses your system instructions
- **RAG poisoning** that corrupts every answer your model gives
- **Tool privilege escalation** where your agent does things it shouldn't
- **Model extraction** via API queries you thought were harmless

These aren't hypotheticals. They're actively exploited against production AI systems. And traditional pentesting doesn't catch them because it's designed for stateless APIs, not stateful, context-driven AI architectures.

We organize AI red teaming around **four trust boundaries**:
1. **Model** (jailbreaks, leakage, extraction)
2. **Data/Knowledge** (RAG poisoning, embeddings)
3. **Application/Agent Runtime** (prompt injection, tool misuse)
4. **Deployment/Governance** (access controls, monitoring gaps)

If your security assessment doesn't test all four boundaries, you're leaving the door open.

I'll follow up in a few days with specifics on how this works in practice. No sales pitch—just clarity on what makes AI security different.

[Your Name]

---

### Email 2: Problem Clarity + Why Incumbents Miss It

**Purpose**: Deepen understanding of the problem and position traditional approaches as insufficient.

**Timing**: 5-7 days after Email 1.

**Subject Options:**
- "Why traditional pentesting misses AI attacks"
- "The blind spots in your AI security assessment"
- "What OWASP Web Top 10 doesn't tell you about LLMs"

**Body:**

---

Subject: Why traditional pentesting misses AI attacks

[First Name],

Quick follow-up to my last note on AI security.

Here's why your current pentest approach likely misses AI-specific risks:

**1. Stateless thinking in a stateful world**
Traditional pentests assume each request is independent. But AI systems maintain context across turns. That means an attacker can build trust over multiple interactions before injecting malicious instructions—and your scanner won't catch it because it's testing one request at a time.

**2. Missing the data poisoning layer**
If your AI uses RAG (retrieval-augmented generation), an attacker can poison your knowledge base by injecting malicious documents into sources you index. Your model will then serve that poisoned content as truth. This attack happens **before** the API layer—so API testing doesn't find it.

**3. Tool abuse isn't XSS**
When your AI agent has tools (send email, query database, create tickets), the attack surface isn't "can I inject JavaScript?" It's "can I trick the agent into invoking tools with malicious parameters?" That requires understanding agent goal structures and prompt hierarchies—concepts outside traditional web security.

**4. Model behavior isn't binary**
You can't just check "does it allow SQL injection?" AI systems operate in probabilities. A jailbreak might work 30% of the time, depending on phrasing. Finding that requires adversarial prompt engineering, not static analysis.

This isn't a criticism of traditional pentesting—it's just a different discipline. You wouldn't use a web scanner to audit Kubernetes RBAC, and you shouldn't use it to audit AI trust boundaries.

Next email: I'll share what a proper AI security assessment actually looks like (scope, deliverables, what you can expect to learn).

[Your Name]

---

### Email 3: The Offer (AI Threat Exposure Review)

**Purpose**: Introduce the engagement with concrete scope, outcomes, and boundaries.

**Timing**: 5-7 days after Email 2.

**Subject Options:**
- "How we assess AI systems (and what you get)"
- "AI Threat Exposure Review: scope and deliverables"
- "What an AI security assessment actually tests"

**Body:**

---

Subject: How we assess AI systems (and what you get)

[First Name],

You asked (or will ask): "What does an AI security assessment actually involve?"

Here's the engagement we run most often: **AI Threat Exposure Review**.

**What it is:**
A comprehensive security assessment that evaluates your AI system across all four trust boundaries. We test for exploitable vulnerabilities using adversarial techniques—prompt injection, RAG poisoning, tool abuse, model extraction, and infrastructure control bypass.

**What's in scope:**
- Model behavior testing (jailbreaks, leakage, extraction)
- RAG security (data poisoning, retrieval hijacking)
- Agent & tool security (privilege escalation, unsafe invocations)
- Application integration (prompt assembly, output encoding)
- Infrastructure controls (access segmentation, monitoring gaps)

**What's out of scope:**
- Traditional infrastructure pentesting (we don't audit your VPC)
- Source code review (separate engagement)
- Training pipeline security (requires ML platform access)

**What you get:**
- Executive summary with business risk context
- Detailed technical findings with proof-of-concept exploits
- Risk-prioritized remediation roadmap
- OWASP/NIST/ATLAS framework compliance mapping
- Retest validation letter (post-remediation)

**Duration:** Typically 2-3 weeks depending on system complexity.

**When to use it:**
- Pre-deployment validation (before launching a new AI feature)
- M&A due diligence (assessing AI security of acquisition targets)
- Regulatory compliance (NIST AI RMF, EU AI Act readiness)
- Post-incident assessment (after a security event)

This isn't a compliance checkbox. It's a red team engagement that finds the bugs before attackers do.

If you're shipping AI to production (or evaluating a vendor who is), we should talk.

Reply with "interested" and I'll send over a one-pager, or we can schedule 20 minutes to discuss your specific use case.

[Your Name]

---

### Email 4: Soft Close + Opt-Out + Referral Ask

**Purpose**: Provide an easy exit, ask for referrals, and leave the door open for future contact.

**Timing**: 7-10 days after Email 3.

**Subject Options:**
- "Last note on AI security (+ a favor)"
- "Quick question before I close the loop"
- "Not the right time? No problem"

**Body:**

---

Subject: Last note on AI security (+ a favor)

[First Name],

I haven't heard back, so I'm guessing this isn't a priority right now—or I'm reaching the wrong person. No worries.

Before I close the loop, two quick things:

**1. If this isn't relevant to you, can you point me to the right person?**
I'm looking to connect with whoever owns AI security, AI/ML platform engineering, or application security for AI products at [Company]. If that's not you, a name or introduction would be hugely helpful.

**2. If it's just not the right time, want me to check back in Q3?**
AI security tends to become urgent right before launch or right after an incident. If you'd prefer I follow up later (or never), just let me know.

Either way, if you ever need clarity on AI red teaming, prompt injection defenses, or RAG security best practices—I'm happy to share what we know, no strings attached.

Appreciate your time.

[Your Name]

P.S. If you want me to stop emailing, just reply "unsubscribe" and I'll remove you immediately.

---

## Reply Handling Guidance

### "Isn't this just a pentest?"

**Response Template:**

> Traditional pentesting focuses on stateless APIs, infrastructure vulnerabilities, and OWASP Web Top 10 issues. AI red teaming focuses on trust boundaries unique to AI systems: model behavior (jailbreaks, extraction), data/knowledge surfaces (RAG poisoning, embeddings), agent runtime (tool misuse, goal hijacking), and AI-specific governance gaps (evaluation gates, prompt logging).
>
> Think of it this way: a web pentest finds SQL injection. An AI red team finds RAG injection—where an attacker poisons your knowledge base so your model serves malicious content as truth. Different attack surface, different discipline.
>
> That said, we often coordinate with your existing pentest team to ensure comprehensive coverage. We handle the AI-specific attacks; they handle traditional infrastructure.

### "What's the pricing?"

**Response Template:**

> Pricing depends on the scope of your AI system (number of models, RAG complexity, tool count, environment access). For an AI Threat Exposure Review, typical engagements range from [pricing guidance - insert your rates here] for 2-3 weeks of testing.
>
> The best next step is a 20-minute scoping call where we can understand your architecture and provide a fixed-price quote with clear deliverables.
>
> Does [specific date/time] work for a quick call?

**Note:** Only provide specific pricing if you have established rates. Otherwise, defer to scoping call.

### "Can you send me a one-pager?"

**Response Template:**

> Absolutely. I'll send over a one-pager that covers the AI Threat Exposure Review engagement: scope, deliverables, duration, and sample findings.
>
> One quick question before I send it: Are you evaluating this for a specific AI system you're launching, or is this more exploratory for now? That'll help me tailor the document to your context.
>
> I'll follow up with the one-pager within 24 hours.

**Action:** Prepare a one-pager PDF that summarizes the AI Threat Exposure Review engagement from `/docs/engagements/ai-threat-exposure-review.md`. Include:
- Purpose (1-2 sentences)
- What's Tested (trust boundaries overview)
- Deliverables (bullet list)
- Sample Finding (one concrete example)
- Next Steps (schedule scoping call)

## Compliance and Ethics Note

### Consent and Opt-Out

**CRITICAL:** Always respect recipient consent and provide clear opt-out mechanisms.

- Include an unsubscribe option in every email (e.g., "Reply 'unsubscribe' to opt out")
- Honor opt-out requests immediately (within 24 hours)
- Do not re-add opted-out contacts to future campaigns without explicit re-consent
- Maintain an opt-out list and cross-check before sending

### Legal and Regulatory Compliance

**This is not legal advice.** Consult qualified counsel for compliance with:

- **CAN-SPAM Act (US)**: Requires opt-out, physical address, and honest subject lines
- **GDPR (EU)**: Requires lawful basis for processing (legitimate interest or consent) and data subject rights
- **CASL (Canada)**: Requires express or implied consent before sending commercial emails

**Operational Guidance:**
- Only email contacts where you have a reasonable business relationship (prior engagement, conference connection, LinkedIn interaction)
- Avoid purchased email lists
- Use professional email infrastructure (not personal Gmail)
- Include your company's physical address in email footer
- Keep records of consent/opt-out for audit purposes

### Ethical Boundaries

**Do not:**
- Make guarantees about security outcomes ("We will find all vulnerabilities")
- Claim access to systems you don't have ("We already tested your competitor and found...")
- Use fear-mongering language ("Your AI is definitely vulnerable and attackers are targeting you right now")
- Misrepresent credentials or framework certifications

**Do:**
- Be transparent about what the engagement covers and what it doesn't
- Provide educational value even if they don't buy (builds trust)
- Respect "not interested" responses without pushback
- Acknowledge limitations of any security assessment

## Campaign Metrics and Iteration

Track these metrics to improve campaign performance:

- **Open Rate**: Target 25-35% (subject line effectiveness)
- **Reply Rate**: Target 5-10% (content relevance)
- **Discovery Call Conversion**: Target 30-50% of replies (qualification)
- **Engagement Conversion**: Target 20-30% of discovery calls (close rate)

**Iteration Guidance:**
- A/B test subject lines between security-focused and problem-focused variants
- Adjust email length based on reply rates (shorter often performs better)
- Track which pain points resonate most by audience segment
- Refine Email 2 based on questions you receive in replies

## Additional Resources

For more detail on the AI Threat Exposure Review engagement, see:
- `/docs/engagements/ai-threat-exposure-review.md` (full engagement specification)
- `/docs/trust-boundaries/` (technical depth on attack surfaces)
- `/docs/methodology/` (methodology and positioning)
