---
id: weak-access-segmentation
title: "Weak Access Segmentation"
sidebar_label: "Weak Access Segmentation"
description: Insufficient RBAC, ABAC, or tenant isolation allows unauthorized access to AI resources.
---

# Weak Access Segmentation

## Summary

Weak access segmentation occurs when LLM systems lack adequate role-based access control (RBAC), attribute-based access control (ABAC), or tenant isolation, allowing users to access resources and perform actions beyond their intended permissions. This vulnerability manifests as overly permissive default permissions, missing granular controls, violations of least privilege principles, and absent segregation of duties for critical operations. Attackers exploit these gaps to escalate privileges vertically (e.g., Reader role gaining Admin capabilities) or horizontally (accessing other users' or tenants' data). In multi-tenant LLM deployments, weak access segmentation can enable cross-tenant data breaches, exposing sensitive information across organizational boundaries and creating severe compliance violations.

## Threat Scenario

A SaaS company deploys a multi-tenant LLM-powered analytics platform with three user roles: Reader (view reports only), Analyst (query data and generate reports), and Admin (configure models and manage users). The access control implementation has critical flaws: permissions are checked at the UI layer but not enforced at the API layer, and tenant isolation relies on application logic rather than database-level enforcement. An Analyst user from Company A discovers that by directly calling API endpoints, they can bypass role checks and execute Admin-level operations such as modifying model parameters or viewing system logs. Furthermore, by manipulating tenant IDs in API requests, they can access Company B's proprietary business data stored in the same LLM knowledge base. This horizontal privilege escalation enables cross-tenant data exfiltration. The attacker downloads sensitive competitive intelligence from multiple customer tenants, exposing trade secrets, financial data, and strategic plans. The breach goes undetected for three months until Company B notices their confidential data appearing in a competitor's marketing materials.

## Preconditions

- Lack of granular permission definitions (permissions are broad and coarse-grained rather than specific to actions and resources)
- No RBAC implementation or poorly configured roles (all users have similar privileges, or roles are not properly enforced)
- Least privilege principle not applied (users granted more permissions than required for their job functions)
- Missing segregation of duties for critical operations (e.g., single user can both deploy model changes and approve those changes)
- Access control checks only implemented at UI layer, not enforced at API or database layers
- Weak or absent tenant isolation in multi-tenant deployments (application-level isolation instead of database-level)
- No regular access audits or permission drift monitoring (overly permissive grants accumulate over time)

## Abuse Path

1. **Permission Enumeration**: Attacker (authenticated as low-privilege user) systematically probes API endpoints to discover which operations are accessible. They identify endpoints that lack proper authorization checks or rely solely on UI-layer restrictions.

2. **Identify Escalation Opportunities**: Attacker analyzes API request/response patterns to identify parameters that control access (e.g., role IDs, tenant IDs, user IDs). They test for injection points where manipulating these parameters might bypass access controls.

3. **Horizontal Privilege Escalation**: Attacker attempts to access resources belonging to other users at the same privilege level by modifying user IDs or session tokens in API requests. In multi-tenant systems, they manipulate tenant identifiers to cross organizational boundaries.

4. **Vertical Privilege Escalation**: Attacker attempts to elevate from Reader to Analyst or Admin roles by modifying role parameters, forging authentication tokens, or exploiting missing authorization checks on privileged API endpoints (e.g., model configuration, user management, system settings).

5. **Abuse Elevated Privileges**: Once escalation is achieved, attacker performs unauthorized actions such as:
   - Modifying model parameters or system configuration
   - Accessing sensitive data across all tenants
   - Creating backdoor admin accounts for persistence
   - Exfiltrating proprietary knowledge base content
   - Deleting audit logs to cover tracks

## Impact

**Business Impact:**
- Unauthorized access to sensitive customer data across organizational boundaries (in multi-tenant deployments)
- Compliance violations (GDPR Article 32 requires appropriate access controls; failure can result in fines up to 4% of global revenue)
- Insider threat enablement (malicious employees can abuse overly permissive access)
- Data breaches exposing confidential business information, trade secrets, or personally identifiable information (PII)
- Reputational damage and loss of customer trust when cross-tenant data leakage is discovered
- Contract violations if customer data is accessed by unauthorized parties (SLA breaches, liability)

**Technical Impact:**
- Complete bypass of intended access control model (horizontal and vertical privilege escalation)
- Cross-tenant data leakage in multi-tenant architectures
- Unauthorized modification of model parameters, configurations, or system settings
- Ability to create persistence mechanisms (backdoor accounts, elevated permissions)
- Potential for complete system compromise if Admin-level access is achieved

**Severity:** **High** (enables data breaches, compliance violations, and system compromise)

## Detection Signals

- Access attempts to resources or API endpoints beyond user's assigned role permissions (e.g., Reader attempting Admin-level operations)
- Unusual API endpoint access patterns (user suddenly calling previously unused endpoints)
- Cross-tenant data queries (user from Tenant A accessing data belonging to Tenant B)
- Permission changes or role modifications outside normal workflows
- Successful API calls with manipulated tenant IDs, user IDs, or role parameters
- Anomalous user behavior (user querying data outside their normal domain or department)
- Access logs showing privilege escalation indicators (e.g., role parameter changes in session tokens)
- Failed authorization attempts followed by successful access (indicating bypass discovery)

## Testing Approach

**Manual Testing:**
- **Horizontal Privilege Escalation Testing**: As User A, attempt to access User B's data by modifying user IDs, session tokens, or tenant identifiers in API requests
- **Vertical Privilege Escalation Testing**: As Reader role, attempt to call Admin-level API endpoints (model configuration, user management) to test for missing authorization checks
- **Cross-Tenant Access Testing** (multi-tenant systems): Attempt to query or modify data belonging to other tenant organizations by manipulating tenant parameters
- **API vs. UI Authorization Mismatch**: Test whether API endpoints enforce same authorization checks as UI (bypass UI restrictions by calling APIs directly)
- **Segregation of Duties Testing**: Attempt to perform critical operations (e.g., deploy model changes) without multi-party approval
- **Permission Enumeration**: Systematically probe all API endpoints to map which operations are accessible to each role

**Automated Testing:**
- **RBAC Policy Analyzers**: Tools that parse access control configurations and identify overly permissive grants or missing restrictions
- **Permission Enumeration Tools**: Automated scanners that test all API endpoints with different role credentials to build permission matrix
- **Access Matrix Validators**: Tools that verify access control matrices match intended design (e.g., Reader should only have read permissions)
- **Tenant Isolation Testers**: Automated tools that test for cross-tenant data leakage in multi-tenant deployments

## Evidence to Capture

- [ ] Successful privilege escalation demonstrations (screenshots or logs showing Reader accessing Admin endpoints)
- [ ] Access logs showing unauthorized resource access (cross-user or cross-tenant data queries)
- [ ] API request/response pairs demonstrating authorization bypass (manipulated tenant IDs resulting in successful data access)
- [ ] Permission configuration files showing overly broad grants or missing restrictions
- [ ] RBAC policy analysis report identifying gaps (missing roles, coarse-grained permissions, least privilege violations)
- [ ] Evidence of API vs. UI authorization mismatches (UI blocks operation but direct API call succeeds)
- [ ] Cross-tenant access proof (User from Tenant A successfully querying Tenant B's data)
- [ ] Segregation of duties failures (single user able to perform conflicting critical operations)

## Mitigations

**Preventive Controls:**
- **Implement RBAC with Defined Roles**: Create clearly defined roles (e.g., Reader, Analyst, Editor, Admin) with specific permissions mapped to job functions
- **Apply Least Privilege Principle**: Grant users minimum permissions necessary for their role; start with minimal access and add permissions as needed
- **Enforce Granular Permissions**: Define fine-grained permissions covering all LLM actions (query model, view logs, modify parameters, access specific data domains, invoke tools)
- **Implement ABAC for Context-Aware Decisions**: Use attribute-based access control to make dynamic decisions based on user attributes, resource properties, and environmental conditions (time, location, risk level)
- **Segregation of Duties**: Require multi-party approval for critical operations (e.g., model deployment requires both developer submission and security approval)
- **Enforce Authorization at All Layers**: Implement access checks at API, service, and database layers—never rely solely on UI-layer restrictions
- **Database-Level Tenant Isolation**: In multi-tenant systems, enforce isolation at database level (separate schemas, row-level security policies) rather than relying on application logic
- **Regular Permission Reviews**: Conduct quarterly access audits to identify and revoke unnecessary permissions (prevent permission creep)

**Detective Controls:**
- **Access Audit Logging**: Log all access attempts (successful and failed) with user context, resource accessed, and action performed
- **Permission Drift Monitoring**: Track changes to user permissions over time and alert on unexpected escalations
- **Anomalous Access Pattern Detection**: Use behavioral analytics to detect users accessing resources outside their normal patterns
- **Cross-Tenant Access Monitoring**: In multi-tenant systems, alert on any queries crossing tenant boundaries
- **Failed Authorization Alerts**: Monitor for repeated failed authorization attempts (may indicate privilege escalation probing)
- **RBAC Policy Compliance Scans**: Regularly validate that actual permissions match intended access control policies

**Responsive Controls:**
- **Automated Permission Revocation**: Automatically revoke overly permissive grants detected during audits
- **Account Suspension for Policy Violations**: Suspend accounts exhibiting privilege escalation behavior pending investigation
- **Incident Response Playbooks**: Maintain runbooks for responding to privilege escalation incidents (containment, forensics, remediation)
- **Emergency Access Revocation**: Capability to immediately revoke all access for compromised accounts or during security incidents

## Engagement Applicability

- [x] AI Threat Exposure Review (Deployment/Governance boundary assessment, access control validation)
- [x] Governance & Compliance Deep Dive (validates RBAC implementation against SOC 2, ISO 27001, GDPR requirements)
- [x] Purple Team Workshop (teaches internal teams to test for privilege escalation and implement detective controls)

## Framework References

**MITRE ATLAS:**
- **TODO:** Identify applicable ATLAS techniques

**OWASP LLM Top 10:**
- **TODO:** Map to OWASP LLM issues

**NIST AI RMF:**
- **TODO:** Map to NIST AI RMF

**NIST GenAI Profile:**
- **TODO:** Map to GenAI Profile
