---
title: "CI/CD Evaluation Gates for ML Models"
tags:
  - type/mitigation
  - target/ml-model
  - target/training-pipeline
  - target/llm
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# CI/CD Evaluation Gates for ML Models

## Summary

CI/CD evaluation gates are security and quality checkpoints integrated into the machine learning development pipeline that prevent insecure, untested, or policy-violating models from reaching production. These gates enforce mandatory validation—including adversarial robustness testing, bias assessment, safety benchmarks, and security review—before models can be promoted across environments (dev → staging → prod). Missing or weak evaluation gates create deployment vulnerabilities where models with backdoors, data poisoning artifacts, safety failures, or compliance violations ship directly to users without detection.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/missing-evaluation-gates]] | Mandatory security validation gates prevent deployment of untested models |
| AML.T0020 | [[techniques/data-poisoning-attacks]] | Pre-deployment testing detects accuracy degradation and behavioral anomalies from poisoned training data |
| | [[techniques/backdoor-poisoning]] | Trigger-based testing and anomaly detection identify backdoored models before production |
| | [[techniques/jailbreak-policy-bypass]] | Safety benchmark testing catches models vulnerable to jailbreak attacks |

## Implementation

### Gate Architecture

**Three-tier gate structure:**

1. **Automated Gates** (blocking)
   - Unit tests for model code
   - Integration tests for data pipelines
   - Functional accuracy benchmarks
   - Security scanning (dependency vulnerabilities, code analysis)
   - Bias metrics against fairness thresholds
   - Performance regression tests

2. **Security Review Gates** (blocking)
   - Adversarial robustness testing results
   - Red team assessment report
   - Safety benchmark scores (e.g., ToxiGen, BBQ for bias)
   - Output filtering validation
   - Data provenance verification
   - Model card completeness check

3. **Governance Approval Gates** (blocking)
   - Human review of gate results
   - Risk assessment sign-off
   - Compliance validation
   - Deployment authorization from model owner

```python
class ModelPromotionPipeline:
    """CI/CD pipeline with evaluation gates"""
    
    def __init__(self, model, environment_target):
        self.model = model
        self.target = environment_target  # dev, staging, prod
        self.gate_results = {}
    
    def run_promotion_pipeline(self):
        """Execute all gates in sequence"""
        
        # Gate 1: Automated Testing
        if not self.automated_gate():
            raise DeploymentBlockedError("Automated gate failed", self.gate_results)
        
        # Gate 2: Security Review
        if not self.security_gate():
            raise DeploymentBlockedError("Security gate failed", self.gate_results)
        
        # Gate 3: Governance Approval (production only)
        if self.target == "prod" and not self.governance_gate():
            raise DeploymentBlockedError("Governance approval required", self.gate_results)
        
        # All gates passed - promote model
        self.promote_model()
        self.log_promotion_event()
    
    def automated_gate(self):
        """Automated checks - must all pass"""
        checks = {
            'unit_tests': self.run_unit_tests(),
            'integration_tests': self.run_integration_tests(),
            'accuracy_benchmark': self.check_accuracy_threshold(),
            'bias_metrics': self.check_fairness_metrics(),
            'security_scan': self.run_security_scanner(),
            'regression_test': self.check_no_performance_regression()
        }
        
        self.gate_results['automated'] = checks
        return all(checks.values())
    
    def security_gate(self):
        """Security validation - manual review required"""
        checks = {
            'adversarial_robustness': self.run_adversarial_tests(),
            'red_team_report': self.has_red_team_approval(),
            'safety_benchmarks': self.run_safety_benchmarks(),
            'output_filtering': self.validate_output_filters(),
            'data_provenance': self.verify_training_data_provenance(),
            'model_card': self.check_model_card_completeness()
        }
        
        self.gate_results['security'] = checks
        return all(checks.values())
    
    def governance_gate(self):
        """Governance approval for production"""
        checks = {
            'human_review': self.has_human_approval(),
            'risk_assessment': self.has_risk_sign_off(),
            'compliance': self.verify_compliance(),
            'deployment_authorization': self.has_deployment_auth()
        }
        
        self.gate_results['governance'] = checks
        return all(checks.values())
    
    def run_adversarial_tests(self):
        """Adversarial robustness evaluation"""
        from adversarial_testing import AdversarialTestSuite
        
        test_suite = AdversarialTestSuite(self.model)
        results = test_suite.run_all_attacks()
        
        # Check if model meets robustness threshold
        robustness_score = results['overall_robustness']
        threshold = 0.70  # 70% of adversarial examples handled correctly
        
        if robustness_score < threshold:
            self.gate_results['adversarial_failure_details'] = results
            return False
        
        return True
    
    def run_safety_benchmarks(self):
        """Safety and policy compliance testing"""
        benchmarks = {
            'jailbreak_resistance': self.test_jailbreak_resistance(),
            'toxicity_avoidance': self.test_toxicity_filtering(),
            'bias_fairness': self.test_fairness_benchmarks(),
            'pii_leakage': self.test_pii_prevention()
        }
        
        # All benchmarks must pass
        return all(benchmarks.values())
    
    def test_jailbreak_resistance(self):
        """Test against known jailbreak techniques"""
        from security_testing import JailbreakTestSuite
        
        jailbreak_suite = JailbreakTestSuite(self.model)
        results = jailbreak_suite.run_tests()
        
        # Model should block >90% of jailbreak attempts
        success_rate = results['jailbreak_blocked_percentage']
        return success_rate > 0.90
```

### Security-Focused Test Suites

**Adversarial robustness testing:**
```python
class AdversarialTestSuite:
    """Comprehensive adversarial attack testing"""
    
    def __init__(self, model):
        self.model = model
        self.attacks = [
            'prompt_injection',
            'adversarial_suffix',
            'semantic_perturbation',
            'backdoor_trigger',
            'data_extraction'
        ]
    
    def run_all_attacks(self):
        """Execute all attack categories"""
        results = {}
        
        for attack_type in self.attacks:
            attack_results = self.run_attack_category(attack_type)
            results[attack_type] = attack_results
        
        # Calculate overall robustness score
        results['overall_robustness'] = self.calculate_robustness(results)
        return results
    
    def run_attack_category(self, attack_type):
        """Run specific attack category"""
        test_cases = self.load_test_cases(attack_type)
        successful_defenses = 0
        
        for test_case in test_cases:
            model_output = self.model.predict(test_case['input'])
            
            # Check if model defended successfully
            if self.is_defended(model_output, test_case):
                successful_defenses += 1
        
        defense_rate = successful_defenses / len(test_cases)
        return {
            'test_cases': len(test_cases),
            'successful_defenses': successful_defenses,
            'defense_rate': defense_rate,
            'passed': defense_rate >= 0.70  # 70% threshold
        }
```

**Safety benchmark suite:**
```python
class SafetyBenchmarkSuite:
    """Safety and policy compliance benchmarks"""
    
    def __init__(self, model):
        self.model = model
        self.benchmarks = {
            'ToxiGen': self.run_toxigen,
            'BBQ': self.run_bbq_bias_benchmark,
            'RealToxicityPrompts': self.run_real_toxicity,
            'BOLD': self.run_bold_bias_benchmark
        }
    
    def run_all_benchmarks(self):
        """Execute all safety benchmarks"""
        results = {}
        
        for name, benchmark_fn in self.benchmarks.items():
            benchmark_results = benchmark_fn()
            results[name] = benchmark_results
        
        return results
    
    def run_toxigen(self):
        """ToxiGen benchmark for toxicity avoidance"""
        # Load ToxiGen test cases
        test_cases = load_toxigen_dataset()
        
        toxic_outputs = 0
        for test_case in test_cases:
            output = self.model.predict(test_case['prompt'])
            
            # Check if output is toxic
            if self.is_toxic(output):
                toxic_outputs += 1
        
        toxicity_rate = toxic_outputs / len(test_cases)
        
        return {
            'toxicity_rate': toxicity_rate,
            'passed': toxicity_rate < 0.05  # <5% toxic outputs
        }
```

### Approval Workflow Implementation

```python
class GovernanceApprovalWorkflow:
    """Human approval process for production deployments"""
    
    def __init__(self, model, gate_results):
        self.model = model
        self.gate_results = gate_results
        self.approvers = self.get_required_approvers()
    
    def get_required_approvers(self):
        """Determine who needs to approve based on risk"""
        risk_level = self.assess_risk_level()
        
        if risk_level == 'critical':
            return ['security_lead', 'compliance_officer', 'cto']
        elif risk_level == 'high':
            return ['security_lead', 'ml_lead']
        else:
            return ['ml_lead']
    
    def assess_risk_level(self):
        """Calculate deployment risk"""
        # Risk factors
        factors = {
            'user_facing': self.model.is_user_facing(),
            'handles_pii': self.model.processes_pii(),
            'financial_impact': self.model.has_financial_impact(),
            'first_deployment': not self.model.has_prior_version(),
            'failed_gates': self.count_gate_failures()
        }
        
        risk_score = sum(factors.values())
        
        if risk_score >= 4:
            return 'critical'
        elif risk_score >= 2:
            return 'high'
        else:
            return 'medium'
    
    def request_approval(self):
        """Send approval request to stakeholders"""
        approval_request = {
            'model_id': self.model.id,
            'model_name': self.model.name,
            'target_environment': 'production',
            'risk_level': self.assess_risk_level(),
            'gate_results': self.gate_results,
            'required_approvers': self.approvers,
            'approval_deadline': datetime.now() + timedelta(days=2)
        }
        
        # Send to approval system
        approval_system.create_request(approval_request)
        
        return approval_request['id']
    
    def check_approval_status(self, request_id):
        """Check if all required approvals received"""
        approvals = approval_system.get_approvals(request_id)
        
        # All required approvers must approve
        approved_by = set(a['approver'] for a in approvals if a['status'] == 'approved')
        required = set(self.approvers)
        
        return required.issubset(approved_by)
```

### Gate Failure Response

**Automated gate failure handling:**
```python
class GateFailureHandler:
    """Handle gate failures with automatic notifications"""
    
    def handle_failure(self, gate_name, gate_results, model):
        """Process gate failure"""
        
        # Log failure
        self.log_gate_failure(gate_name, gate_results, model)
        
        # Block deployment
        self.block_deployment(model)
        
        # Notify stakeholders
        self.send_failure_notifications(gate_name, gate_results, model)
        
        # Create remediation ticket
        self.create_remediation_ticket(gate_name, gate_results, model)
    
    def send_failure_notifications(self, gate_name, gate_results, model):
        """Alert relevant parties"""
        
        notification = {
            'title': f'Model Deployment Blocked: {gate_name} Failed',
            'model': model.name,
            'gate': gate_name,
            'failure_details': self.format_failure_details(gate_results),
            'severity': self.assess_failure_severity(gate_results),
            'recipients': self.get_notification_recipients(model)
        }
        
        notification_system.send(notification)
    
    def format_failure_details(self, gate_results):
        """Create human-readable failure summary"""
        failures = []
        
        for check, result in gate_results.items():
            if not result:
                failures.append(f"- {check}: FAILED")
        
        return "\n".join(failures)
```

## Limitations & Trade-offs

**Limitations:**
- **Test Coverage Gaps**: Evaluation gates only catch issues tested for; novel attacks may bypass gates
- **Test Set Representativeness**: Security benchmarks may not cover all deployment scenarios
- **Human Review Bottleneck**: Approval gates slow deployment velocity
- **False Negatives**: Sophisticated poisoning may pass automated checks

**Trade-offs:**
- **Speed vs. Security**: Comprehensive gates slow model deployment cycles
- **Automation vs. Human Judgment**: Fully automated gates miss nuanced risks; manual review slows process
- **Threshold Strictness**: Strict pass/fail criteria may block safe models; loose thresholds allow risky deployments
- **Cost**: Extensive testing infrastructure and red team resources are expensive

## Testing & Validation

**Functional Testing:**
1. **Gate Enforcement**: Attempt to deploy model without passing gates → should block
2. **Comprehensive Coverage**: Verify all security checks execute correctly
3. **Failure Handling**: Inject test failures, verify notifications and blocking work
4. **Approval Workflow**: Test approval routing and authorization logic

**Security Testing:**
1. **Gate Bypass Attempts**: Try to circumvent gates (skip checks, forge approvals)
2. **Poisoned Model Detection**: Deploy intentionally poisoned model → should fail security gate
3. **Backdoored Model Detection**: Deploy backdoored model → should fail adversarial testing
4. **Jailbreak Vulnerability**: Deploy jailbreak-vulnerable model → should fail safety benchmarks

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Security review of all training code before deployment. Strict version control for scripts/configurations. Red teaming during training. Security-focused validation datasets (prompt injections, policy boundary tests, data leakage triggers). Red team validation as final pre-deployment gate."
> — [[sources/bibliography#AI-Native LLM Security]], p. 249-276

> "Governance & Collaboration: Approval gates for changes. Data changes require approval. Model promotion (dev → staging → prod) requires review. Automated checks before approval. Benefit: Human review catches suspicious changes automated systems miss."
> — [[sources/bibliography#Adversarial AI]], p. 81-82

> "MLOps Security Controls: Testing. Test coverage across pipelines: Functional tests (model accuracy, performance), Security tests (robustness against adversarial attacks), Bias tests (fairness validation), Quality tests (data integrity checks)."
> — [[sources/bibliography#Adversarial AI]], p. 81-82

## Related

- **Implements**: Security validation checkpoints from [[mitigations/llm-development-lifecycle-security]]
- **Integrates with**: [[mitigations/mlops-security]], [[mitigations/red-team-continuous-testing]]
- **Enables**: Pre-deployment detection of poisoning, backdoors, jailbreak vulnerabilities, bias violations
