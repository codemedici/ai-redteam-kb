---
title: "Authentication Context Confusion"
tags:
  - type/technique
  - target/agent
  - target/llm-app
  - access/inference
  - access/api
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Authentication Context Confusion

## Summary

Authentication context confusion occurs when an agent or LLM-powered application confuses user identity or authorization context during multi-user or multi-tenant operations, leading to privilege violations and unauthorized access. This vulnerability arises when systems fail to properly bind execution context to the authenticated user throughout the request lifecycle, allowing one user's actions to be executed with another user's permissions or enabling unauthorized access to protected resources. Context confusion attacks exploit gaps in session management, context propagation, and authorization enforcement to bypass access controls.

## Mechanism

Authentication context confusion attacks exploit weaknesses in how systems maintain and validate user identity across the execution chain. The attack manifests in several variants:

### Session Context Leakage

In multi-user systems, user context (identity, permissions, session state) may leak between concurrent requests or sessions if not properly isolated. This occurs when:

- Shared agent instances process requests from multiple users without proper context reset
- Session state persists in memory or caching layers beyond intended scope
- Context propagation mechanisms fail to maintain user boundaries
- Background tasks or asynchronous operations inherit wrong user context

**Example Scenario:**

```
User A submits request → Agent processes with User A context
User B submits request → Agent reuses partial state from User A
→ User B's request executes with User A's permissions or data access
```

### Multi-Tenant Isolation Failures

In multi-tenant LLM applications (e.g., shared chatbots, enterprise AI assistants), failure to enforce tenant boundaries can result in:

- Cross-tenant data leakage (Tenant A sees Tenant B's data)
- Cross-tenant action execution (Tenant A modifies Tenant B's resources)
- Privilege escalation across tenant boundaries

### Context Binding Gaps

Systems that fail to bind execution context to authenticated users throughout the request lifecycle create opportunities for confusion:

- **API-level context bypass:** Attacker manipulates API parameters to impersonate another user
- **Tool execution context loss:** Agent tools execute without proper user context binding
- **Multi-turn conversation drift:** Long conversations cause context loss or confusion
- **Async operation context leakage:** Background tasks execute with wrong user context

### Authorization Context Override

Attackers may exploit systems that rely on client-provided or easily manipulated authorization context:

- Request parameters contain `user_id` field that system trusts without validation
- Authorization checks use wrong source of truth for user identity (e.g., request body instead of authentication token)
- Context extracted from untrusted sources (cookies, headers, URL parameters)

## Preconditions

- Multi-user or multi-tenant LLM application or agent system
- Shared agent instances processing requests from multiple users
- Weak session management or context isolation mechanisms
- Authorization checks that don't consistently validate user context
- Systems that trust client-provided user identity parameters
- Long-running conversations or sessions where context may drift
- Asynchronous or background processing without explicit context binding

## Impact

**Business Impact:**
- **Data breach:** Unauthorized access to sensitive customer or business data across user/tenant boundaries
- **Compliance violations:** GDPR, HIPAA, or other regulatory violations due to unauthorized data access
- **Reputational damage:** Loss of customer trust when context confusion exposes private information
- **Legal liability:** Lawsuits from affected users whose data was exposed or manipulated
- **Service disruption:** Context confusion leading to incorrect operations or data corruption

**Technical Impact:**
- User A can access User B's private data (messages, documents, search results)
- User A can perform actions as User B (file modifications, API calls, transactions)
- Low-privilege user gains high-privilege context through confusion
- Cross-tenant data leakage in multi-tenant deployments
- Session hijacking or impersonation without credential theft
- Incorrect authorization decisions based on confused context

## Detection

**Observable Indicators:**

- User accessing resources not assigned to their tenant or organization
- API requests with mismatched authentication token and request body user identifiers
- Audit logs showing actions attributed to wrong user
- Cross-tenant data access patterns in database queries
- Session state persisting longer than expected session lifetime
- Background tasks executing with incorrect user context
- Authorization checks failing or bypassing unexpectedly

**Monitoring Signals:**

- Anomaly detection: User accessing resources they've never accessed before
- Context correlation: Requests with authentication token User A but actions affecting User B's resources
- Tenant boundary violations: Database queries crossing tenant isolation boundaries
- Session lifecycle anomalies: Sessions persisting or switching contexts mid-flow
- Permission escalation: User suddenly performing actions beyond normal privilege level

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Mitigations

| ID | Name | Description |
|----|------|-------------|
| AML.M0016 | [[mitigations/user-context-binding]] | Bind tool execution to originating user's security context throughout request lifecycle |
| AML.M0004 | [[mitigations/least-privilege-implementation]] | Enforce minimum required permissions at tool level, preventing escalation through context confusion |
| | [[mitigations/access-segmentation-and-rbac]] | Enforce role-based access control and tenant isolation to prevent cross-context access |

## Sources

> "Context-aware authorization: Check session context, not just API fields. Validate user role against tool privilege level before execution."
> — [[sources/bibliography#Securing AI Agents]], p. 210-211
