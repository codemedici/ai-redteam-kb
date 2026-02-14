---
title: "Access Segmentation and RBAC"
tags:
  - type/mitigation
  - target/llm-app
  - target/agent
  - target/rag-system
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Access Segmentation and RBAC

## Summary

Access segmentation and role-based access control (RBAC) enforce isolation boundaries and permission models across LLM systems, agents, and supporting infrastructure. This control prevents unauthorized access by restricting users, services, and system components to the minimum resources necessary for their function. Proper segmentation creates defense-in-depth layers that prevent lateral movement, contain breaches, and enforce the principle of least privilege across network, application, and data layers. RBAC complements segmentation by defining granular permission models that map users and services to authorized actions and resources.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/ai-infrastructure-attacks]] | Network segmentation isolates AI infrastructure from general network to limit attack surface |
| | [[techniques/api-authentication-bypass]] | RBAC enforces authentication requirements before API access |
| | [[techniques/auth-context-confusion]] | Prevents context confusion by enforcing strict user-resource isolation and tenant boundaries |
| | [[techniques/insecure-model-gateway-config]] | Proper segmentation isolates model gateways from untrusted networks |
| | [[techniques/model-extraction]] | Access controls limit who can query models at high volume |
| | [[techniques/model-extraction-llm]] | Rate limiting and RBAC prevent unauthorized model extraction attempts |
| | [[techniques/model-tampering]] | Segmentation isolates model storage from unauthorized modification |
| | [[techniques/privacy-attacks-beyond-membership-inference]] | Data access controls limit exposure of sensitive training data |
| | [[techniques/prompt-log-data-leakage]] | RBAC restricts access to prompt logs containing sensitive information |
| | [[techniques/secrets-in-prompts-and-logs]] | Access segmentation prevents unauthorized access to logs with embedded secrets |
| | [[techniques/sensitive-info-disclosure]] | Strict data access controls prevent unauthorized information disclosure |
| | [[techniques/unauthorized-knowledge-disclosure]] | Enforces tenant isolation and role-based access control at knowledge base and vector search layers, preventing cross-tenant data leakage |
| | [[techniques/weak-access-segmentation]] | Directly addresses inadequate segmentation and access control |
| | [[techniques/react-security-risks]] | Robust access controls and data separation minimize ReAct agent exposure to APIs and protected information |

## Implementation

### Network Segmentation Architecture

**Layered Network Zones:**

1. **Public Zone:**
   - User-facing interfaces (chatbots, APIs)
   - Web application firewalls (WAFs)
   - DDoS protection and rate limiting
   - No direct access to internal systems

2. **Application Zone:**
   - Agent orchestration services
   - LLM inference APIs
   - Application logic and business rules
   - Restricted access from public zone via API gateway

3. **Data Zone:**
   - Vector databases (RAG systems)
   - Knowledge bases and document stores
   - Training data repositories
   - No direct external access

4. **Model Zone:**
   - Model storage and versioning
   - Fine-tuning infrastructure
   - Model registry
   - Isolated from application and data zones

5. **Management Zone:**
   - Monitoring and logging systems
   - Administrative interfaces
   - Security tooling
   - Restricted to authorized personnel only

**Segmentation Rules:**

- **Default deny:** All traffic blocked unless explicitly allowed
- **Zone-to-zone restrictions:** Define allowed communication paths between zones
- **Protocol restrictions:** Limit protocols to business-necessary (e.g., HTTPS only for API calls)
- **Egress filtering:** Prevent unauthorized outbound connections from sensitive zones

### Role-Based Access Control Implementation

**Permission Model:**

```yaml
# Example RBAC policy for LLM application

roles:
  end_user:
    description: "Standard application user"
    permissions:
      - read:own_conversations
      - create:conversation
      - query:public_knowledge_base
    resource_scope: "user-owned"
  
  analyst:
    description: "Business analyst with broader access"
    permissions:
      - read:all_conversations
      - read:usage_metrics
      - query:internal_knowledge_base
    resource_scope: "organization"
  
  developer:
    description: "Application developer"
    permissions:
      - read:model_logs
      - update:application_config
      - deploy:model_version
    resource_scope: "development_environment"
  
  admin:
    description: "System administrator"
    permissions:
      - admin:all_resources
      - manage:user_roles
      - access:audit_logs
    resource_scope: "global"

# Multi-tenant isolation
tenants:
  tenant_a:
    isolation_level: "strict"
    allowed_roles: [end_user, analyst]
    data_boundary: "tenant_a_namespace"
  
  tenant_b:
    isolation_level: "strict"
    allowed_roles: [end_user, analyst, developer]
    data_boundary: "tenant_b_namespace"
```

**RBAC Enforcement Points:**

1. **API Gateway:**
   - Validate authentication token
   - Extract user roles from claims
   - Enforce route-level permissions
   - Reject unauthorized requests before processing

2. **Application Layer:**
   - Context-aware authorization checks
   - Resource-level permission validation
   - Tenant boundary enforcement
   - Action-level permission checks

3. **Data Access Layer:**
   - Row-level security in databases
   - Document-level permissions in knowledge bases
   - Query result filtering based on user permissions
   - Tenant data isolation

**Implementation Example:**

```python
from functools import wraps
from flask import request, abort

# RBAC decorator for API endpoints
def require_permission(required_permission):
    """
    Enforce permission check before endpoint execution
    
    Args:
        required_permission: Permission string (e.g., "read:conversations")
    """
    def decorator(f):
        @wraps(f)
        def wrapped(*args, **kwargs):
            # Extract user context from authentication token
            user_context = extract_user_context(request.headers.get("Authorization"))
            
            # Check if user has required permission
            if not user_context.has_permission(required_permission):
                log_security_event("rbac_permission_denied", {
                    "user_id": user_context.user_id,
                    "required_permission": required_permission,
                    "user_permissions": user_context.permissions
                })
                abort(403, f"Permission denied: {required_permission}")
            
            # Inject user context for resource-level checks
            kwargs['user_context'] = user_context
            return f(*args, **kwargs)
        return wrapped
    return decorator

# Tenant isolation decorator
def require_tenant_context():
    """
    Enforce tenant boundary isolation
    """
    def decorator(f):
        @wraps(f)
        def wrapped(*args, **kwargs):
            user_context = extract_user_context(request.headers.get("Authorization"))
            
            # Extract tenant from user context
            tenant_id = user_context.tenant_id
            
            if not tenant_id:
                abort(400, "Missing tenant context")
            
            # Inject tenant isolation filter
            kwargs['tenant_id'] = tenant_id
            kwargs['user_context'] = user_context
            
            return f(*args, **kwargs)
        return wrapped
    return decorator

# Example protected endpoint
@app.route("/api/conversations", methods=["GET"])
@require_tenant_context()
@require_permission("read:conversations")
def list_conversations(user_context, tenant_id):
    """
    List conversations with tenant and permission isolation
    
    ✅ Tenant boundary enforced
    ✅ RBAC permission validated
    ✅ Resource-level filtering applied
    """
    # Query only conversations in user's tenant
    conversations = db.query(Conversation).filter(
        Conversation.tenant_id == tenant_id
    )
    
    # Further filter based on user role
    if not user_context.has_permission("read:all_conversations"):
        # Standard users see only their own conversations
        conversations = conversations.filter(
            Conversation.user_id == user_context.user_id
        )
    
    return jsonify([c.to_dict() for c in conversations.all()])
```

### Data Access Segmentation

**Knowledge Base Access Control:**

- **Document-level permissions:** Tag documents with required permission levels
- **Dynamic filtering:** Filter search results based on user permissions
- **Metadata-based access control:** Use document metadata to enforce access rules
- **Tenant isolation:** Separate vector stores or namespaces per tenant

**Database Row-Level Security:**

```sql
-- PostgreSQL row-level security policy example
-- Ensure users can only access their own tenant's data

CREATE POLICY tenant_isolation_policy ON conversations
    FOR ALL
    TO application_role
    USING (tenant_id = current_setting('app.current_tenant')::uuid);

-- Enable row-level security
ALTER TABLE conversations ENABLE ROW LEVEL SECURITY;

-- Set tenant context before queries
SET app.current_tenant = '<tenant_uuid>';
```

### Infrastructure Access Controls

**Model Storage Isolation:**

- **Separate storage accounts** per environment (dev, staging, prod)
- **IAM policies** restricting model access to authorized services only
- **Encryption at rest** with tenant-specific keys where applicable
- **Audit logging** for all model access attempts

**Service-to-Service Authentication:**

- **Mutual TLS (mTLS)** for service communication
- **Service accounts** with minimal required permissions
- **Token-based authentication** with short-lived credentials
- **Zero-trust networking:** Verify every service request

## Limitations & Trade-offs

**Limitations:**

- **Does not prevent authorized misuse:** Users with legitimate access can still abuse permissions
- **Complexity overhead:** Fine-grained RBAC increases administrative burden
- **Performance impact:** Additional permission checks add latency to requests
- **Cannot prevent all lateral movement:** Sophisticated attackers may find paths through segmentation
- **Requires accurate role modeling:** Effectiveness depends on correctly defining roles and permissions

**Trade-offs:**

- **Security vs. Usability:** Strict segmentation can hinder collaboration and data sharing
- **Granularity vs. Complexity:** Fine-grained controls improve security but increase management overhead
- **Centralized vs. Distributed:** Centralized RBAC improves consistency but creates dependencies
- **Static vs. Dynamic:** Static roles are simpler but less flexible than dynamic, context-aware permissions

**Bypass Scenarios:**

- **Privilege escalation:** Attackers gain higher-privilege role through social engineering or exploitation
- **Misconfigured policies:** Overly permissive rules create gaps in segmentation
- **Credential theft:** Stolen credentials grant access within segmentation boundaries
- **Application vulnerabilities:** Code-level flaws may bypass RBAC checks

## Testing & Validation

**Functional Testing:**

1. **Permission Enforcement:** Verify users can only access resources matching their role
2. **Tenant Isolation:** Confirm tenants cannot access other tenants' data
3. **Network Segmentation:** Validate zone-to-zone communication restrictions
4. **Role Inheritance:** Test that role hierarchies and inheritance work correctly

**Security Testing:**

1. **Privilege Escalation:** Attempt to access higher-privilege resources
2. **Cross-Tenant Access:** Try accessing another tenant's data
3. **Network Traversal:** Attempt unauthorized zone-to-zone connections
4. **Permission Bypass:** Test if application logic can bypass RBAC checks

**Audit & Monitoring:**

- **Access logs:** Monitor who accessed what resources and when
- **Permission changes:** Alert on role or permission modifications
- **Anomalous access patterns:** Detect unusual resource access
- **Failed authorization attempts:** Track and investigate repeated denials

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Access segmentation and role-based access control enforce isolation boundaries across LLM systems. Network segmentation creates defense-in-depth layers that prevent lateral movement and contain breaches."
> — Derived from security best practices for LLM application deployment

## Related

- **Combines with:** [[mitigations/user-context-binding]], [[mitigations/least-privilege-implementation]]
- **Supports:** [[mitigations/anomaly-detection-architecture]], [[mitigations/incident-response-procedures]]
