---
title: "Access Review and Audit"
tags:
  - type/mitigation
  - target/llm-app
  - target/agent
  - target/rag-system
  - target/ml-pipeline
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Access Review and Audit

## Summary

Access review and audit is a continuous governance control that prevents permission creep, detects unauthorized access, and ensures access controls remain aligned with business needs and security policies. Through regular permission audits, automated drift detection, and compliance validation, this control identifies overly permissive grants, orphaned accounts, inappropriate access patterns, and policy violations before they can be exploited. In LLM systems where access decisions govern model inference, knowledge base retrieval, tool invocation, and data access, maintaining accurate and minimal permissions is critical to preventing unauthorized disclosure, privilege escalation, and policy bypass.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/weak-access-segmentation]] | Detects permission creep and overly permissive access grants that violate least privilege |
| | [[techniques/auth-context-confusion]] | Identifies authorization policy gaps and misconfigurations through regular audits |
| | [[techniques/unauthorized-knowledge-disclosure]] | Audit trails reveal unauthorized knowledge base access patterns and cross-tenant data leakage |
| | [[techniques/insider-threat-model-theft]] | Permission audits detect excessive access to model artifacts and training data |
| | [[techniques/prompt-log-data-leakage]] | Access reviews identify unauthorized access to sensitive prompt logs |

## Implementation

### Regular Permission Review Process

**Quarterly Access Certification:**

1. **Access Inventory Generation:**
   - Extract all user accounts, roles, and permissions
   - Document resource access (models, data, tools, logs)
   - Generate per-user access summary
   - Identify access granted in last quarter

2. **Manager Review:**
   - Send access reports to resource owners and managers
   - Require explicit certification or revocation for each user
   - Highlight high-risk permissions (admin, cross-tenant, sensitive data)
   - Set deadline for review (e.g., 14 days)

3. **Exception Review:**
   - Identify accounts with no manager (orphaned accounts)
   - Flag excessive permissions (beyond role requirements)
   - Review emergency access grants still active
   - Validate service account permissions

4. **Remediation:**
   - Automatically revoke uncertified access
   - Remove orphaned accounts
   - Reduce excessive permissions to role baseline
   - Document justification for exceptions retained

**Implementation Example:**

```python
from datetime import datetime, timedelta
from typing import List, Dict

class AccessReviewCampaign:
    """
    Quarterly access review and certification campaign
    
    Generates access reports, distributes to managers,
    and automates remediation based on responses
    """
    
    def __init__(self, campaign_id: str, review_period_start: datetime):
        self.campaign_id = campaign_id
        self.review_period_start = review_period_start
        self.review_deadline = datetime.now() + timedelta(days=14)
        self.certifications = {}
    
    def generate_access_reports(self) -> List[Dict]:
        """
        Generate per-manager access reports
        
        Returns list of reports for each manager showing
        users they are responsible for and their access
        """
        reports = []
        
        # Get all active users grouped by manager
        user_groups = get_users_by_manager()
        
        for manager, users in user_groups.items():
            manager_report = {
                'manager': manager,
                'review_deadline': self.review_deadline,
                'users': []
            }
            
            for user in users:
                user_access = self.get_user_access_summary(user)
                manager_report['users'].append(user_access)
            
            reports.append(manager_report)
        
        return reports
    
    def get_user_access_summary(self, user_id: str) -> Dict:
        """
        Summarize user's current access for review
        
        Returns dict with user details, roles, permissions,
        and high-risk access indicators
        """
        # Get user details
        user = get_user_details(user_id)
        
        # Get assigned roles and permissions
        roles = get_user_roles(user_id)
        permissions = get_user_permissions(user_id)
        
        # Identify high-risk access
        high_risk_flags = self.identify_high_risk_access(user_id, permissions)
        
        # Get access granted in review period
        recent_grants = get_recent_access_grants(
            user_id,
            since=self.review_period_start
        )
        
        # Calculate last activity
        last_activity = get_last_activity_date(user_id)
        
        return {
            'user_id': user_id,
            'user_name': user.name,
            'department': user.department,
            'roles': roles,
            'permissions': permissions,
            'high_risk_flags': high_risk_flags,
            'recent_grants': recent_grants,
            'last_activity': last_activity,
            'requires_certification': True
        }
    
    def identify_high_risk_access(self, user_id: str, permissions: List[str]) -> List[str]:
        """
        Flag high-risk permissions requiring extra scrutiny
        
        Returns list of risk indicators
        """
        flags = []
        
        # Admin permissions
        if any(p.startswith('admin:') for p in permissions):
            flags.append('ADMIN_ACCESS')
        
        # Cross-tenant access
        if 'access:all_tenants' in permissions:
            flags.append('CROSS_TENANT_ACCESS')
        
        # Sensitive data access
        sensitive_perms = [
            'read:confidential_data',
            'access:training_data',
            'read:model_artifacts',
            'access:prompt_logs'
        ]
        if any(p in permissions for p in sensitive_perms):
            flags.append('SENSITIVE_DATA_ACCESS')
        
        # Dormant account (no activity in 90 days)
        last_activity = get_last_activity_date(user_id)
        if (datetime.now() - last_activity).days > 90:
            flags.append('DORMANT_ACCOUNT')
        
        # Excessive permissions (more than 2 standard deviations above role baseline)
        if self.is_excessive_permissions(user_id, permissions):
            flags.append('EXCESSIVE_PERMISSIONS')
        
        return flags
    
    def is_excessive_permissions(self, user_id: str, permissions: List[str]) -> bool:
        """
        Check if user has excessive permissions compared to role peers
        """
        user_roles = get_user_roles(user_id)
        
        # Get average permission count for users with same roles
        peer_avg = get_average_permission_count_for_roles(user_roles)
        peer_stddev = get_permission_count_stddev_for_roles(user_roles)
        
        # Flag if user has >2 stddev more permissions than peers
        threshold = peer_avg + (2 * peer_stddev)
        
        return len(permissions) > threshold
    
    def record_certification(
        self,
        manager: str,
        user_id: str,
        decision: str,
        justification: str
    ):
        """
        Record manager's certification decision
        
        Args:
            manager: Manager certifying the access
            user_id: User being reviewed
            decision: "certify" or "revoke"
            justification: Reason for decision
        """
        self.certifications[user_id] = {
            'manager': manager,
            'decision': decision,
            'justification': justification,
            'timestamp': datetime.now()
        }
        
        log_audit_event("access_certification", {
            "campaign_id": self.campaign_id,
            "user_id": user_id,
            "manager": manager,
            "decision": decision
        })
    
    def execute_remediation(self):
        """
        Execute remediation actions after review deadline
        
        - Revoke access for uncertified users
        - Revoke access where manager explicitly revoked
        - Remove orphaned accounts
        - Generate remediation report
        """
        remediation_actions = []
        
        # Get all users in scope
        all_users = get_all_active_users()
        
        for user_id in all_users:
            certification = self.certifications.get(user_id)
            
            # Uncertified access after deadline
            if certification is None:
                if datetime.now() > self.review_deadline:
                    revoke_all_access(user_id, reason="uncertified_after_deadline")
                    remediation_actions.append({
                        'user_id': user_id,
                        'action': 'revoke_all',
                        'reason': 'uncertified'
                    })
            
            # Explicitly revoked
            elif certification['decision'] == 'revoke':
                revoke_all_access(user_id, reason=certification['justification'])
                remediation_actions.append({
                    'user_id': user_id,
                    'action': 'revoke_all',
                    'reason': 'manager_revoked'
                })
        
        # Log remediation summary
        log_audit_event("access_review_remediation", {
            "campaign_id": self.campaign_id,
            "total_users_reviewed": len(all_users),
            "certifications_received": len(self.certifications),
            "remediation_actions": len(remediation_actions)
        })
        
        return remediation_actions
```

### Automated Permission Drift Detection

**Continuous Monitoring for Permission Anomalies:**

```python
class PermissionDriftDetector:
    """
    Detect permission drift and policy violations
    
    Monitors permission changes and flags anomalies
    """
    
    def __init__(self):
        self.baseline_permissions = {}
        self.load_baseline()
    
    def load_baseline(self):
        """
        Load expected permission baselines for each role
        """
        # Expected permissions for each role
        self.baseline_permissions = {
            'end_user': {
                'read:own_conversations',
                'create:conversation',
                'query:public_knowledge_base'
            },
            'analyst': {
                'read:own_conversations',
                'read:all_conversations',
                'read:usage_metrics',
                'query:internal_knowledge_base'
            },
            'developer': {
                'read:model_logs',
                'update:application_config',
                'deploy:model_version'
            },
            'admin': {
                'admin:all_resources',
                'manage:user_roles',
                'access:audit_logs'
            }
        }
    
    def detect_drift(self, user_id: str) -> List[Dict]:
        """
        Detect permission drift for user
        
        Returns list of anomalies detected
        """
        anomalies = []
        
        # Get user's current permissions
        current_permissions = set(get_user_permissions(user_id))
        
        # Get user's assigned roles
        user_roles = get_user_roles(user_id)
        
        # Calculate expected permissions based on roles
        expected_permissions = set()
        for role in user_roles:
            expected_permissions.update(
                self.baseline_permissions.get(role, set())
            )
        
        # Identify extra permissions (drift)
        extra_permissions = current_permissions - expected_permissions
        
        if extra_permissions:
            anomalies.append({
                'type': 'permission_drift',
                'user_id': user_id,
                'extra_permissions': list(extra_permissions),
                'severity': 'high' if self.is_high_risk_drift(extra_permissions) else 'medium'
            })
        
        # Identify missing permissions (role not applied correctly)
        missing_permissions = expected_permissions - current_permissions
        
        if missing_permissions:
            anomalies.append({
                'type': 'missing_expected_permissions',
                'user_id': user_id,
                'missing_permissions': list(missing_permissions),
                'severity': 'low'
            })
        
        return anomalies
    
    def is_high_risk_drift(self, extra_permissions: set) -> bool:
        """
        Check if drift involves high-risk permissions
        """
        high_risk_keywords = ['admin:', 'delete:', 'all_tenants', 'confidential']
        
        return any(
            any(keyword in perm for keyword in high_risk_keywords)
            for perm in extra_permissions
        )
    
    def monitor_permission_changes(self):
        """
        Continuous monitoring for permission changes
        
        Run periodically (e.g., hourly) to detect new drifts
        """
        all_users = get_all_active_users()
        
        for user_id in all_users:
            anomalies = self.detect_drift(user_id)
            
            for anomaly in anomalies:
                # Log anomaly
                log_audit_event("permission_drift_detected", anomaly)
                
                # Alert security team for high-severity drifts
                if anomaly['severity'] == 'high':
                    alert_security_team(
                        f"High-risk permission drift detected for user {user_id}",
                        details=anomaly
                    )
```

### RBAC Policy Compliance Scanning

**Automated Policy Validation:**

```python
class RBACPolicyScanner:
    """
    Scan RBAC configuration for policy violations
    
    Validates access control policies match security requirements
    """
    
    def scan_policy_violations(self) -> List[Dict]:
        """
        Scan for RBAC policy violations
        
        Returns list of violations found
        """
        violations = []
        
        # Check 1: No users should have direct permissions (role-based only)
        violations.extend(self.check_direct_permission_grants())
        
        # Check 2: Admin role should have minimal membership
        violations.extend(self.check_excessive_admin_assignments())
        
        # Check 3: Least privilege violations
        violations.extend(self.check_least_privilege_violations())
        
        # Check 4: Tenant isolation violations
        violations.extend(self.check_tenant_isolation_violations())
        
        # Check 5: Orphaned permissions (assigned but no role)
        violations.extend(self.check_orphaned_permissions())
        
        return violations
    
    def check_direct_permission_grants(self) -> List[Dict]:
        """
        Detect users with direct permission grants (not via roles)
        """
        violations = []
        
        all_users = get_all_active_users()
        
        for user_id in all_users:
            direct_permissions = get_direct_user_permissions(user_id)
            
            if direct_permissions:
                violations.append({
                    'type': 'direct_permission_grant',
                    'user_id': user_id,
                    'permissions': direct_permissions,
                    'severity': 'medium',
                    'remediation': 'Assign permissions via roles, not directly'
                })
        
        return violations
    
    def check_excessive_admin_assignments(self) -> List[Dict]:
        """
        Detect excessive admin role assignments
        """
        violations = []
        
        admins = get_users_with_role('admin')
        
        # Define threshold (e.g., max 5% of users should be admins)
        total_users = len(get_all_active_users())
        max_admins = max(3, int(total_users * 0.05))
        
        if len(admins) > max_admins:
            violations.append({
                'type': 'excessive_admin_assignments',
                'admin_count': len(admins),
                'threshold': max_admins,
                'severity': 'high',
                'remediation': 'Review admin assignments and reduce to essential personnel'
            })
        
        return violations
    
    def check_tenant_isolation_violations(self) -> List[Dict]:
        """
        Detect cross-tenant access violations
        """
        violations = []
        
        all_users = get_all_active_users()
        
        for user_id in all_users:
            user_tenant = get_user_tenant(user_id)
            accessible_tenants = get_accessible_tenants(user_id)
            
            # Users should only access their own tenant (except admins)
            if len(accessible_tenants) > 1:
                user_roles = get_user_roles(user_id)
                
                if 'admin' not in user_roles:
                    violations.append({
                        'type': 'tenant_isolation_violation',
                        'user_id': user_id,
                        'user_tenant': user_tenant,
                        'accessible_tenants': accessible_tenants,
                        'severity': 'critical',
                        'remediation': 'Remove cross-tenant access for non-admin users'
                    })
        
        return violations
```

## Limitations & Trade-offs

**Limitations:**

- **Reactive, not preventive:** Audits detect problems after permissions already granted
- **Requires manual review:** Manager certification depends on human judgment
- **Does not prevent all abuse:** Authorized users can still misuse legitimate access
- **Snapshot in time:** Access appropriate at review time may become excessive later

**Trade-offs:**

- **Security vs. Productivity:** Frequent reviews create administrative overhead
- **Automation vs. Accuracy:** Automated drift detection may have false positives
- **Granularity vs. Scalability:** Fine-grained audits don't scale to large user populations
- **Compliance vs. Agility:** Strict certification requirements slow down access provisioning

**Bypass Scenarios:**

- **Timing attacks:** Attackers exploit access between review cycles
- **Certification fraud:** Managers rubber-stamp approvals without review
- **Exception abuse:** Legitimate exceptions accumulate and never removed
- **Drift post-review:** Permissions drift immediately after certification

## Testing & Validation

**Functional Testing:**

1. **Review Completeness:** Verify all users included in access review campaigns
2. **Certification Workflow:** Test manager approval/revocation workflows
3. **Remediation Execution:** Validate uncertified access is automatically revoked
4. **Drift Detection Accuracy:** Verify anomaly detection correctly identifies drifts

**Security Testing:**

1. **Orphaned Account Detection:** Test that orphaned accounts are identified and removed
2. **Excessive Permission Detection:** Validate detection of users with excessive grants
3. **Policy Violation Scanning:** Test compliance scanner finds violations
4. **Tenant Isolation Validation:** Verify cross-tenant access is flagged

**Audit Testing:**

- Verify complete audit trail for all permission changes
- Test that certification decisions are immutable
- Validate remediation actions are logged

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Regular permission reviews: Conduct quarterly access audits to identify and revoke unnecessary permissions (prevent permission creep)."
> — Derived from weak access segmentation mitigation recommendations

> "Permission drift monitoring: Track changes to user permissions over time and alert on unexpected escalations."
> — Derived from weak access segmentation detective controls

> "RBAC policy compliance scans: Regularly validate that actual permissions match intended access control policies."
> — Derived from weak access segmentation detective controls

## Related

- **Supports:** [[mitigations/access-segmentation-and-rbac]], [[mitigations/least-privilege-implementation]]
- **Combines with:** [[mitigations/anomaly-detection-architecture]] (behavioral anomaly detection)
- **Requires:** [[mitigations/telemetry-and-monitoring-architecture]] (access logging for audits)
