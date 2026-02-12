---
tags:
  - trust-boundary/general
  - type/methodology
---
# Phase 6: Triage & Severity Assessment

## Purpose

Triage transforms raw findings from Phase 5 into validated, risk-prioritized vulnerabilities ready for reporting. Not every discovered quirk merits the same level of concern—this phase identifies which findings truly represent significant AI failures or security gaps, applies consistent severity scoring, and provides business context for remediation prioritization.

**Key principle:** Even a single successful exploit proves the vulnerability exists and must be addressed. However, reproducibility, exploitability, and business impact determine severity.

---

## Objectives

- Classify findings (confirmed vs hypothesis vs observation)
- Verify reproducibility with statistical thresholds
- Apply AI-specific severity scoring
- Conduct root cause analysis
- Map findings to frameworks (ATLAS, OWASP, NIST)
- Prepare validated findings for reporting

---

## Key Activities

### 1. Finding Classification

**Purpose:** Distinguish between confirmed vulnerabilities, hypotheses needing validation, and observations

#### Confirmed Finding

**Criteria (ALL must be met):**

1. **Reproducible:** Attack succeeds at statistical threshold for severity level
2. **Exploitable:** Proof-of-concept demonstrates concrete harm measurable in business terms
3. **In-Scope:** Targets defined assessment boundaries (trust boundaries, threat model)
4. **Documented:** Complete evidence package enabling independent validation

**Example:**
- **Finding:** System prompt extraction via base64 encoding
- **Reproducibility:** 8 out of 10 attempts (80%)
- **Exploitability:** Reveals API keys embedded in system prompt
- **In-Scope:** Chatbot endpoint within scope
- **Documented:** Full transcripts, screenshots, reproduction script
- **Classification:** **Confirmed Finding** → High severity

---

#### Hypothesis

**Characteristics:**
- Theoretical vulnerability with partial evidence
- Needs deeper validation or specific conditions not yet achieved
- Promising attack vector but incomplete proof-of-concept

**Disposition:**
- Documented in appendix with "requires deeper validation" caveat
- May inform future testing or threat modeling
- Not assigned severity, not included in main findings count

**Example:**
- **Hypothesis:** Agent might be vulnerable to feedback loop poisoning
- **Evidence:** Partial success in one scenario, but cannot consistently reproduce
- **Status:** Document as hypothesis for future investigation

---

#### Observation

**Characteristics:**
- Security weakness noted but exploit not achieved
- Defense-in-depth concern without immediate exploitability
- Interesting behavior without clear business impact

**Disposition:**
- Documented for defense-in-depth consideration
- May be included in report as "Observations" section
- Not assigned severity, not counted in critical/high metrics

**Example:**
- **Observation:** Input validation accepts very long prompts (10K+ tokens)
- **Evidence:** No exploit achieved, but could enable resource exhaustion
- **Status:** Document as observation with recommendation to add length limits

---

#### Near-Miss

**Characteristics:**
- Attack almost succeeded
- Valuable for understanding defensive boundaries
- Helps inform robust mitigations

**Disposition:**
- Documented to inform remediation (don't just fix what broke, fix what almost broke)
- May indicate defense is fragile or easily bypassed with minor variations

**Example:**
- **Near-Miss:** Prompt injection attempt blocked by filter, but filter triggered on keyword match only
- **Insight:** Synonym or encoding would likely bypass
- **Status:** Document to recommend robust filter (semantic, not keyword-based)

---

### 2. Reproducibility Verification

**Purpose:** Validate that findings can be reliably triggered, accounting for AI non-determinism

#### Statistical Thresholds

**Reproducibility tiers:**

| Tier | Success Rate | Severity Impact | Classification |
|------|-------------|-----------------|----------------|
| **Deterministic** | 100% | No reduction | Confirmed |
| **Highly Reproducible** | ≥80% | No reduction | Confirmed |
| **Probabilistic** | 50-79% | Consider reduction | Confirmed (note variability) |
| **Edge Case** | &lt;50% | Significant reduction | Observation or Hypothesis |

**Verification process:**

1. **Re-execute PoC:** Run original attack 10-20 times (depending on finding severity)
2. **Control variables:** Use same model version, temperature, environment
3. **Document variability:** Note conditions that increase/decrease success
4. **Calculate success rate:** Successes / total attempts
5. **Classify reproducibility:** Apply tier based on success rate

**Example:**

**Finding:** Prompt injection → PII extraction
**Verification:**
- Attempts: 15
- Successes: 12 (80%)
- Conditions: Success rate higher with temperature 0.7 vs 0.3
- **Classification:** Highly Reproducible → Confirmed Finding

---

#### Handling Non-Determinism

**Strategies:**

**Control Randomness:**
- Set fixed random seed if possible
- Use deterministic mode
- Test across multiple model instances
- Test at different times (detect model updates)

**Document Variability:**
- What factors affect success rate?
- Are certain phrasings more effective?
- Does conversation history matter?
- Does time of day matter (model updates)?

**Establish Confidence:**
- Run enough trials for statistical significance
- Report confidence intervals if applicable
- Note that results may vary in production

[[methodology/handling-non-determinism|Learn more about handling non-determinism →]]

---

### 3. AI-Specific Severity Scoring

**Purpose:** Apply risk-based severity ratings that account for AI-specific factors

#### Traditional CVSS Limitations

CVSS assumes deterministic code execution, binary vulnerability states, and network-based vectors. AI systems exhibit probabilistic behavior, gradient vulnerabilities, and emergent risks.

**AI-specific adaptations needed:**
- Account for non-deterministic exploitability
- Consider autonomy risks (tool misuse, goal hijacking)
- Factor in persistence (memory poisoning, backdoors)
- Include safety violations (harmful content, CBRN)

---

#### Severity Dimensions

**1. Exploitability**

| Level | Description | Examples |
|-------|-------------|----------|
| **Trivial** | Single prompt, no prerequisites | "Ignore instructions...", basic jailbreak |
| **Moderate** | Multi-turn attack or requires system understanding | Gradual manipulation, encoding tricks |
| **Complex** | Requires insider knowledge, timing, or sophisticated tooling | Supply chain attack, coordinated multi-agent |

---

**2. Impact Categories**

**Confidentiality:**
- Training data extraction (PII, proprietary information)
- System prompt leakage (reveals defensive logic, API keys)
- Cross-tenant data access
- Credential exposure

**Integrity:**
- Business logic manipulation (unauthorized transactions)
- Data poisoning (corrupted RAG stores)
- Model behavior modification (persistent injection)
- Output manipulation (injected content)

**Autonomy Risk (AI-specific):**
- Tool misuse (unauthorized API invocations)
- Goal hijacking (redirected agent objectives)
- Cascading failures (multi-agent compromise)
- Unintended actions (harmful commands executed)

**Compliance/Safety:**
- Regulatory violations (GDPR, HIPAA)
- Safety policy bypass (harmful content generation)
- Fairness failures (discriminatory outputs)
- Misinformation at scale

---

**3. Scope**

| Level | Description | Examples |
|-------|-------------|----------|
| **Single-User** | Affects only attacker's session/data | Attacker extracts own data from training set |
| **Cross-User** | Can access other users' data or affect their sessions | Prompt injection accesses another user's conversation |
| **Multi-Tenant** | Breaks isolation between organizational boundaries | RAG poisoning affects all tenants |
| **Systemic** | Affects all users or corrupts shared resources | Model backdoor, knowledge base corruption |

---

**4. Persistence**

| Level | Description | Examples |
|-------|-------------|----------|
| **Transient** | Effect ends when session ends | Single-turn prompt injection |
| **Session** | Persists within user session | Multi-turn manipulation with memory |
| **Persistent** | Remains until explicitly fixed | Data poisoning, model backdoor, memory corruption |

---

**5. Detectability**

| Level | Description | Impact on Severity |
|-------|-------------|-------------------|
| **High** | Obvious, triggers alerts | Lower severity (will be caught) |
| **Medium** | Detectable with monitoring | Neutral |
| **Low** | Covert, no obvious signs | Higher severity (undetected abuse) |

---

#### Severity Matrix

| Impact | Exploitability | Scope | Base Severity |
|--------|---------------|-------|---------------|
| High Confidentiality/Integrity/Autonomy | Trivial | Systemic | **Critical** |
| High Confidentiality/Integrity | Moderate | Cross-User | **High** |
| Medium Confidentiality/Integrity | Moderate | Single-User | **Medium** |
| Low Impact | Complex | Single-User | **Low** |

**Special Elevation Rules (+1 severity level):**
- Safety violations (CBRN information, illegal activity instructions)
- Persistent compromise (backdoor, memory injection that survives sessions)
- Regulatory exposure (GDPR, HIPAA violations with legal consequences)

**Final Severity Levels:**

**Critical:**
- High impact + (Trivial exploit OR Systemic scope)
- Safety violations enabling physical harm
- Persistent compromise with broad impact

**High:**
- High impact + Moderate exploit
- Medium impact + Systemic scope
- Regulatory violations

**Medium:**
- Medium impact + Moderate exploit
- High impact + Complex exploit + Limited scope

**Low:**
- Low impact regardless of exploitability
- Complex exploit + Limited scope + Minor impact

---

#### Severity Calculation Example

**Finding:** Agent tool privilege escalation

**Analysis:**
- **Impact:** High (Autonomy Risk - unauthorized API invocations)
- **Exploitability:** Moderate (requires multi-turn goal hijacking)
- **Scope:** Cross-User (agent has access to shared tools)
- **Persistence:** Session (lasts until agent resets)
- **Detectability:** Low (tool calls look legitimate)

**Base Severity:** High (High impact + Moderate exploit + Cross-User)
**Elevation:** None
**Final Severity:** **High**

---

### 4. Root Cause Analysis

**Purpose:** Identify systemic patterns and underlying weaknesses, not just symptoms

**Questions to answer:**

**What is the root cause?**
- Is this a model training issue? (safety fine-tuning insufficient)
- Is this an application design issue? (insecure prompt assembly)
- Is this an infrastructure issue? (missing access controls)
- Is this a process issue? (no security review before deployment)

**Is this systemic or isolated?**
- Does this affect one feature or the entire system?
- Are there similar vulnerabilities in other components?
- Is this a pattern we've seen before?

**What defense failed?**
- Was there supposed to be a control here?
- Did the control fail or was it never implemented?
- Can we strengthen the control or need a new approach?

**Example:**

**Finding:** Multiple prompt injection vulnerabilities across different features

**Root Cause Analysis:**
- **Pattern:** All instances involve user input concatenated directly into prompts
- **Root Cause:** Insecure prompt assembly pattern used throughout codebase
- **Failed Defense:** No input sanitization or prompt structure enforcement
- **Systemic:** Affects all features that accept user input
- **Recommendation:** Implement prompt templating framework with input validation

**Outputs:**
- Root cause per finding or finding cluster
- Systemic patterns identified
- Defense gap analysis

---

### 5. Framework Mapping

**Purpose:** Tag findings with framework identifiers for traceability and compliance

#### MITRE ATLAS Mapping

Map each finding to ATLAS tactics and techniques:

**Example:**
- **Finding:** Prompt injection → PII extraction
- **ATLAS Tactic:** AML.TA0009 (Exfiltration)
- **ATLAS Technique:** AML.T0051 (LLM Prompt Injection)

[[atlas/techniques|Browse ATLAS techniques →]]

---

#### OWASP LLM Top 10 Mapping

Categorize findings by OWASP risk classes:

**Example:**
- **Finding:** System prompt leakage
- **OWASP Category:** LLM07 (System Prompt Leakage)

**Example:**
- **Finding:** Agent tool privilege escalation
- **OWASP Category:** LLM06 (Excessive Agency)

---

#### NIST AI RMF Mapping

Map findings to RMF functions:

**Example:**
- **Finding:** Insufficient monitoring (attacks not detected)
- **NIST Function:** MEASURE (gaps in measurement and monitoring)
- **RMF Implication:** Need to enhance observability controls

---

### 6. Impact Assessment

**Purpose:** Translate technical exploits to business consequences

**Business impact categories:**

**Financial:**
- Direct loss (fraud, theft)
- Indirect loss (incident response costs, downtime)
- Regulatory fines

**Reputational:**
- Customer trust loss
- Public disclosure
- Competitive disadvantage

**Operational:**
- Service disruption
- Resource exhaustion
- Productivity impact

**Legal/Compliance:**
- Regulatory violations (GDPR, HIPAA)
- Contractual breaches
- Audit failures

**Safety:**
- Physical harm potential
- Dangerous instructions
- CBRN information

**Example:**

**Finding:** Cross-tenant PII leakage via RAG

**Technical Impact:** User A can retrieve User B's documents via crafted queries

**Business Impact:**
- **Financial:** GDPR fines (up to 4% of revenue), class action lawsuits
- **Reputational:** Loss of customer trust, negative press
- **Legal:** Regulatory investigation, contractual breaches with enterprise customers
- **Operational:** Potential service shutdown until fixed

**Severity Justification:** Critical (cross-tenant data breach with regulatory exposure)

---

## Outputs

By the end of Phase 6, the following artifacts should be complete:

1. **Validated Findings List**
   - Confirmed findings with severity ratings
   - Hypotheses and observations separated
   - Framework mappings (ATLAS, OWASP, NIST)

2. **Severity Rationale Document**
   - Justification per severity rating
   - Scoring dimensions documented
   - Special elevations explained

3. **Root Cause Analysis**
   - Systemic patterns identified
   - Defense gap analysis
   - Recommendations preview

4. **Impact Assessment**
   - Business consequences per finding
   - Risk prioritization matrix
   - Stakeholder-facing impact summaries

5. **Framework Compliance Matrix**
   - OWASP Top 10 coverage
   - ATLAS technique mapping
   - NIST RMF gaps

---

## Common Mistakes to Avoid

### Inconsistent Severity Scoring

**Problem:** Different testers apply different severity criteria

**Impact:** Confusing or disputed severity ratings, misaligned remediation priorities

**Solution:** Use standardized severity matrix, document rationale, peer review severity assignments

---

### Ignoring Reproducibility

**Problem:** Reporting one-off successes without validation

**Impact:** False positives, unreproducible findings, wasted remediation effort

**Solution:** Verify all findings with multiple trials, document success rates

---

### Missing Business Context

**Problem:** Technical severity without business impact analysis

**Impact:** Stakeholders don't understand why findings matter, misaligned priorities

**Solution:** Always map technical impact to business consequences

---

### No Root Cause Analysis

**Problem:** Treating each finding in isolation

**Impact:** Missing systemic patterns, fixing symptoms not causes

**Solution:** Look for patterns across findings, identify root causes

---

## Timeline

**Typical duration:** 1-2 days

**Activities breakdown:**
- Finding classification: 4 hours
- Reproducibility verification: 1 day (parallel testing)
- Severity scoring: 4-6 hours
- Root cause analysis: 4 hours
- Framework mapping: 2-3 hours
- Impact assessment: 4 hours

---

## Transition to Phase 7

Once triage is complete, the engagement transitions to [[methodology/phase-7-reporting|Phase 7: Reporting & Communication]].

**Handoff checklist:**
- [ ] All findings classified (confirmed/hypothesis/observation)
- [ ] Severity ratings assigned with justification
- [ ] Root cause analysis complete
- [ ] Framework mappings validated
- [ ] Business impact assessed per finding

**Next phase preview:**
Phase 7 will package the validated findings into executive and technical reports, create the remediation roadmap, and prepare for stakeholder presentation.

---

## Related Documentation

- [[methodology/lifecycle-overview|Lifecycle Overview]] - How Phase 6 fits into the full engagement
- [[methodology/phase-5-execution|Phase 5: Execution]] - Previous phase
- [[methodology/phase-7-reporting|Phase 7: Reporting]] - Next phase
- [[methodology/evidence-reproducibility|Evidence & Reproducibility]] - Standards and thresholds
- [[methodology/handling-non-determinism|Handling Non-Determinism]] - Statistical validation
- [[methodology/framework-mapping|Framework Mapping]] - ATLAS, OWASP, NIST integration
