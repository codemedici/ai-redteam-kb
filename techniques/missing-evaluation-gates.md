---
title: "Missing Evaluation Gates"
tags:
  - type/technique
  - target/ml-model
  - target/training-pipeline
  - target/llm
  - access/gray-box
  - access/white-box
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

## Summary

Missing evaluation gates occur when ML models are deployed to production without adequate security testing, validation checkpoints, or approval workflows in the CI/CD pipeline. This technique exploits the absence of mandatory pre-deployment security gates—such as adversarial robustness testing, safety benchmarks, bias assessment, or red team validation—to ship vulnerable, poisoned, backdoored, or policy-violating models directly to users. Without evaluation gates, insecure models bypass detection that would normally occur in a defense-in-depth development lifecycle, creating a deployment governance vulnerability where technical security failures reach production undetected.


## Mechanism

### Exploitation of Absent Gates

**Attack pattern:**

1. **Attacker introduces vulnerability** during model development:
   - Data poisoning in training dataset
   - Backdoor implantation via trigger injection
   - Fine-tuning on policy-violating data
   - Supply chain compromise (poisoned base model)

2. **Model training proceeds** without anomaly detection or validation

3. **CI/CD pipeline lacks security gates**:
   - No adversarial robustness testing
   - No safety benchmark validation
   - No red team review requirement
   - No bias/fairness assessment
   - No human approval workflow

4. **Vulnerable model auto-deploys to production** because:
   - Functional accuracy tests pass (poisoning/backdoors don't affect benign inputs)
   - No security-specific checks exist to catch the vulnerability
   - Deployment is fully automated without human review

5. **Vulnerability reaches users** and becomes exploitable at scale

### Common Missing Gates

| Missing Gate | Enables Attack | Impact |
|-------------|----------------|---------|
| **Adversarial robustness testing** | Prompt injection, jailbreaks, adversarial examples | Deployed model vulnerable to known attack techniques |
| **Safety benchmarks** | Policy bypass, toxic output generation | Model violates safety policies in production |
| **Backdoor detection** | Triggered malicious behavior | Hidden functionality exploitable by attacker |
| **Bias/fairness assessment** | Discriminatory outputs | Compliance violations, reputational damage |
| **Red team validation** | Novel attack vectors | Undiscovered vulnerabilities ship to production |
| **Data provenance verification** | Supply chain poisoning | Compromised training data goes undetected |
| **Human approval workflow** | All of the above | No human judgment checkpoint before production |

### Why Gates Are Often Missing

**Organizational factors:**
- **Speed pressure**: Fast deployment cycles prioritize velocity over security validation
- **Cost concerns**: Comprehensive testing infrastructure and red team resources are expensive
- **Lack of awareness**: Teams unaware of ML-specific security risks
- **Tooling gaps**: Security testing tools for ML are immature compared to traditional software
- **Siloed teams**: Security teams lack ML expertise; ML teams lack security training

**Technical factors:**
- **Automated CI/CD**: Fully automated pipelines designed for speed, not security
- **Inherited workflows**: Traditional software CI/CD patterns ported to ML without adaptation
- **Test coverage blindness**: Functional tests pass even when security vulnerabilities exist
- **Metrics misalignment**: Deployment gates check accuracy/performance, not adversarial robustness


## Preconditions

- Organization deploys ML models via CI/CD pipeline
- Pipeline lacks mandatory security validation gates (adversarial testing, safety benchmarks, red team review)
- Model promotion (dev → staging → prod) is automated without human approval checkpoints
- Attacker has access to introduce vulnerabilities during development (insider threat, supply chain compromise, or compromised training data pipeline)
- No compensating controls exist in production (runtime monitoring may detect but not prevent initial deployment)


## Impact

**Business Impact:**
- **Reputational damage**: Deployed model generates toxic, biased, or harmful outputs associated with brand
- **Legal liability**: Model violates regulations (discriminatory outputs, unsafe recommendations, privacy violations)
- **Compliance violations**: Models deployed without required risk assessments or approvals
- **Competitive disadvantage**: Publicized security failures undermine market position
- **User harm**: Vulnerable models provide incorrect medical/financial/safety-critical advice
- **Incident response costs**: Expensive emergency model rollback and remediation after production failures

**Technical Impact:**
- **Poisoned models reach production**: Data poisoning artifacts (accuracy degradation, targeted misclassification) affect real users
- **Backdoors activate in production**: Trigger-based malicious behavior exploitable at scale
- **Jailbreak vulnerabilities exploitable**: Models vulnerable to prompt injection ship without detection
- **Bias violations deployed**: Discriminatory models violate fairness requirements
- **No pre-deployment detection**: Vulnerabilities discovered only after user impact or public disclosure
- **Rollback complexity**: Production models must be emergency-patched or rolled back, disrupting service
- **Audit trail gaps**: Lack of approval records creates compliance and forensic challenges

**Severity:** **High** to **Critical** depending on:
- Model criticality (user-facing vs. internal)
- Data sensitivity (PII, financial, health)
- Deployment scale (number of users affected)
- Presence of compensating controls (runtime monitoring, output filtering)


## Detection

**Indicators of missing evaluation gates:**

**In CI/CD pipelines:**
- Model deployment jobs lack adversarial testing stages
- No security scanning or red team review steps in pipeline definitions
- Deployment approval workflows do not exist or are skippable
- Pipeline execution logs show no security validation activities
- Deployment succeeds despite known vulnerabilities in model

**In development processes:**
- Model promotion (dev → staging → prod) is fully automated without human checkpoints
- Security team has no visibility into ML deployments
- No documented security requirements for model deployment
- Developers unaware of ML-specific security testing tools or practices

**In production incidents:**
- Models vulnerable to known attacks (jailbreaks, prompt injection) discovered post-deployment
- Poisoned or backdoored models detected only after user reports or external research
- Emergency model rollbacks due to safety failures that should have been caught pre-deployment


## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |


## Mitigations

| ID | Name | Description |
|----|------|-------------|
| | [[mitigations/ci-cd-evaluation-gates]] | Mandatory security validation checkpoints in CI/CD prevent deployment of untested models |
| | [[mitigations/llm-development-lifecycle-security]] | Comprehensive security practices across development lifecycle include red team validation as final pre-deployment gate |
| | [[mitigations/mlops-security]] | Approval gates, testing frameworks, and governance controls enforce security validation before promotion |
| | [[mitigations/red-team-continuous-testing]] | Ongoing adversarial testing identifies vulnerabilities before production deployment |
| AML.M0026 | [[mitigations/ab-testing-validation]] | Reference model comparison detects behavioral anomalies from poisoning or backdoors before full deployment |


## Sources

> "Security review of all training code before deployment. Red team validation as final pre-deployment gate."
> — [[sources/bibliography#AI-Native LLM Security]], p. 249-276

> "Governance & Collaboration: Approval gates for changes. Model promotion (dev → staging → prod) requires review. Human review catches suspicious changes automated systems miss."
> — [[sources/bibliography#Adversarial AI]], p. 81-82
