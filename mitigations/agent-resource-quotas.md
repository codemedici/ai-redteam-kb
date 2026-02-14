---
title: "Agent Resource Quotas"
tags:
  - type/mitigation
  - target/agent
  - source/ai-native-llm-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Agent Resource Quotas

## Summary

Agent resource quotas limit the computational, financial, and system resources available to AI agents, preventing resource exhaustion attacks and containing the blast radius when agents are compromised or manipulated. By implementing per-agent spending limits, rate limits, and circuit breakers, this mitigation ensures that errors or cascading failures in one component cannot monopolize resources or cause large-scale system degradation.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/agentic-ai-security-risks-owasp-aivss]] | Prevents agent cascading failures and tool misuse through resource containment |
| | [[techniques/unsafe-tool-invocation]] | Limits financial and computational damage from malicious tool invocations |
| | [[techniques/agent-goal-hijack]] | Caps resource consumption when agent goals are manipulated |

## Implementation

### Financial Quota Management

**Implement prepaid wallets with spending limits:**

```python
from dataclasses import dataclass
from decimal import Decimal
from datetime import datetime
from typing import Optional

@dataclass
class AgentWallet:
    """Agent financial quota tracking"""
    agent_id: str
    balance: Decimal
    daily_limit: Decimal
    total_spent_today: Decimal
    last_reset: datetime
    
    def can_spend(self, amount: Decimal) -> bool:
        """Check if agent has budget for operation"""
        return (
            self.balance >= amount and 
            (self.total_spent_today + amount) <= self.daily_limit
        )
    
    def deduct(self, amount: Decimal, operation: str):
        """
        Deduct amount from agent wallet
        
        Raises:
            InsufficientFundsError if budget exceeded
        """
        if not self.can_spend(amount):
            raise InsufficientFundsError(
                f"Agent {self.agent_id} insufficient budget: "
                f"requested ${amount}, balance ${self.balance}, "
                f"daily remaining ${self.daily_limit - self.total_spent_today}"
            )
        
        self.balance -= amount
        self.total_spent_today += amount
        
        log_financial_event("agent_spend", {
            "agent_id": self.agent_id,
            "amount": float(amount),
            "operation": operation,
            "remaining_balance": float(self.balance)
        })

class AgentWalletManager:
    """Manage agent financial quotas"""
    
    def __init__(self):
        self.wallets = {}
    
    def create_wallet(self, agent_id: str, initial_balance: Decimal, daily_limit: Decimal):
        """Initialize agent wallet with quotas"""
        self.wallets[agent_id] = AgentWallet(
            agent_id=agent_id,
            balance=initial_balance,
            daily_limit=daily_limit,
            total_spent_today=Decimal("0"),
            last_reset=datetime.now()
        )
    
    def authorize_spend(self, agent_id: str, amount: Decimal, operation: str) -> bool:
        """
        Authorize spending operation
        
        Returns: True if authorized, False otherwise
        """
        wallet = self.wallets.get(agent_id)
        if not wallet:
            raise ValueError(f"No wallet found for agent {agent_id}")
        
        # Reset daily counter if needed
        self._reset_daily_limit_if_needed(wallet)
        
        # Check and deduct
        try:
            wallet.deduct(amount, operation)
            return True
        except InsufficientFundsError:
            log_security_event("agent_spending_blocked", {
                "agent_id": agent_id,
                "requested_amount": float(amount),
                "operation": operation,
                "reason": "insufficient_budget"
            })
            return False
    
    def _reset_daily_limit_if_needed(self, wallet: AgentWallet):
        """Reset daily spending counter at day boundary"""
        now = datetime.now()
        if now.date() > wallet.last_reset.date():
            wallet.total_spent_today = Decimal("0")
            wallet.last_reset = now
```

### Circuit Breaker Pattern

**Implement circuit breakers to prevent cascading failures:**

```python
from enum import Enum
from threading import Lock
import time

class CircuitState(Enum):
    """Circuit breaker states"""
    CLOSED = "closed"      # Normal operation
    OPEN = "open"          # Failure threshold exceeded, blocking requests
    HALF_OPEN = "half_open"  # Testing if service recovered

class CircuitBreaker:
    """
    Circuit breaker for agent operations
    
    Prevents cascading failures by opening circuit when error rate exceeds threshold
    """
    
    def __init__(
        self,
        name: str,
        failure_threshold: int = 5,
        timeout_seconds: int = 60,
        half_open_max_calls: int = 3
    ):
        self.name = name
        self.failure_threshold = failure_threshold
        self.timeout_seconds = timeout_seconds
        self.half_open_max_calls = half_open_max_calls
        
        self.state = CircuitState.CLOSED
        self.failure_count = 0
        self.last_failure_time = None
        self.half_open_calls = 0
        self.lock = Lock()
    
    def call(self, func, *args, **kwargs):
        """
        Execute function with circuit breaker protection
        
        Raises:
            CircuitOpenError if circuit is open
        """
        with self.lock:
            # Check if circuit should transition to half-open
            if self.state == CircuitState.OPEN:
                if self._should_attempt_reset():
                    self.state = CircuitState.HALF_OPEN
                    self.half_open_calls = 0
                else:
                    raise CircuitOpenError(
                        f"Circuit '{self.name}' is OPEN. "
                        f"Retry after {self.timeout_seconds}s"
                    )
            
            # Limit calls in half-open state
            if self.state == CircuitState.HALF_OPEN:
                if self.half_open_calls >= self.half_open_max_calls:
                    raise CircuitOpenError(
                        f"Circuit '{self.name}' in HALF_OPEN, max test calls exceeded"
                    )
                self.half_open_calls += 1
        
        # Execute function
        try:
            result = func(*args, **kwargs)
            self._on_success()
            return result
        except Exception as e:
            self._on_failure()
            raise
    
    def _on_success(self):
        """Handle successful call"""
        with self.lock:
            if self.state == CircuitState.HALF_OPEN:
                # Recovery successful, close circuit
                self.state = CircuitState.CLOSED
                self.failure_count = 0
                log_circuit_event(self.name, "circuit_closed", "recovery_successful")
            
            # Reset failure count on success in closed state
            if self.state == CircuitState.CLOSED:
                self.failure_count = 0
    
    def _on_failure(self):
        """Handle failed call"""
        with self.lock:
            self.failure_count += 1
            self.last_failure_time = time.time()
            
            # Open circuit if threshold exceeded
            if self.failure_count >= self.failure_threshold:
                self.state = CircuitState.OPEN
                log_circuit_event(self.name, "circuit_opened", {
                    "failure_count": self.failure_count,
                    "threshold": self.failure_threshold
                })
            
            # If in half-open and still failing, reopen circuit
            if self.state == CircuitState.HALF_OPEN:
                self.state = CircuitState.OPEN
                log_circuit_event(self.name, "circuit_reopened", "half_open_test_failed")
    
    def _should_attempt_reset(self) -> bool:
        """Check if enough time has passed to try half-open"""
        if self.last_failure_time is None:
            return False
        return (time.time() - self.last_failure_time) >= self.timeout_seconds

# Usage in agent runtime
class AgentRuntimeWithCircuitBreakers:
    """Agent runtime with circuit breaker protection"""
    
    def __init__(self):
        self.breakers = {
            "tool_invocation": CircuitBreaker("tool_invocation", failure_threshold=5),
            "llm_inference": CircuitBreaker("llm_inference", failure_threshold=3),
            "database_query": CircuitBreaker("database_query", failure_threshold=10)
        }
    
    def invoke_tool_with_breaker(self, tool_name: str, arguments: dict):
        """Invoke tool with circuit breaker protection"""
        breaker = self.breakers["tool_invocation"]
        
        try:
            return breaker.call(self._execute_tool, tool_name, arguments)
        except CircuitOpenError as e:
            log_security_event("circuit_breaker_blocked_call", {
                "circuit": "tool_invocation",
                "tool": tool_name,
                "reason": str(e)
            })
            raise
```

### Per-Agent Rate Limiting

**Implement request rate limits per agent:**

```python
from collections import defaultdict
from time import time

class AgentRateLimiter:
    """Rate limit agent operations"""
    
    def __init__(self, requests_per_minute: int = 60):
        self.requests_per_minute = requests_per_minute
        self.window_size = 60  # seconds
        
        # Track request timestamps per agent
        self.request_history = defaultdict(list)
    
    def check_rate_limit(self, agent_id: str) -> bool:
        """
        Check if agent has exceeded rate limit
        
        Returns: True if within limit, False if exceeded
        """
        now = time()
        cutoff = now - self.window_size
        
        # Remove old requests outside window
        self.request_history[agent_id] = [
            ts for ts in self.request_history[agent_id]
            if ts > cutoff
        ]
        
        # Check if under limit
        if len(self.request_history[agent_id]) >= self.requests_per_minute:
            log_security_event("agent_rate_limit_exceeded", {
                "agent_id": agent_id,
                "requests_in_window": len(self.request_history[agent_id]),
                "limit": self.requests_per_minute
            })
            return False
        
        # Record this request
        self.request_history[agent_id].append(now)
        return True
    
    def get_remaining_quota(self, agent_id: str) -> int:
        """Get remaining requests in current window"""
        now = time()
        cutoff = now - self.window_size
        
        recent_requests = [
            ts for ts in self.request_history[agent_id]
            if ts > cutoff
        ]
        
        return max(0, self.requests_per_minute - len(recent_requests))
```

### Token Usage Quotas

**Limit LLM token consumption per agent:**

```python
class AgentTokenQuota:
    """Manage LLM token quotas for agents"""
    
    def __init__(self, daily_token_limit: int = 100000):
        self.daily_token_limit = daily_token_limit
        self.token_usage = defaultdict(int)
        self.last_reset = defaultdict(lambda: datetime.now())
    
    def authorize_tokens(self, agent_id: str, estimated_tokens: int) -> bool:
        """
        Check if agent has token quota available
        
        Args:
            agent_id: Agent identifier
            estimated_tokens: Estimated tokens for operation
        
        Returns: True if authorized, False if quota exceeded
        """
        # Reset daily counter if needed
        self._reset_if_needed(agent_id)
        
        # Check quota
        if self.token_usage[agent_id] + estimated_tokens > self.daily_token_limit:
            log_security_event("agent_token_quota_exceeded", {
                "agent_id": agent_id,
                "current_usage": self.token_usage[agent_id],
                "requested": estimated_tokens,
                "daily_limit": self.daily_token_limit
            })
            return False
        
        return True
    
    def record_usage(self, agent_id: str, actual_tokens: int):
        """Record actual token usage"""
        self.token_usage[agent_id] += actual_tokens
        
        log_token_usage(agent_id, actual_tokens, self.token_usage[agent_id])
    
    def _reset_if_needed(self, agent_id: str):
        """Reset daily counter at day boundary"""
        now = datetime.now()
        if now.date() > self.last_reset[agent_id].date():
            self.token_usage[agent_id] = 0
            self.last_reset[agent_id] = now
```

## Limitations & Trade-offs

**Limitations:**

- **Cannot prevent all cascading failures:** Failures may propagate through non-resource pathways (logic errors, data corruption)
- **Quota calibration challenges:** Setting appropriate limits requires understanding normal vs. abnormal usage patterns
- **May impact legitimate operations:** Restrictive quotas can block valid agent operations during usage spikes
- **Does not prevent authorized resource misuse:** If operation is within quota, this control cannot prevent misuse

**Trade-offs:**

- **Security vs. Availability:** Strict quotas improve containment but may cause false positives during legitimate high-load periods
- **Granularity vs. Complexity:** Fine-grained quotas (per-tool, per-operation) improve control but increase management overhead
- **Static vs. Dynamic:** Static quotas are simpler but inflexible; dynamic quotas adapt but add complexity
- **Fail-open vs. Fail-closed:** Circuit breakers that fail open maintain availability but reduce security; fail-closed prioritizes security

**Bypass Scenarios:**

- **Distributed abuse:** Attacker spreads operations across multiple agents to stay under per-agent quotas
- **Quota gaming:** Malicious operations optimized to stay just under quota thresholds
- **Resource type substitution:** If only financial quotas are enforced, attacker may exhaust other resources (CPU, memory, network)
- **Circuit breaker manipulation:** Triggering circuit breakers deliberately to cause denial of service

## Testing & Validation

**Functional Testing:**

1. **Quota Enforcement:** Verify agents are blocked when quotas are exceeded
2. **Circuit Breaker Transitions:** Test state transitions (closed → open → half-open → closed)
3. **Daily Reset:** Confirm quotas reset correctly at day boundaries
4. **Rate Limiting:** Validate request rate limits are enforced

**Security Testing:**

1. **Quota Bypass:** Attempt to exceed quotas through various methods
2. **Circuit Breaker Abuse:** Test if circuit breakers can be manipulated to cause DoS
3. **Resource Exhaustion:** Verify quotas prevent resource exhaustion attacks
4. **Multi-Agent Coordination:** Test if quotas can be evaded by distributing across agents

**Monitoring & Metrics:**

- **Quota Utilization:** Track agent resource usage against quotas
- **Circuit Breaker States:** Monitor frequency of circuit opens and recovery times
- **Blocked Operations:** Alert when operations are blocked due to quota/rate limits
- **Resource Trends:** Identify agents with unusual resource consumption patterns

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Prepaid wallet with limited token use caps financial damage when agents are compromised or manipulated."
> — [[sources/bibliography#AI-Native LLM Security]], pp. 357-362

> "Per-agent resource quotas and circuit breakers prevent cascading failures from propagating through entire pipeline."
> — [[sources/bibliography#AI-Native LLM Security]], pp. 359, 363

## Related

- **Combines with:** [[mitigations/rate-limiting-and-throttling]], [[mitigations/anomaly-detection-architecture]]
- **Complements:** [[mitigations/tool-sandboxing-architecture]], [[mitigations/agent-tool-allowlisting]]
