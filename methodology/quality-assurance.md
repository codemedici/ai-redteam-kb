---
tags:
  - trust-boundary/general
  - type/methodology
---
# Quality Assurance and Peer Review

## Internal QA Process

Before delivery:
- **Technical peer review**: Second red teamer validates findings independently
- **Impact validation**: Business consequences reviewed for accuracy
- **Remediation feasibility**: Mitigation guidance assessed for practicality
- **Framework accuracy**: ATLAS/OWASP/NIST mappings verified
- **Evidence completeness**: Reproduction steps tested by uninvolved team member

## Customer Validation Points

- **Mid-engagement checkpoint** (optional): Interim findings review at 50% completion
- **Draft report review**: Technical team validates findings before executive delivery
- **Walkthrough session**: Interactive Q&A on findings and remediation strategy
- **Retest validation**: Confirm fixes resolve root causes, not just symptoms

---

## Continuous Testing and Automation

**Purpose:** Integrate automated security testing throughout SDLC to complement manual red teaming

**Core principle:** Continuous validation prevents regression and catches issues between manual assessments

### Automated Testing Strategies

#### CI/CD Pipeline Integration

**Pre-commit checks:**
- Static analysis on prompt templates (detect hardcoded instructions vulnerable to injection)
- Dependency vulnerability scanning (known CVEs in ML frameworks)
- Secret detection (API keys, credentials in training scripts)

**Build-time validation:**
- Automated adversarial example generation and testing
- Model robustness metrics calculation (accuracy under FGSM/PGD attacks)
- Prompt injection test suite execution (known jailbreak patterns)
- Data validation schema checks

**Pre-deployment gates:**
- Security metric thresholds enforcement (e.g., under 5% jailbreak success rate)
- Framework compliance validation (OWASP LLM Top 10 coverage)
- Automated vulnerability scanning on deployment configs

---

#### Security Metrics and Thresholds

**Robustness metrics (adversarial ML):**
- **Adversarial accuracy**: Model accuracy under adversarial attacks (target: over X percent under Y perturbation budget)
- **Attack success rate**: Percentage of adversarial examples causing misclassification (target: under Z percent)
- **Transferability**: Success of attacks trained on surrogate models (measures black-box risk)

**Generative AI safety metrics:**
- **Jailbreak success rate**: Percentage of malicious prompts bypassing filters (target: 0% for known patterns)
- **Toxicity score**: Model outputs triggering content moderation systems (target: under threshold)
- **Refusal rate**: Percentage of harmful requests properly refused (target: over 99%)

**Privacy risk metrics:**
- **Membership inference accuracy**: Ability to determine if data point was in training set (target: near-random guessing)
- **Training data extraction rate**: Success of attacks recovering training examples (target: 0%)
- **PII leakage rate**: Model outputs containing sensitive personal information (target: 0%)

**Infrastructure security metrics:**
- **API authentication bypass rate**: Failed attempts to access unauthorized endpoints (target: 0%)
- **Rate limiting effectiveness**: Percentage of abuse attempts blocked (target: over 99%)
- **Secrets exposure**: Count of credentials in logs, prompts, outputs (target: 0)

**Example threshold configuration:**
```yaml
security_metrics:
  robustness:
    adversarial_accuracy_pgd: minimum 85%
    evasion_success_rate: maximum 10%
  safety:
    jailbreak_success_known_patterns: maximum 0%
    jailbreak_success_novel_patterns: maximum 5%
    harmful_output_rate: maximum 0.1%
  privacy:
    membership_inference_advantage: maximum 5%
    training_data_leakage_rate: maximum 0%
```

**Failure handling:** If metrics fail thresholds, automatically block deployment and trigger remediation workflow

---

#### Automated Red Team Testing Frameworks

**LLM-specific automated tools:**
- **Garak**: Automated prompt injection and jailbreak testing with curated attack libraries
- **PyRIT**: Multi-turn adversarial campaign automation for generative AI
- **Custom harnesses**: Domain-specific test suites tailored to application

**Adversarial ML automation:**
- **ART (Adversarial Robustness Toolbox)**: Automated evasion attack generation (FGSM, PGD, C&W)
- **TextAttack**: NLP-specific attack automation (synonym substitution, paraphrasing)
- **Foolbox**: Automated adversarial example generation across frameworks

**Implementation guidance:**
1. **Baseline with known attacks**: Run established attack patterns to establish security floor
2. **Schedule regular runs**: Nightly or per-commit execution in CI/CD
3. **Tunable difficulty**: Start with simple attacks (FGSM), increase to sophisticated (PGD, adaptive)
4. **Result integration**: Automatically create tickets for failed tests
5. **Trend tracking**: Monitor metrics over time to detect regression

---

### Regression Testing

**Purpose:** Ensure fixes don't break and new changes don't reintroduce vulnerabilities

**Process:**
1. **Convert findings to test cases**: Every manual red team finding becomes automated regression test
2. **Maintain test suite**: Update as new attack patterns discovered
3. **Pre-deployment verification**: Run full regression suite before each release
4. **Post-remediation validation**: Retest after fixes to confirm effectiveness

**Example workflow:**
```
Manual Red Team Finding: Prompt injection via Unicode homoglyphs
↓
Create automated test: Generate 100 homoglyph variations, verify all blocked
↓
Add to CI/CD pipeline: Run on every commit to prompt handling code
↓
Regression prevention: Future changes can't reintroduce vulnerability
```

---

### Continuous Monitoring and Feedback

**Runtime security monitoring:**
- **Anomaly detection**: Unusual query patterns, input characteristics
- **Attack signature matching**: Known prompt injection patterns in production traffic
- **Model drift detection**: Performance degradation signals potential poisoning
- **Output filtering logs**: Track blocked harmful outputs, identify bypass attempts

**Feedback to development:**
- **Weekly security dashboards**: Trend reports on blocked attacks, new patterns
- **Incident post-mortems**: Analysis of production exploits feeding into test suite
- **Threat intelligence updates**: External attack trends informing automated testing

**Continuous improvement loop:**
```
Production monitoring detects new attack pattern
↓
Security team analyzes and documents technique
↓
Add to automated test suite
↓
Update defenses (filters, training data)
↓
Deploy and validate
↓
Monitor for bypass attempts
↓
(Repeat)
```

---

## Collaboration Models

**Purpose:** Effective partnership between development, security, and red teams throughout lifecycle

### Development and Security Integration

**Embedded security champions:**
- Developers trained in AI security fundamentals
- Act as liaison between engineering and security teams
- Conduct initial threat modeling and secure design reviews
- Escalate complex issues to red team

**Regular touchpoints:**
- **Sprint planning**: Security considerations in feature design
- **Design reviews**: Red team consultation on new AI features
- **Code reviews**: Security-focused peer review for critical components (prompt handling, API auth, data validation)

**Shared tools and visibility:**
- Security teams have read access to code repositories
- Developers can view red team findings and test results
- Shared dashboard for security metrics and test coverage

### Red Team Integration

**Continuous red team engagement:**
- **On-demand consultation**: Ad-hoc design reviews, threat modeling assistance
- **Scheduled assessments**: Quarterly full-stack red team engagements
- **Targeted testing**: Per-feature security validation before launch

**Knowledge transfer:**
- **Training**: Red team teaches developers AI attack techniques
- **Workshops**: Hands-on secure coding and threat modeling sessions
- **Documentation**: Playbooks, attack pattern libraries, remediation guides

### External Programs

**Bug bounty programs:**
- Invite external security researchers to test production AI systems
- Define scope (in-scope: prompt injection, data leakage; out-of-scope: brute force, DDoS)
- Establish reward structure based on severity and novelty
- Supplement internal red teaming with diverse attacker perspectives

**Responsible disclosure:**
- Public security contact (security@company.com)
- Clear disclosure process and timelines
- Acknowledgment and recognition for reporters
- Coordinated vulnerability disclosure (CVD) for critical issues

---

## Quality Benchmarks

**Engagement quality indicators:**
- **Coverage**: Percentage of trust boundaries tested
- **Depth**: Average techniques per finding type
- **Novelty**: Ratio of new vs known attack patterns
- **Actionability**: Percentage of findings with complete remediation guidance
- **Remediation success**: Percentage of validated fixes on first retest

**Continuous testing maturity:**
- **Level 1**: Manual testing only
- **Level 2**: Automated testing in pre-deployment
- **Level 3**: CI/CD integration with basic metrics
- **Level 4**: Comprehensive automation with thresholds and gates
- **Level 5**: Continuous monitoring with automated response
