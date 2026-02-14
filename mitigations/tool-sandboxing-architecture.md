---
title: "Tool Sandboxing Architecture"
tags:
  - trust-boundary/agent-runtime
  - type/mitigation
  - target/agent
  - source/ai-native-llm-security
atlas: AML.M0032
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Tool Sandboxing Architecture

## Summary

Tool sandboxing architecture is an isolation control that executes agent tool invocations in restricted environments, preventing compromised or malicious tool calls from impacting production systems. By containing tool execution within sandboxed contexts with limited permissions, network access, and resource quotas, this mitigation limits the blast radius when agents are manipulated into unsafe tool invocations. Sandboxing provides a critical safety boundary between language model reasoning (which can be manipulated through prompt injection or other attacks) and real-world system access.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/unsafe-tool-invocation]] | Contains damage from malicious tool invocations by restricting execution environment |
| | [[techniques/tool-privilege-escalation]] | Prevents escalated tool permissions from accessing protected resources through sandbox boundaries |
| | [[techniques/agent-goal-hijack]] | Limits impact of hijacked agent goals by restricting what tools can actually execute |
| | [[techniques/prompt-injection]] | Reduces impact of injected instructions that trigger dangerous tool calls |

## Implementation

### Sandbox Isolation Strategies

**1. Container-Based Sandboxing**

Execute tool calls in isolated containers with restricted capabilities:

```python
import docker
import json

class DockerToolSandbox:
    """Execute tools in isolated Docker containers"""
    
    def __init__(self):
        self.client = docker.from_env()
        self.sandbox_image = "tool-sandbox:latest"  # Pre-built minimal image
    
    def execute_tool(self, tool_name, arguments, timeout=30):
        """
        Execute tool in isolated container with resource limits
        
        Args:
            tool_name: Name of tool to execute
            arguments: Tool arguments (JSON-serializable)
            timeout: Maximum execution time in seconds
        
        Returns:
            Tool execution result
        """
        # Prepare command
        command = [
            "python", "-m", f"tools.{tool_name}",
            "--args", json.dumps(arguments)
        ]
        
        # Run container with strict limits
        try:
            container = self.client.containers.run(
                self.sandbox_image,
                command=command,
                detach=True,
                mem_limit="512m",           # Memory limit
                cpu_quota=50000,            # CPU limit (50% of one core)
                network_mode="none",        # No network access
                read_only=True,             # Read-only filesystem
                security_opt=["no-new-privileges"],  # Prevent privilege escalation
                cap_drop=["ALL"],           # Drop all capabilities
                pids_limit=100,             # Process limit
                remove=True                 # Auto-remove after execution
            )
            
            # Wait for completion with timeout
            result = container.wait(timeout=timeout)
            output = container.logs().decode('utf-8')
            
            return {
                'status': result['StatusCode'],
                'output': output
            }
            
        except docker.errors.ContainerError as e:
            raise ToolExecutionError(f"Tool execution failed: {e}")
        except Exception as e:
            raise ToolExecutionError(f"Sandbox error: {e}")
```

**Benefits:**
- **Strong isolation:** Kernel-level isolation between tool and host
- **Resource quotas:** Memory, CPU, and process limits prevent resource exhaustion
- **Network isolation:** No external connections without explicit permission
- **Read-only filesystem:** Prevents unauthorized file modifications

**2. Virtual Machine Sandboxing (High-Security)**

For high-risk tools, use full VM isolation:

```python
import subprocess
import json

class VMToolSandbox:
    """Execute tools in ephemeral VMs using Firecracker micro-VMs"""
    
    def execute_tool(self, tool_name, arguments, timeout=60):
        """
        Execute tool in isolated micro-VM
        
        Provides stronger isolation than containers for high-risk operations
        """
        # Create ephemeral VM configuration
        vm_config = {
            'kernel': '/var/firecracker/vmlinux',
            'rootfs': f'/var/firecracker/rootfs/{tool_name}.ext4',
            'vcpu_count': 1,
            'mem_size_mib': 256,
            'boot_args': f'console=ttyS0 reboot=k panic=1 pci=off tool={tool_name}'
        }
        
        # Launch micro-VM
        result = subprocess.run(
            ['firecracker', '--config-file', '/tmp/vm_config.json'],
            input=json.dumps(arguments).encode(),
            capture_output=True,
            timeout=timeout
        )
        
        return result.stdout.decode('utf-8')
```

**Benefits:**
- **Hardware-level isolation:** Stronger than containers
- **Minimal attack surface:** Micro-VMs have minimal software stack
- **Fast startup:** Modern micro-VMs start in ~150ms

**Use cases:** Code execution tools, data processing on untrusted inputs, high-value operations

**3. Process-Level Sandboxing (Lightweight)**

For lower-risk tools, use OS-level sandboxing:

```python
import subprocess
import resource

def execute_sandboxed_process(tool_command, arguments, timeout=10):
    """
    Execute tool in sandboxed subprocess with resource limits
    
    Uses OS-level controls (ulimit, seccomp, capabilities)
    """
    def set_limits():
        # Memory limit (100MB)
        resource.setrlimit(resource.RLIMIT_AS, (100 * 1024 * 1024, 100 * 1024 * 1024))
        
        # CPU time limit (10 seconds)
        resource.setrlimit(resource.RLIMIT_CPU, (10, 10))
        
        # File size limit (10MB)
        resource.setrlimit(resource.RLIMIT_FSIZE, (10 * 1024 * 1024, 10 * 1024 * 1024))
        
        # Process limit
        resource.setrlimit(resource.RLIMIT_NPROC, (10, 10))
    
    try:
        result = subprocess.run(
            tool_command + arguments,
            capture_output=True,
            timeout=timeout,
            preexec_fn=set_limits,  # Apply limits before execution
            cwd="/tmp/sandbox",     # Restricted working directory
            env={"PATH": "/usr/bin:/bin"}  # Minimal environment
        )
        
        return result.stdout.decode('utf-8')
    
    except subprocess.TimeoutExpired:
        raise ToolExecutionError("Tool execution timed out")
    except Exception as e:
        raise ToolExecutionError(f"Execution failed: {e}")
```

### Sandbox Permission Models

**Network Access Control:**

```python
class NetworkIsolationPolicy:
    """Define network access policies for sandboxed tools"""
    
    POLICIES = {
        'read_database': {
            'network': 'limited',
            'allowed_hosts': ['db.internal.corp:5432'],
            'protocols': ['postgresql']
        },
        'send_email': {
            'network': 'limited',
            'allowed_hosts': ['smtp.internal.corp:587'],
            'protocols': ['smtp']
        },
        'execute_code': {
            'network': 'none',  # No network access
            'allowed_hosts': [],
            'protocols': []
        }
    }
    
    @staticmethod
    def get_policy(tool_name):
        return NetworkIsolationPolicy.POLICIES.get(tool_name, {
            'network': 'none',
            'allowed_hosts': [],
            'protocols': []
        })
```

**Filesystem Access Control:**

```python
class FilesystemIsolationPolicy:
    """Define filesystem access policies for tools"""
    
    @staticmethod
    def get_allowed_paths(tool_name, user_context):
        """
        Return allowed filesystem paths for tool execution
        
        Implements principle of least privilege for file access
        """
        base_policies = {
            'read_file': {
                'read': ['/data/public/', f'/data/users/{user_context.user_id}/'],
                'write': []
            },
            'write_file': {
                'read': [f'/data/users/{user_context.user_id}/'],
                'write': [f'/data/users/{user_context.user_id}/outputs/']
            },
            'execute_code': {
                'read': ['/tmp/sandbox/'],
                'write': ['/tmp/sandbox/outputs/']
            }
        }
        
        return base_policies.get(tool_name, {'read': [], 'write': []})
```

### Tool Chaining Restrictions

**Disable or restrict tool chaining** to prevent lateral movement:

```python
class ToolChainGuard:
    """Restrict unsafe tool chaining sequences"""
    
    DANGEROUS_CHAINS = [
        # Read → Write chains that could enable data exfiltration
        (['read_database', 'send_email'], 'Data exfiltration risk'),
        (['read_file', 'external_api_call'], 'Data leakage risk'),
        
        # Privilege escalation chains
        (['read_config', 'modify_permissions'], 'Privilege escalation'),
        
        # Code execution chains
        (['generate_code', 'execute_code'], 'Arbitrary code execution')
    ]
    
    def __init__(self):
        self.tool_history = []
    
    def check_tool_chain(self, next_tool):
        """
        Check if invoking next_tool would create a dangerous chain
        
        Returns: (allowed: bool, reason: str)
        """
        # Check recent tool history
        recent_chain = self.tool_history[-5:] + [next_tool]
        
        for dangerous_chain, reason in self.DANGEROUS_CHAINS:
            if self._matches_pattern(recent_chain, dangerous_chain):
                return False, f"Blocked: {reason}"
        
        return True, "Allowed"
    
    def record_invocation(self, tool_name):
        """Record tool invocation in chain history"""
        self.tool_history.append({
            'tool': tool_name,
            'timestamp': datetime.now()
        })
        
        # Keep only recent history (prevent unbounded memory growth)
        if len(self.tool_history) > 100:
            self.tool_history = self.tool_history[-50:]
    
    def _matches_pattern(self, chain, pattern):
        """Check if chain contains the dangerous pattern"""
        chain_tools = [t if isinstance(t, str) else t['tool'] for t in chain]
        
        # Check if pattern appears as subsequence in chain
        for i in range(len(chain_tools) - len(pattern) + 1):
            if chain_tools[i:i+len(pattern)] == pattern:
                return True
        return False
```

**Integration with agent framework:**

```python
class SandboxedAgentRuntime:
    """Agent runtime with sandboxed tool execution"""
    
    def __init__(self):
        self.sandbox = DockerToolSandbox()
        self.chain_guard = ToolChainGuard()
    
    def invoke_tool(self, tool_name, arguments, user_context):
        """
        Invoke tool with sandboxing and chain restrictions
        """
        # Check tool chain safety
        allowed, reason = self.chain_guard.check_tool_chain(tool_name)
        if not allowed:
            raise ToolChainViolation(reason)
        
        # Get isolation policies
        net_policy = NetworkIsolationPolicy.get_policy(tool_name)
        fs_policy = FilesystemIsolationPolicy.get_allowed_paths(tool_name, user_context)
        
        # Execute in sandbox
        try:
            result = self.sandbox.execute_tool(
                tool_name, 
                arguments,
                network_policy=net_policy,
                filesystem_policy=fs_policy
            )
            
            # Record successful invocation
            self.chain_guard.record_invocation(tool_name)
            
            return result
            
        except ToolExecutionError as e:
            # Log failure but don't record in chain
            log_security_event("tool_execution_failed", {
                'tool': tool_name,
                'error': str(e)
            })
            raise
```

## Limitations & Trade-offs

**Limitations:**

- **Cannot prevent authorized misuse:** If a tool is legitimately accessible, sandboxing cannot prevent a malicious agent (or user) from invoking it
- **Complexity overhead:** Sandboxing adds significant implementation and operational complexity
- **Performance impact:** Container/VM startup latency adds 100ms-1s per tool invocation
- **Debugging challenges:** Isolated environments make troubleshooting tool failures more difficult

**Trade-offs:**

- **Security vs. Performance:** Stronger isolation (VMs) provides better security but higher latency than lightweight isolation (processes)
- **Isolation vs. Functionality:** Restricting network/filesystem access improves security but may break legitimate tool functionality
- **Tool chaining restrictions vs. Agent capability:** Disabling chaining improves security but limits agent problem-solving ability

**Bypass Scenarios:**

- **Sandbox escape vulnerabilities:** Container/VM escape exploits could bypass isolation
- **Side-channel attacks:** Timing, cache, or other side channels may leak information across sandbox boundary
- **Misconfigured policies:** Overly permissive network or filesystem policies reduce isolation effectiveness
- **Tool implementation bugs:** Vulnerabilities in the tool itself may allow sandbox-contained attacks

## Testing & Validation

**Functional Testing:**

1. **Isolation Verification:** Verify sandboxed tools cannot access prohibited resources (network, filesystem, processes)
2. **Resource Limit Enforcement:** Test that memory, CPU, and timeout limits are enforced
3. **Permission Boundary Testing:** Attempt filesystem and network access outside allowed policies
4. **Tool Chain Restrictions:** Verify dangerous tool chains are blocked

**Security Testing:**

1. **Sandbox Escape Attempts:** Test for known container/VM escape vulnerabilities
2. **Privilege Escalation:** Attempt to gain elevated privileges within sandbox
3. **Resource Exhaustion:** Test that resource limits prevent DoS attacks
4. **Data Exfiltration:** Verify isolation prevents data leakage to unauthorized destinations

**Monitoring & Metrics:**

- **Sandbox Violations:** Track attempts to access prohibited resources
- **Resource Limit Hits:** Monitor tools hitting memory/CPU/timeout limits
- **Chain Blocks:** Frequency of tool chain restriction enforcement
- **Execution Failures:** Tool failures within sandbox (may indicate attacks or misconfigurations)

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Insecure plugin design demonstrates need for sandboxing and isolation of plugin/tool execution environments. Plugins should not be able to invoke other plugins by default. More transparency and user control over plugin invocations and data sharing needed."
> — [[sources/bibliography#AI-Native LLM Security]], p. 136-138

## Related

- **Combines with:** [[mitigations/least-privilege-implementation]], [[mitigations/approval-workflow-patterns]], [[mitigations/input-validation-patterns]]
- **Complements:** [[mitigations/kill-switch-and-session-termination]], [[mitigations/anomaly-detection-architecture]]
