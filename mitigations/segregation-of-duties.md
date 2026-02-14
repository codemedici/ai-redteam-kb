---
title: "Segregation of Duties"
tags:
  - type/mitigation
  - target/llm-app
  - target/agent
  - target/ml-pipeline
  - target/training-pipeline
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Segregation of Duties

## Summary

Segregation of Duties (SOD) is a security control that prevents any single individual from having complete control over critical operations by requiring multi-party approval or involvement for sensitive actions. In LLM and ML systems, SOD ensures that high-risk operations—such as model deployment, training data modification, access control changes, and system configuration updates—require collaboration between multiple authorized parties, preventing insider threats, accidental misconfigurations, and unauthorized changes. SOD is particularly critical in AI systems where a single malicious or compromised actor could poison training data, deploy backdoored models, or exfiltrate sensitive information.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/weak-access-segmentation]] | Prevents single-person control over critical AI system operations |
| AML.T0020 | [[techniques/rag-data-poisoning]] | Requires multiple approvers for knowledge base data modifications |
| | [[techniques/supply-chain-model-poisoning]] | Multi-party approval prevents single person from deploying poisoned models |
| | [[techniques/model-tampering]] | Separation between model developers and deployment approvers prevents unauthorized model changes |
| | [[techniques/training-data-poisoning]] | Requires data curation and training approval by different parties |
| | [[techniques/insider-threat-model-theft]] | Prevents single insider from both accessing and exfiltrating model artifacts |

## Implementation

### Core SOD Principles for AI Systems

**Critical Operations Requiring SOD:**

1. **Model Deployment:**
   - **Developer** creates model and submits for deployment
   - **Security reviewer** validates model for safety and compliance
   - **Operations approver** authorizes production deployment
   - No single person can both develop and deploy to production

2. **Training Data Modification:**
   - **Data curator** proposes data changes
   - **Subject matter expert** validates data quality and accuracy
   - **Security reviewer** checks for poisoning attempts or sensitive data leakage
   - Multi-party approval required before training pipeline ingests changes

3. **Access Control Changes:**
   - **Requester** submits access change request
   - **Resource owner** approves or denies based on business need
   - **Security team** validates compliance with least privilege principles
   - Audit trail maintained for all access modifications

4. **System Configuration Changes:**
   - **Engineer** proposes configuration change
   - **Security team** reviews for security implications
   - **Change Advisory Board (CAB)** approves for production deployment

5. **Model Fine-Tuning:**
   - **Domain expert** prepares fine-tuning dataset
   - **Security team** validates dataset for poisoning and sensitive data
   - **ML engineer** executes fine-tuning
   - **Separate approver** deploys fine-tuned model to production

### Workflow Implementation Patterns

**Multi-Party Approval Workflow:**

```python
from enum import Enum
from datetime import datetime
from typing import List, Dict

class ApprovalStatus(Enum):
    PENDING = "pending"
    APPROVED = "approved"
    REJECTED = "rejected"

class ApprovalRequest:
    """
    Multi-party approval request for critical operations
    
    Implements segregation of duties for AI system changes
    """
    
    def __init__(
        self,
        request_id: str,
        requester: str,
        operation_type: str,
        operation_details: Dict,
        required_approvers: List[str]
    ):
        self.request_id = request_id
        self.requester = requester
        self.operation_type = operation_type
        self.operation_details = operation_details
        self.required_approvers = required_approvers
        self.approvals = {}
        self.status = ApprovalStatus.PENDING
        self.created_at = datetime.now()
        self.completed_at = None
    
    def add_approval(self, approver: str, decision: str, justification: str):
        """
        Record approval decision from required approver
        
        Args:
            approver: Approver user ID
            decision: "approve" or "reject"
            justification: Reason for decision
        """
        # Validate approver is in required list
        if approver not in self.required_approvers:
            raise PermissionError(
                f"{approver} is not authorized to approve this request"
            )
        
        # Prevent requester from approving their own request
        if approver == self.requester:
            raise PermissionError(
                "Requester cannot approve their own request (SOD violation)"
            )
        
        # Record approval decision
        self.approvals[approver] = {
            'decision': decision,
            'justification': justification,
            'timestamp': datetime.now()
        }
        
        # Log approval event
        log_sod_event("approval_recorded", {
            "request_id": self.request_id,
            "approver": approver,
            "decision": decision,
            "operation_type": self.operation_type
        })
        
        # Check if request can be executed
        self._update_status()
    
    def _update_status(self):
        """
        Update request status based on approvals
        
        - If any required approver rejects, request is rejected
        - If all required approvers approve, request is approved
        - Otherwise, request remains pending
        """
        # Check for rejections
        for approver, approval in self.approvals.items():
            if approval['decision'] == 'reject':
                self.status = ApprovalStatus.REJECTED
                self.completed_at = datetime.now()
                log_sod_event("request_rejected", {
                    "request_id": self.request_id,
                    "rejected_by": approver,
                    "operation_type": self.operation_type
                })
                return
        
        # Check if all approvers have approved
        approved_count = sum(
            1 for a in self.approvals.values()
            if a['decision'] == 'approve'
        )
        
        if approved_count == len(self.required_approvers):
            self.status = ApprovalStatus.APPROVED
            self.completed_at = datetime.now()
            log_sod_event("request_approved", {
                "request_id": self.request_id,
                "approvers": list(self.approvals.keys()),
                "operation_type": self.operation_type
            })
    
    def can_execute(self) -> bool:
        """Check if request has sufficient approvals to execute"""
        return self.status == ApprovalStatus.APPROVED

# Example: Model deployment with SOD
class ModelDeploymentRequest(ApprovalRequest):
    """
    Model deployment request requiring multiple approvals
    
    Required approvers:
    - Security reviewer (validates model safety)
    - Operations lead (authorizes production deployment)
    """
    
    def __init__(
        self,
        requester: str,
        model_id: str,
        model_version: str,
        deployment_environment: str
    ):
        required_approvers = [
            "security_reviewer",
            "operations_lead"
        ]
        
        operation_details = {
            'model_id': model_id,
            'model_version': model_version,
            'deployment_environment': deployment_environment
        }
        
        super().__init__(
            request_id=f"deploy_{model_id}_{model_version}",
            requester=requester,
            operation_type="model_deployment",
            operation_details=operation_details,
            required_approvers=required_approvers
        )
    
    def execute_deployment(self):
        """
        Execute model deployment after approvals obtained
        
        Raises:
            PermissionError if approvals not complete
        """
        if not self.can_execute():
            raise PermissionError(
                f"Cannot deploy: approval status is {self.status.value}"
            )
        
        # Execute deployment
        deploy_model_to_production(
            model_id=self.operation_details['model_id'],
            model_version=self.operation_details['model_version'],
            environment=self.operation_details['deployment_environment']
        )
        
        log_sod_event("deployment_executed", {
            "request_id": self.request_id,
            "model_id": self.operation_details['model_id'],
            "approvers": list(self.approvals.keys())
        })
```

### SOD Enforcement in CI/CD Pipelines

**GitOps Workflow with Required Reviewers:**

```yaml
# .github/workflows/model-deployment.yml
# Enforce SOD via branch protection and required reviews

name: Model Deployment

on:
  pull_request:
    paths:
      - 'models/**'
    types: [opened, synchronize]
    branches:
      - main

jobs:
  security-review:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Security scan
        run: |
          # Scan model artifacts for backdoors
          python scripts/scan_model_security.py
      
      - name: Require security team approval
        uses: actions/github-script@v6
        with:
          script: |
            const reviews = await github.rest.pulls.listReviews({
              owner: context.repo.owner,
              repo: context.repo.repo,
              pull_number: context.issue.number
            });
            
            const securityApproval = reviews.data.some(
              review => review.user.login === 'security-reviewer' &&
                       review.state === 'APPROVED'
            );
            
            if (!securityApproval) {
              core.setFailed('Security team approval required');
            }
  
  deploy:
    needs: security-review
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to production
        run: |
          # Deploy only after security and ops approval
          python scripts/deploy_model.py
```

**Branch Protection Rules (GitHub):**

- **Require pull request reviews before merging:** Enabled
- **Required approving reviews:** 2
- **Required reviewers:**
  - `security-reviewer` (for all model changes)
  - `ops-lead` (for production deployments)
- **Dismiss stale reviews when new commits pushed:** Enabled
- **Require review from code owners:** Enabled
- **Prevent requester from approving their own PR:** Enabled

### Data Modification SOD Pattern

**Training Data Change Approval:**

```python
class TrainingDataChangeRequest(ApprovalRequest):
    """
    Request to modify training data with multi-party approval
    
    Required approvers:
    - Subject matter expert (validates data quality)
    - Security reviewer (checks for poisoning)
    """
    
    def __init__(
        self,
        requester: str,
        dataset_id: str,
        change_type: str,  # 'add', 'modify', 'delete'
        data_records: List[Dict]
    ):
        required_approvers = [
            "subject_matter_expert",
            "security_reviewer"
        ]
        
        operation_details = {
            'dataset_id': dataset_id,
            'change_type': change_type,
            'data_records': data_records,
            'record_count': len(data_records)
        }
        
        super().__init__(
            request_id=f"data_change_{dataset_id}_{datetime.now().timestamp()}",
            requester=requester,
            operation_type="training_data_modification",
            operation_details=operation_details,
            required_approvers=required_approvers
        )
    
    def execute_data_change(self):
        """
        Apply training data changes after approvals
        """
        if not self.can_execute():
            raise PermissionError(
                f"Cannot modify data: approval status is {self.status.value}"
            )
        
        # Apply data changes with audit trail
        apply_training_data_changes(
            dataset_id=self.operation_details['dataset_id'],
            change_type=self.operation_details['change_type'],
            data_records=self.operation_details['data_records'],
            approved_by=list(self.approvals.keys()),
            request_id=self.request_id
        )
        
        log_sod_event("data_modification_executed", {
            "request_id": self.request_id,
            "dataset_id": self.operation_details['dataset_id'],
            "record_count": self.operation_details['record_count'],
            "approvers": list(self.approvals.keys())
        })
```

### Access Control SOD

**Prevent Self-Escalation:**

```python
def request_permission_change(
    requester: str,
    target_user: str,
    new_permissions: List[str]
):
    """
    Request permission change with SOD enforcement
    
    Prevents users from granting themselves permissions
    """
    # SOD Rule: Cannot change your own permissions
    if requester == target_user:
        raise PermissionError(
            "Cannot modify your own permissions (SOD violation)"
        )
    
    # Create approval request
    request = AccessChangeRequest(
        requester=requester,
        target_user=target_user,
        new_permissions=new_permissions,
        required_approvers=["resource_owner", "security_team"]
    )
    
    # Store request for approval workflow
    store_approval_request(request)
    
    # Notify approvers
    notify_approvers(request)
    
    return request.request_id
```

## Limitations & Trade-offs

**Limitations:**

- **Does not prevent collusion:** If multiple approvers collude, SOD can be bypassed
- **Operational overhead:** Multi-party approval adds delay to operations
- **Does not prevent all insider threats:** Authorized approvers can still abuse their privileges
- **Requires organizational discipline:** SOD policies must be consistently enforced

**Trade-offs:**

- **Security vs. Velocity:** Approval workflows slow down deployments and changes
- **Bureaucracy vs. Agility:** Strict SOD can hinder rapid incident response
- **Granularity vs. Complexity:** Fine-grained SOD rules increase management burden
- **Centralized vs. Distributed:** Centralized approval creates bottlenecks, distributed approval risks inconsistency

**Bypass Scenarios:**

- **Collusion:** Multiple approvers working together to bypass controls
- **Credential theft:** Attacker steals credentials from multiple approvers
- **Social engineering:** Attackers manipulate approvers into granting authorization
- **Emergency override abuse:** Override mechanisms intended for incidents used inappropriately

## Testing & Validation

**Functional Testing:**

1. **Self-Approval Prevention:** Verify users cannot approve their own requests
2. **Insufficient Approvals:** Test that operations blocked until all approvers confirm
3. **Rejection Handling:** Validate that single rejection blocks execution
4. **Approval Revocation:** Test what happens if approver revokes approval after granting

**Security Testing:**

1. **Bypass Attempts:** Try to execute critical operations without approvals
2. **Approval Forgery:** Attempt to forge approval records
3. **Privilege Escalation:** Test if users can escalate their own permissions
4. **Emergency Override Abuse:** Validate emergency processes cannot be abused

**Audit Testing:**

- Verify all critical operations have complete approval audit trails
- Test that approval history is immutable
- Validate approval notifications are sent correctly

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Segregation of duties: Require multi-party approval for critical operations (e.g., model deployment requires both developer submission and security approval)."
> — Derived from weak access segmentation mitigation recommendations

## Related

- **Combines with:** [[mitigations/access-segmentation-and-rbac]], [[mitigations/least-privilege-implementation]]
- **Requires:** [[mitigations/approval-workflow-patterns]] for implementation
- **Supports:** [[mitigations/incident-response-procedures]] (prevents insider threats)
