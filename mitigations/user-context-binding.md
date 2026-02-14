---
title: "User Context Binding"
tags:
  - type/mitigation
  - target/agent
  - target/llm-app
  - source/securing-ai-agents
atlas: AML.M0016
maturity: draft
created: 2026-02-11
updated: 2026-02-14
---
# User Context Binding

## Summary

User context binding ensures that agent tool executions are bound to the originating user's security context and permissions, preventing privilege escalation and confused deputy attacks. This control prevents attackers from manipulating API requests to invoke privileged tools by enforcing that authorization checks validate the actual user's permissions, not manipulated parameters in API payloads. By binding tool execution to user identity throughout the request lifecycle, this mitigation prevents authorization bypass attacks where low-privilege users attempt to access high-privilege operations.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0051 | [[techniques/agent-authorization-hijacking]] | Prevents attackers from bypassing authorization by binding tool execution to user's actual security context |
| | [[techniques/auth-context-confusion]] | Prevents context confusion by maintaining user identity throughout execution chain |
| | [[techniques/agentic-ai-security-risks-owasp-aivss]] | Addresses multiple agent security risks related to authorization and access control |
| | [[techniques/weak-access-segmentation]] | Enforces proper access segmentation by binding operations to user context |
| | [[techniques/unauthorized-knowledge-disclosure]] | Binds document retrieval to user's actual security context, preventing privilege escalation in RAG systems |

## Implementation

### Core Principles

**Context-Aware Authorization:**

Authorization decisions must be based on the authenticated user's security context, not on client-controlled parameters in API requests. The agent framework must:

1. **Extract user identity from authentication token** (not from request body)
2. **Validate user permissions against tool requirements** before execution
3. **Maintain user context throughout the execution chain** (agent → tool selection → tool invocation)
4. **Prevent privilege inheritance** from system or agent processes

### Architecture Pattern

**Secure User Context Flow:**

```
User Authentication → Extract User Context → Tool Selection → Permission Validation → Tool Execution
                                ↓                                    ↓
                        User Identity (trusted)         Check User Role vs. Tool Requirements
```

**Vulnerable Pattern (API-Level Privilege Escalation):**

```python
# VULNERABLE: Tool selection controlled by client
@app.route("/api/tasks", methods=["POST"])
def create_task():
    user_auth_token = request.headers.get("Authorization")
    task_payload = request.json
    
    # Attacker controls tool_to_use field
    tool_to_use = task_payload.get("tool_to_use")  # ❌ CLIENT-CONTROLLED
    tool_params = task_payload.get("tool_parameters")
    
    # No authorization check before tool invocation
    result = agent.invoke_tool(tool_to_use, tool_params)  # ❌ NO PERMISSION CHECK
    
    return jsonify({"result": result})
```

**Attack Example:**

```python
# Attacker request: low-privilege user requesting high-privilege tool
malicious_request = {
    "user_id": "eric.jones",
    "task_description": "My mouse isn't working, please run diagnostics.",
    "tool_to_use": "run_admin_powershell",  # ❌ PRIVILEGE ESCALATION ATTEMPT
    "tool_parameters": {
        "script": "New-Item -Path 'C:\\' -Name 'redteam_owned.txt'"
    }
}
```

### Secure Implementation

**Server-Side Tool Selection with Context Binding:**

```python
from functools import wraps
from flask import request, abort, jsonify
import jwt

# Tool ACL: Maps tools to required permissions
TOOL_ACL = {
    "password_reset": ["helpdesk.user"],
    "run_admin_powershell": ["helpdesk.admin"],
    "read_user_data": ["helpdesk.user"],
    "modify_system_config": ["system.admin"]
}

class UserContext:
    """User security context extracted from authentication token"""
    def __init__(self, user_id, roles, permissions):
        self.user_id = user_id
        self.roles = roles
        self.permissions = permissions
    
    def has_permission(self, required_permission):
        """Check if user has required permission"""
        return required_permission in self.permissions
    
    def can_use_tool(self, tool_name):
        """Check if user can use specified tool"""
        required_perms = TOOL_ACL.get(tool_name, [])
        return all(self.has_permission(perm) for perm in required_perms)

def extract_user_context(auth_token):
    """
    Extract user context from authentication token
    
    Args:
        auth_token: Bearer token from Authorization header
    
    Returns:
        UserContext object with user identity and permissions
    """
    try:
        # Decode and validate JWT token
        token = auth_token.replace("Bearer ", "")
        payload = jwt.decode(token, SECRET_KEY, algorithms=["HS256"])
        
        # Extract user information from trusted token payload
        user_id = payload["user_id"]
        roles = payload.get("roles", [])
        permissions = payload.get("permissions", [])
        
        return UserContext(user_id, roles, permissions)
    except jwt.InvalidTokenError:
        abort(401, "Invalid authentication token")

def require_tool_permission(tool_name):
    """
    Decorator to enforce tool permission checks
    
    Validates that user has required permissions before tool invocation
    """
    def decorator(f):
        @wraps(f)
        def wrapped(*args, **kwargs):
            # Extract user context from auth token (trusted source)
            auth_token = request.headers.get("Authorization")
            user_context = extract_user_context(auth_token)
            
            # Check tool permission
            if not user_context.can_use_tool(tool_name):
                log_security_event("unauthorized_tool_access_attempt", {
                    "user_id": user_context.user_id,
                    "tool": tool_name,
                    "user_permissions": user_context.permissions,
                    "required_permissions": TOOL_ACL.get(tool_name, [])
                })
                abort(403, f"User does not have permission to use tool: {tool_name}")
            
            # Inject user context into function
            kwargs['user_context'] = user_context
            return f(*args, **kwargs)
        return wrapped
    return decorator

@app.route("/api/tasks", methods=["POST"])
def create_task():
    """
    Secure task creation endpoint with context binding
    
    ✅ User context extracted from trusted auth token
    ✅ Tool selection performed server-side based on task description
    ✅ Permission validation before tool invocation
    """
    # Extract user context from authentication token (trusted)
    auth_token = request.headers.get("Authorization")
    user_context = extract_user_context(auth_token)
    
    task_payload = request.json
    task_description = task_payload.get("task_description")
    
    # ✅ SERVER-SIDE TOOL SELECTION (not client-controlled)
    selected_tool, tool_params = agent.select_tool_for_task(
        task_description,
        user_context  # Pass user context to tool selection
    )
    
    # ✅ VALIDATE USER PERMISSION FOR SELECTED TOOL
    if not user_context.can_use_tool(selected_tool):
        log_security_event("tool_authorization_failure", {
            "user_id": user_context.user_id,
            "task_description": task_description,
            "selected_tool": selected_tool,
            "denied_reason": "insufficient_permissions"
        })
        abort(403, f"User does not have permission to use required tool: {selected_tool}")
    
    # ✅ EXECUTE TOOL WITH USER CONTEXT BINDING
    result = agent.invoke_tool(
        selected_tool,
        tool_params,
        user_context=user_context  # Bind execution to user context
    )
    
    log_security_event("tool_invocation_success", {
        "user_id": user_context.user_id,
        "tool": selected_tool,
        "authorized": True
    })
    
    return jsonify({"result": result})
```

### Context-Aware Tool Invocation Layer

**Tool execution layer that enforces context binding:**

```python
class ContextBoundToolExecutor:
    """
    Tool executor that enforces user context binding
    
    Ensures all tool invocations respect user permissions
    """
    
    def __init__(self, tool_acl):
        self.tool_acl = tool_acl
    
    def invoke_tool(self, tool_name, parameters, user_context):
        """
        Invoke tool with context validation
        
        Args:
            tool_name: Tool to invoke
            parameters: Tool parameters
            user_context: User security context (must be bound)
        
        Raises:
            PermissionError if user lacks required permissions
        """
        # Validate user context is bound
        if user_context is None:
            raise SecurityError("Tool invocation requires user context binding")
        
        # Check tool permissions
        required_permissions = self.tool_acl.get(tool_name, [])
        
        for perm in required_permissions:
            if not user_context.has_permission(perm):
                raise PermissionError(
                    f"User {user_context.user_id} lacks required permission: {perm}"
                )
        
        # Log authorized execution
        log_security_event("authorized_tool_execution", {
            "user_id": user_context.user_id,
            "tool": tool_name,
            "permissions_checked": required_permissions
        })
        
        # Execute tool with user context
        return self._execute_tool_impl(tool_name, parameters, user_context)
    
    def _execute_tool_impl(self, tool_name, parameters, user_context):
        """
        Internal tool execution implementation
        
        Tool implementations receive user_context to enforce
        data-level permissions (e.g., user can only access their own records)
        """
        tool_impl = get_tool_implementation(tool_name)
        
        # Pass user context to tool for data-level access control
        return tool_impl.execute(parameters, user_context=user_context)
```

### Data-Level Access Control

**Extend context binding to data-level permissions:**

```python
class PasswordResetTool:
    """
    Example tool with data-level access control
    
    Users can only reset passwords for accounts they have access to
    """
    
    def execute(self, parameters, user_context):
        target_user_id = parameters.get("target_user_id")
        
        # Data-level authorization check
        if not self.can_reset_password_for_user(user_context, target_user_id):
            raise PermissionError(
                f"User {user_context.user_id} cannot reset password for {target_user_id}"
            )
        
        # Perform password reset
        new_password = self.generate_temp_password()
        update_user_password(target_user_id, new_password)
        
        return {"status": "success", "temp_password": new_password}
    
    def can_reset_password_for_user(self, user_context, target_user_id):
        """
        Check if user can reset target's password
        
        Rules:
        - User can reset their own password
        - Helpdesk admins can reset any password
        - Helpdesk users can reset passwords for their assigned customers
        """
        # Self-service
        if user_context.user_id == target_user_id:
            return True
        
        # Admin override
        if user_context.has_permission("helpdesk.admin"):
            return True
        
        # Check if target is assigned to this helpdesk user
        if user_context.has_permission("helpdesk.user"):
            assigned_customers = get_assigned_customers(user_context.user_id)
            return target_user_id in assigned_customers
        
        return False
```

## Limitations & Trade-offs

**Limitations:**

- **Does not prevent authorized misuse:** If a user has legitimate permission for a tool, context binding cannot prevent misuse
- **Requires accurate permission modeling:** Effectiveness depends on correctly defining tool-to-permission mappings
- **Cannot prevent all confused deputy attacks:** If tools themselves have excessive permissions, context binding alone is insufficient
- **Complexity in multi-tenant systems:** Requires careful isolation of user contexts across tenants

**Trade-offs:**

- **Implementation complexity vs. security:** Proper context binding adds architectural complexity
- **Performance vs. security:** Permission checks on every tool invocation add latency
- **Granularity vs. usability:** Fine-grained permissions increase security but complicate user management
- **Centralized vs. distributed authorization:** Centralized authorization improves consistency but creates dependencies

**Bypass Scenarios:**

- **Tool implementation vulnerabilities:** If tools don't respect user_context parameter, binding is ineffective
- **Session hijacking:** If attacker steals valid authentication token, they inherit that user's context
- **Privilege escalation in tools:** If tools themselves run with excessive system privileges, user context may not limit damage
- **Context confusion attacks:** Sophisticated attacks may confuse the system about which context applies

## Testing & Validation

**Functional Testing:**

1. **Context Extraction:** Verify user context correctly extracted from authentication tokens
2. **Permission Enforcement:** Test that tool invocations are denied when permissions are insufficient
3. **Context Binding:** Verify user context propagates through entire execution chain
4. **Data-Level Access:** Test that tools respect user context for data access decisions

**Security Testing:**

1. **Client-Controlled Tool Selection:** Attempt to bypass authorization by manipulating `tool_to_use` field
2. **Permission Escalation:** Test if low-privilege users can invoke high-privilege tools
3. **Context Manipulation:** Attempt to forge or manipulate user context parameters
4. **Token Replay:** Test that stolen tokens are properly invalidated or limited

**Test Cases:**

```python
def test_api_level_privilege_escalation():
    """
    Red team test: Attempt to escalate privileges via API manipulation
    
    Expected: Request denied with 403 Forbidden
    """
    # Authenticate as low-privilege user
    low_priv_token = authenticate_user("eric.jones", "password123")
    
    # Attempt to invoke admin tool via API manipulation
    malicious_request = {
        "task_description": "My mouse isn't working",
        "tool_to_use": "run_admin_powershell",  # Escalation attempt
        "tool_parameters": {
            "script": "New-Item 'C:\\redteam_owned.txt'"
        }
    }
    
    response = requests.post(
        "https://api.helpbot.corp/v1/tasks",
        headers={"Authorization": f"Bearer {low_priv_token}"},
        json=malicious_request
    )
    
    # Verify request is denied
    assert response.status_code == 403
    assert "permission" in response.json()["error"].lower()

def test_context_binding():
    """
    Verify user context is properly bound throughout execution
    
    Expected: Tool receives correct user context, enforces data-level permissions
    """
    user_token = authenticate_user("alice", "password")
    user_context = extract_user_context(user_token)
    
    # Attempt to reset password for another user
    result = invoke_tool(
        "password_reset",
        {"target_user_id": "bob"},
        user_context=user_context
    )
    
    # Should fail if alice doesn't have permission to reset bob's password
    if not user_context.has_permission("helpdesk.admin"):
        assert result["status"] == "error"
        assert "permission" in result["message"].lower()
```

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Context-aware authorization: Check session context, not just API fields. Validate user role against tool privilege level before execution."
> — [[sources/bibliography#Securing AI Agents]], p. 210-211

> "User Context Binding: Bind tool execution to originating user's security context. Prevent privilege inheritance from system or agent processes. Enforce user role-based access control (RBAC) at tool level. Validate user permissions match tool requirements."
> — [[sources/bibliography#Securing AI Agents]], p. 210-211 (extracted from agent authorization hijacking mitigations)

## Related

- **Combines with:** [[mitigations/least-privilege-implementation]], [[mitigations/approval-workflow-patterns]], [[mitigations/input-validation-patterns]]
- **Requires:** Strong authentication and session management
- **Supports:** [[mitigations/anomaly-detection-architecture]] (context-aware anomaly detection)
