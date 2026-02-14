---
title: "Attribute-Based Access Control"
tags:
  - type/mitigation
  - target/llm-app
  - target/agent
  - target/rag-system
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Attribute-Based Access Control

## Summary

Attribute-Based Access Control (ABAC) extends traditional role-based access control by making dynamic authorization decisions based on user attributes, resource properties, environmental conditions, and contextual factors. Unlike static RBAC where permissions are fixed to roles, ABAC evaluates policies that consider multiple dimensions—including time of day, location, device security posture, data sensitivity classification, and risk level—enabling fine-grained, context-aware access decisions for LLM systems and agentic workflows. ABAC is particularly valuable in multi-tenant LLM deployments where access requirements vary based on regulatory jurisdiction, data classification, and operational context.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/weak-access-segmentation]] | Provides dynamic, context-aware access decisions that adapt to risk level and environmental conditions |
| | [[techniques/auth-context-confusion]] | Enforces context-aware authorization that considers user location, time, and device attributes |
| | [[techniques/unauthorized-knowledge-disclosure]] | Applies data classification and sensitivity attributes to control access to sensitive knowledge base content |
| | [[techniques/sensitive-info-disclosure]] | Dynamic access control based on data sensitivity classification and user clearance level |

## Implementation

### ABAC Policy Model

**Core Attribute Categories:**

1. **Subject Attributes** (User characteristics):
   - User ID, roles, department, clearance level
   - Authentication method (MFA, SSO, password)
   - User location (IP address, geolocation, country)
   - Device attributes (managed vs. unmanaged, security posture)
   - Risk score (behavioral analytics, anomaly detection)

2. **Resource Attributes** (Data/system properties):
   - Data classification (public, internal, confidential, restricted)
   - Tenant ID, data owner, department
   - Sensitivity tags (PII, financial, health, legal)
   - Model type (public API, internal fine-tuned, sensitive domain model)
   - Document metadata (creation date, access history, retention policy)

3. **Action Attributes** (Operations):
   - Query model, read data, modify configuration
   - Export data, deploy model, access logs
   - Invoke privileged tool, cross-tenant operation

4. **Environmental Attributes** (Contextual factors):
   - Time of day, day of week
   - Network zone (corporate, VPN, public internet)
   - Geographic location
   - Current system risk level (incident response mode, elevated threat)
   - Compliance requirement (GDPR jurisdiction, HIPAA applicability)

### Policy Definition Language

**Example ABAC Policy (XACML-style):**

```xml
<Policy PolicyId="HighValueDataAccess">
  <Description>
    Restrict access to confidential data to authorized users
    during business hours from corporate network only
  </Description>
  
  <Target>
    <Resource>
      <Match>
        <AttributeValue>confidential</AttributeValue>
        <ResourceAttribute>data_classification</ResourceAttribute>
      </Match>
    </Resource>
  </Target>
  
  <Rule RuleId="RequireCorporateNetworkAndBusinessHours" Effect="Permit">
    <Condition>
      <And>
        <!-- User must have required clearance -->
        <Match>
          <AttributeValue>confidential_clearance</AttributeValue>
          <SubjectAttribute>clearance_level</SubjectAttribute>
        </Match>
        
        <!-- Access during business hours only -->
        <Function name="time-in-range">
          <EnvironmentAttribute>current_time</EnvironmentAttribute>
          <AttributeValue>09:00:00</AttributeValue>
          <AttributeValue>17:00:00</AttributeValue>
        </Function>
        
        <!-- Access from corporate network or VPN -->
        <Or>
          <Match>
            <AttributeValue>corporate</AttributeValue>
            <EnvironmentAttribute>network_zone</EnvironmentAttribute>
          </Match>
          <Match>
            <AttributeValue>vpn</AttributeValue>
            <EnvironmentAttribute>network_zone</EnvironmentAttribute>
          </Match>
        </Or>
        
        <!-- Device must be managed -->
        <Match>
          <AttributeValue>managed</AttributeValue>
          <SubjectAttribute>device_type</SubjectAttribute>
        </Match>
      </And>
    </Condition>
  </Rule>
  
  <Rule RuleId="DenyByDefault" Effect="Deny">
    <!-- If conditions not met, deny access -->
  </Rule>
</Policy>
```

### Python Implementation Example

**ABAC Policy Engine:**

```python
from typing import Dict, Any, List
from datetime import datetime, time
import ipaddress

class ABACPolicy:
    """
    Attribute-Based Access Control policy engine
    
    Evaluates access decisions based on user, resource,
    action, and environmental attributes
    """
    
    def __init__(self):
        self.policies = []
    
    def evaluate(
        self,
        subject_attrs: Dict[str, Any],
        resource_attrs: Dict[str, Any],
        action: str,
        environment_attrs: Dict[str, Any]
    ) -> tuple[bool, str]:
        """
        Evaluate access decision
        
        Returns: (authorized: bool, reason: str)
        """
        # Combine all attributes for policy evaluation
        context = {
            'subject': subject_attrs,
            'resource': resource_attrs,
            'action': action,
            'environment': environment_attrs
        }
        
        # Evaluate each policy in order
        for policy in self.policies:
            if policy.applies_to(context):
                decision, reason = policy.evaluate(context)
                
                # Log policy evaluation
                log_abac_decision(
                    policy_id=policy.id,
                    decision=decision,
                    reason=reason,
                    context=context
                )
                
                return decision, reason
        
        # Default deny
        return False, "No matching policy found (default deny)"

class ConfidentialDataAccessPolicy:
    """
    Example ABAC policy: Restrict confidential data access
    """
    
    id = "confidential_data_access"
    
    BUSINESS_HOURS_START = time(9, 0)
    BUSINESS_HOURS_END = time(17, 0)
    
    CORPORATE_NETWORKS = [
        ipaddress.ip_network('10.0.0.0/8'),
        ipaddress.ip_network('172.16.0.0/12')
    ]
    
    def applies_to(self, context: Dict) -> bool:
        """Check if policy applies to this context"""
        resource_classification = context['resource'].get('classification')
        return resource_classification in ['confidential', 'restricted']
    
    def evaluate(self, context: Dict) -> tuple[bool, str]:
        """
        Evaluate policy conditions
        
        Required conditions:
        - User has confidential clearance
        - Access during business hours
        - Access from corporate network or VPN
        - Device is managed
        - User risk score below threshold
        """
        subject = context['subject']
        resource = context['resource']
        environment = context['environment']
        
        # Check clearance level
        if not self._has_clearance(subject, resource):
            return False, "User lacks required clearance level"
        
        # Check business hours
        if not self._is_business_hours(environment):
            return False, "Access to confidential data restricted to business hours"
        
        # Check network location
        if not self._is_trusted_network(environment):
            return False, "Access to confidential data requires corporate network or VPN"
        
        # Check device management
        if not self._is_managed_device(subject):
            return False, "Access to confidential data requires managed device"
        
        # Check risk score
        if not self._acceptable_risk_score(subject):
            return False, "User risk score exceeds threshold for confidential data access"
        
        # All conditions met
        return True, "Access granted: All ABAC policy conditions satisfied"
    
    def _has_clearance(self, subject: Dict, resource: Dict) -> bool:
        """Check if user has required clearance level"""
        user_clearance = subject.get('clearance_level', 'none')
        required_clearance = resource.get('required_clearance', 'confidential')
        
        clearance_hierarchy = {
            'none': 0,
            'public': 1,
            'internal': 2,
            'confidential': 3,
            'restricted': 4
        }
        
        return clearance_hierarchy.get(user_clearance, 0) >= \
               clearance_hierarchy.get(required_clearance, 0)
    
    def _is_business_hours(self, environment: Dict) -> bool:
        """Check if current time is within business hours"""
        current_time = environment.get('current_time', datetime.now().time())
        
        return self.BUSINESS_HOURS_START <= current_time <= self.BUSINESS_HOURS_END
    
    def _is_trusted_network(self, environment: Dict) -> bool:
        """Check if access is from trusted network"""
        network_zone = environment.get('network_zone')
        
        # VPN or corporate network is trusted
        if network_zone in ['corporate', 'vpn']:
            return True
        
        # Check if IP is in corporate range
        source_ip = environment.get('source_ip')
        if source_ip:
            user_ip = ipaddress.ip_address(source_ip)
            return any(user_ip in network for network in self.CORPORATE_NETWORKS)
        
        return False
    
    def _is_managed_device(self, subject: Dict) -> bool:
        """Check if device is managed by organization"""
        return subject.get('device_type') == 'managed'
    
    def _acceptable_risk_score(self, subject: Dict) -> bool:
        """Check if user risk score is acceptable"""
        risk_score = subject.get('risk_score', 0)
        threshold = 75  # Risk score threshold for confidential data
        
        return risk_score < threshold

# Usage example
def check_knowledge_base_access(user_context, document_id):
    """
    Check if user can access a knowledge base document
    using ABAC policies
    """
    # Extract attributes
    subject_attrs = {
        'user_id': user_context.user_id,
        'clearance_level': user_context.clearance_level,
        'device_type': user_context.device_type,
        'risk_score': user_context.risk_score
    }
    
    resource_attrs = {
        'document_id': document_id,
        'classification': get_document_classification(document_id),
        'required_clearance': get_document_required_clearance(document_id),
        'tenant_id': get_document_tenant(document_id)
    }
    
    environment_attrs = {
        'current_time': datetime.now().time(),
        'network_zone': user_context.network_zone,
        'source_ip': user_context.source_ip,
        'geolocation': user_context.geolocation
    }
    
    # Evaluate ABAC policy
    abac_engine = ABACPolicy()
    abac_engine.policies.append(ConfidentialDataAccessPolicy())
    
    authorized, reason = abac_engine.evaluate(
        subject_attrs=subject_attrs,
        resource_attrs=resource_attrs,
        action='read_document',
        environment_attrs=environment_attrs
    )
    
    if not authorized:
        log_security_event("abac_access_denied", {
            "user_id": user_context.user_id,
            "document_id": document_id,
            "reason": reason
        })
        raise PermissionError(reason)
    
    return True
```

### Integration with LLM Systems

**RAG System ABAC Integration:**

```python
def rag_retrieval_with_abac(query: str, user_context):
    """
    Retrieve documents from knowledge base with ABAC filtering
    
    Only returns documents user is authorized to access based on
    ABAC policy evaluation
    """
    # Retrieve candidate documents
    candidate_docs = vector_db.similarity_search(query, k=20)
    
    # Filter based on ABAC policies
    authorized_docs = []
    
    for doc in candidate_docs:
        try:
            # Check ABAC authorization for each document
            check_knowledge_base_access(user_context, doc.id)
            authorized_docs.append(doc)
        except PermissionError as e:
            # User not authorized for this document
            log_filtered_document(user_context.user_id, doc.id, str(e))
            continue
    
    # Return only authorized documents
    return authorized_docs
```

## Limitations & Trade-offs

**Limitations:**

- **Complexity:** ABAC policies are more complex to design and maintain than static RBAC
- **Performance overhead:** Real-time attribute evaluation adds latency to authorization checks
- **Attribute accuracy dependency:** Decisions only as good as attribute data quality
- **Policy conflicts:** Multiple policies may conflict, requiring conflict resolution strategies
- **Does not prevent authorized misuse:** If all conditions are met legitimately, ABAC cannot prevent misuse

**Trade-offs:**

- **Granularity vs. Performance:** Fine-grained attribute checks increase security but slow authorization
- **Flexibility vs. Predictability:** Dynamic policies adapt to context but are less predictable than static roles
- **Security vs. User Experience:** Strict contextual requirements may block legitimate use cases
- **Centralized vs. Distributed:** Centralized policy engine improves consistency but creates bottleneck

**Bypass Scenarios:**

- **Attribute manipulation:** If attackers can forge attributes (IP spoofing, device ID manipulation), policies may fail
- **Time-based attacks:** Attackers may wait for favorable conditions (business hours, low-risk environment)
- **Policy gaps:** Incomplete policies may leave access paths unprotected
- **Privilege escalation in attributes:** If attackers gain higher clearance level, all ABAC checks pass

## Testing & Validation

**Functional Testing:**

1. **Policy Evaluation Correctness:** Verify policies grant access when all conditions met
2. **Conditional Denial:** Test that access denied when any condition fails
3. **Attribute Accuracy:** Validate attributes are correctly extracted and evaluated
4. **Edge Case Handling:** Test boundary conditions (exactly 9:00 AM, IP at network edge)

**Security Testing:**

1. **Attribute Forgery:** Attempt to manipulate attributes to bypass policies
2. **Time-Based Bypass:** Test if access granted outside allowed time windows
3. **Network Spoofing:** Verify IP-based policies cannot be bypassed via spoofing
4. **Policy Gaps:** Scan for resource/action combinations without coverage

**Performance Testing:**

- Measure authorization latency with varying numbers of policies
- Test attribute retrieval performance at scale
- Validate caching strategies don't create security gaps

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Implement ABAC for context-aware decisions: Use attribute-based access control to make dynamic decisions based on user attributes, resource properties, and environmental conditions (time, location, risk level)."
> — Derived from weak access segmentation mitigation recommendations

## Related

- **Builds on:** [[mitigations/access-segmentation-and-rbac]] (RBAC provides foundation, ABAC adds context)
- **Combines with:** [[mitigations/user-context-binding]], [[mitigations/anomaly-detection-architecture]]
- **Supports:** [[mitigations/least-privilege-implementation]] (dynamic least privilege based on context)
