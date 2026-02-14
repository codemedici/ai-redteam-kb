---
title: "Immutable Audit Logs"
tags:
  - type/mitigation
  - target/agent
  - source/ai-native-llm-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Immutable Audit Logs

## Summary

Immutable audit logs capture all agent actions, tool invocations, and decision points in an append-only, tamper-proof log store, enabling forensic reconstruction of agent behavior and preventing attackers from covering their tracks. By emitting structured telemetry (OpenTelemetry spans) for every significant operation and shipping logs to immutable storage with retention policies, this mitigation ensures traceability even when agents are compromised, providing the foundation for incident investigation and attribution.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/agentic-ai-security-risks-owasp-aivss]] | Prevents agent untraceability by creating verifiable audit trail of all agent actions |
| | [[techniques/unauthorized-knowledge-disclosure]] | Provides forensic evidence of data access and exfiltration attempts |
| | [[techniques/unsafe-tool-invocation]] | Records all tool invocations for post-incident analysis |

## Implementation

### OpenTelemetry Span Emission

**Emit structured telemetry for all agent operations:**

```python
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor
from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
import hashlib
import json

# Initialize OpenTelemetry
trace.set_tracer_provider(TracerProvider())
tracer = trace.get_tracer(__name__)

# Configure OTLP exporter to immutable store
otlp_exporter = OTLPSpanExporter(
    endpoint="https://telemetry-collector.corp:4317",
    insecure=False
)
span_processor = BatchSpanProcessor(otlp_exporter)
trace.get_tracer_provider().add_span_processor(span_processor)

class AuditableAgentRuntime:
    """Agent runtime with comprehensive audit logging"""
    
    def __init__(self, agent_id: str):
        self.agent_id = agent_id
        self.tracer = trace.get_tracer(f"agent.{agent_id}")
    
    def invoke_tool(self, tool_name: str, arguments: dict, user_context: dict):
        """
        Invoke tool with full audit trail
        
        Args:
            tool_name: Tool to invoke
            arguments: Tool arguments
            user_context: User security context
        
        Returns:
            Tool execution result
        """
        # ✅ EMIT OPENTELEMETRY SPAN
        with self.tracer.start_as_current_span("tool_invocation") as span:
            # Tag with user identity
            user_jwt_subject = user_context.get("user_id")
            span.set_attribute("user.id", user_jwt_subject)
            span.set_attribute("user.roles", ",".join(user_context.get("roles", [])))
            
            # Tag with tool information
            span.set_attribute("tool.name", tool_name)
            span.set_attribute("agent.id", self.agent_id)
            
            # Hash of prompt (for correlation)
            prompt_text = arguments.get("prompt", "")
            prompt_hash = hashlib.sha256(prompt_text.encode()).hexdigest()
            span.set_attribute("prompt.hash", prompt_hash)
            
            # Serialize arguments (sanitize sensitive data)
            sanitized_args = self._sanitize_arguments(arguments)
            span.set_attribute("tool.arguments", json.dumps(sanitized_args))
            
            try:
                # Execute tool
                result = self._execute_tool(tool_name, arguments)
                
                # Record success
                span.set_attribute("tool.status", "success")
                span.set_attribute("tool.result_size", len(str(result)))
                
                return result
            
            except Exception as e:
                # Record failure
                span.set_attribute("tool.status", "error")
                span.set_attribute("tool.error", str(e))
                span.record_exception(e)
                raise
    
    def _sanitize_arguments(self, arguments: dict) -> dict:
        """Remove sensitive data from arguments before logging"""
        sensitive_keys = {"password", "api_key", "token", "secret"}
        
        sanitized = {}
        for key, value in arguments.items():
            if key.lower() in sensitive_keys:
                sanitized[key] = "[REDACTED]"
            else:
                sanitized[key] = value
        
        return sanitized
    
    def _execute_tool(self, tool_name: str, arguments: dict):
        """Internal tool execution"""
        # Implementation varies
        pass
```

### Immutable Log Storage

**Ship logs to append-only, tamper-proof storage:**

```python
from datetime import datetime, timedelta
from typing import List
import boto3
from botocore.exceptions import ClientError

class ImmutableLogStore:
    """
    Immutable log storage with retention policies
    
    Uses S3 Object Lock or equivalent append-only storage
    """
    
    def __init__(self, bucket_name: str, retention_days: int = 30):
        self.bucket_name = bucket_name
        self.retention_days = retention_days
        self.s3_client = boto3.client('s3')
    
    def store_log_entry(self, agent_id: str, event_type: str, event_data: dict):
        """
        Store log entry in immutable storage
        
        Args:
            agent_id: Agent identifier
            event_type: Type of event (e.g., "tool_invocation", "decision")
            event_data: Event details
        """
        # Create log entry
        log_entry = {
            "agent_id": agent_id,
            "event_type": event_type,
            "timestamp": datetime.now().isoformat(),
            "data": event_data
        }
        
        # Generate unique key
        timestamp_prefix = datetime.now().strftime("%Y/%m/%d/%H")
        log_key = f"agent-logs/{agent_id}/{timestamp_prefix}/{event_type}-{datetime.now().timestamp()}.json"
        
        try:
            # Upload with Object Lock (immutable)
            self.s3_client.put_object(
                Bucket=self.bucket_name,
                Key=log_key,
                Body=json.dumps(log_entry),
                ContentType='application/json',
                # S3 Object Lock: Prevents deletion/modification
                ObjectLockMode='COMPLIANCE',
                ObjectLockRetainUntilDate=datetime.now() + timedelta(days=self.retention_days)
            )
            
            return log_key
        
        except ClientError as e:
            # Log storage failure (but don't block operation)
            print(f"Failed to store audit log: {e}")
            # In production: emit alert, failover to backup storage
            return None
    
    def query_logs(
        self,
        agent_id: str,
        start_time: datetime,
        end_time: datetime,
        event_types: List[str] = None
    ) -> List[dict]:
        """
        Query audit logs for forensic investigation
        
        Args:
            agent_id: Agent to query
            start_time: Start of time range
            end_time: End of time range
            event_types: Optional filter by event types
        
        Returns:
            List of matching log entries
        """
        # Generate S3 prefix for time range
        prefix = f"agent-logs/{agent_id}/"
        
        # List objects in range
        results = []
        paginator = self.s3_client.get_paginator('list_objects_v2')
        
        for page in paginator.paginate(Bucket=self.bucket_name, Prefix=prefix):
            for obj in page.get('Contents', []):
                # Parse timestamp from key
                # (In production: use S3 Select or Athena for efficient querying)
                
                # Retrieve object
                response = self.s3_client.get_object(Bucket=self.bucket_name, Key=obj['Key'])
                log_entry = json.loads(response['Body'].read())
                
                # Filter by time and event type
                entry_time = datetime.fromisoformat(log_entry['timestamp'])
                
                if start_time <= entry_time <= end_time:
                    if event_types is None or log_entry['event_type'] in event_types:
                        results.append(log_entry)
        
        return results
```

### Prompt Lineage Tracking

**Track causal relationships between prompts and actions:**

```python
from uuid import uuid4

class PromptLineageTracker:
    """Track lineage from user prompt to agent actions"""
    
    def __init__(self, log_store: ImmutableLogStore):
        self.log_store = log_store
    
    def track_prompt(self, agent_id: str, user_id: str, prompt_text: str) -> str:
        """
        Record user prompt with unique trace ID
        
        Returns: trace_id for correlating downstream actions
        """
        trace_id = str(uuid4())
        
        # Compute prompt hash
        prompt_hash = hashlib.sha256(prompt_text.encode()).hexdigest()
        
        # Log prompt
        self.log_store.store_log_entry(
            agent_id=agent_id,
            event_type="user_prompt",
            event_data={
                "trace_id": trace_id,
                "user_id": user_id,
                "prompt_hash": prompt_hash,
                "prompt_length": len(prompt_text),
                "prompt_preview": prompt_text[:200]  # First 200 chars
            }
        )
        
        return trace_id
    
    def track_tool_invocation(
        self,
        agent_id: str,
        trace_id: str,
        tool_name: str,
        arguments: dict,
        result: dict
    ):
        """
        Record tool invocation linked to originating prompt
        
        Args:
            agent_id: Agent executing tool
            trace_id: Trace ID from originating prompt
            tool_name: Tool invoked
            arguments: Tool arguments
            result: Tool execution result
        """
        self.log_store.store_log_entry(
            agent_id=agent_id,
            event_type="tool_invocation",
            event_data={
                "trace_id": trace_id,
                "tool_name": tool_name,
                "arguments_hash": hashlib.sha256(
                    json.dumps(arguments, sort_keys=True).encode()
                ).hexdigest(),
                "result_status": result.get("status"),
                "timestamp": datetime.now().isoformat()
            }
        )
    
    def reconstruct_trace(self, trace_id: str) -> List[dict]:
        """
        Reconstruct full causal chain for a trace
        
        Returns chronological sequence: prompt → decisions → actions
        """
        # Query logs for this trace_id
        # (Implementation depends on log storage backend)
        pass
```

### Behavioral Anomaly Correlation

**Correlate audit logs with behavioral patterns:**

```python
class AuditLogAnalyzer:
    """Analyze audit logs for security anomalies"""
    
    def __init__(self, log_store: ImmutableLogStore):
        self.log_store = log_store
    
    def detect_unusual_tool_usage(
        self,
        agent_id: str,
        time_window_hours: int = 24
    ) -> List[dict]:
        """
        Detect unusual tool invocation patterns
        
        Returns: List of anomalous events
        """
        end_time = datetime.now()
        start_time = end_time - timedelta(hours=time_window_hours)
        
        # Query tool invocation logs
        logs = self.log_store.query_logs(
            agent_id=agent_id,
            start_time=start_time,
            end_time=end_time,
            event_types=["tool_invocation"]
        )
        
        # Analyze patterns
        anomalies = []
        
        # 1. Frequency anomalies
        tool_counts = {}
        for log in logs:
            tool_name = log['data'].get('tool_name')
            tool_counts[tool_name] = tool_counts.get(tool_name, 0) + 1
        
        # Flag tools used significantly more than baseline
        for tool, count in tool_counts.items():
            baseline = self._get_baseline_count(agent_id, tool)
            if count > baseline * 3:  # 3x normal usage
                anomalies.append({
                    "type": "high_frequency",
                    "tool": tool,
                    "count": count,
                    "baseline": baseline
                })
        
        # 2. Unusual tool combinations
        # (Implementation: detect tool chains that don't match historical patterns)
        
        # 3. Off-hours access
        for log in logs:
            timestamp = datetime.fromisoformat(log['timestamp'])
            if timestamp.hour < 6 or timestamp.hour > 22:  # Outside business hours
                anomalies.append({
                    "type": "off_hours_access",
                    "timestamp": log['timestamp'],
                    "tool": log['data'].get('tool_name')
                })
        
        return anomalies
    
    def _get_baseline_count(self, agent_id: str, tool_name: str) -> int:
        """Get historical baseline count for tool usage"""
        # Implementation: query historical data
        return 10  # Placeholder
```

## Limitations & Trade-offs

**Limitations:**

- **Cannot prevent attacks in real-time:** Logging is detective, not preventive
- **Storage costs:** Immutable logs with long retention consume significant storage
- **Log flooding:** Attacker may attempt to flood logs with noise to obscure malicious activity
- **Sensitive data leakage:** Logs may inadvertently capture sensitive information if not sanitized

**Trade-offs:**

- **Security vs. Performance:** Comprehensive logging adds overhead to every operation
- **Retention vs. Cost:** Longer retention improves forensics but increases storage costs
- **Detail vs. Privacy:** Verbose logs aid investigation but may capture sensitive user data
- **Immutability vs. Compliance:** Immutable logs support forensics but complicate right-to-deletion requirements

**Bypass Scenarios:**

- **Log injection:** Attacker may attempt to inject false log entries (mitigated by immutability and signing)
- **Storage compromise:** If log storage backend is compromised, immutability guarantees may be violated
- **Timing attacks:** Attacker may time malicious activity to exploit log retention expiration
- **Log correlation evasion:** Sophisticated attacker may structure activity to evade correlation analysis

## Testing & Validation

**Functional Testing:**

1. **Log Completeness:** Verify all agent operations are logged
2. **Immutability:** Confirm stored logs cannot be modified or deleted before retention expires
3. **Query Performance:** Test log query efficiency for forensic investigations
4. **Trace Reconstruction:** Validate ability to reconstruct full causal chain from trace ID

**Security Testing:**

1. **Log Injection:** Attempt to inject false log entries
2. **Log Deletion:** Test immutability by attempting to delete or modify logs
3. **Sanitization:** Verify sensitive data is correctly redacted from logs
4. **Correlation Evasion:** Test if anomaly detection can be evaded

**Monitoring & Metrics:**

- **Log Volume:** Track log generation rate per agent
- **Storage Utilization:** Monitor immutable storage usage and costs
- **Query Latency:** Measure forensic query performance
- **Anomaly Detection:** Alert on detected unusual patterns

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Emit OpenTelemetry span for every tool call, tag with user JWT subject + hash of prompt, ship to immutable store with 30-day retention."
> — [[sources/bibliography#AI-Native LLM Security]], pp. 359-360, 366-367

> "Unique verifiable identity per agent (no shared credentials/anonymous tokens) enables traceability and attribution."
> — [[sources/bibliography#AI-Native LLM Security]], pp. 359-360, 366-367

## Related

- **Combines with:** [[mitigations/telemetry-and-monitoring-architecture]], [[mitigations/behavioral-monitoring]]
- **Requires:** [[mitigations/workload-identity-spiffe]] (unique agent identities)
- **Supports:** [[mitigations/anomaly-detection-architecture]], [[mitigations/incident-response-procedures]]
