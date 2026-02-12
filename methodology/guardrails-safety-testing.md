---
tags:
  - trust-boundary/data-knowledge
  - trust-boundary/model
  - type/methodology
---
# Guardrails and Safety Evaluation

## Testing Approach for Safety Controls

Safety evaluation validates whether guardrails function under adversarial pressure:

### Jailbreak Resistance

**What we measure**:
- Policy coverage: Which prohibited content types are blocked
- Bypass resilience: Success rate of known jailbreak techniques (DAN, role-play, hypothetical scenarios)
- Multi-turn robustness: Whether conditioning attacks gradually erode resistance
- Obfuscation handling: Base64, ROT13, language switching, synonym substitution

**Test corpus**: 100+ jailbreak prompts across 10+ harmful categories (violence, illegal activity, discrimination, misinformation, etc.)

**Success criteria**: 
- Under 5% bypass rate on direct attempts
- Under 10% bypass rate on multi-turn conditioning
- Consistent refusal messaging without "helpful but dangerous" hedging

**Link to wiki**: [[jailbreak-policy-bypass|Jailbreak & Policy Bypass]]

### Tool Misuse Prevention

**What we measure**:
- Authorization enforcement: Can users invoke tools beyond their permissions
- Argument validation: Are tool parameters sanitized and validated
- Approval gates: Do sensitive operations require explicit user consent
- Rate limiting: Can attackers exhaust resources through tool abuse

**Test approach**:
- Attempt unauthorized tool invocations via prompt injection
- Inject malicious arguments (SQL injection, path traversal, command injection)
- Chain tool calls to achieve privilege escalation
- Validate least-privilege implementation

**Success criteria**:
- Zero unauthorized cross-user tool access
- Argument validation blocks injection attempts
- Sensitive operations require explicit approval
- Rate limits prevent resource exhaustion

**Link to wiki**: [[methodology/application-agent-boundary-overview|Tool Privilege Escalation]]

### Prompt Injection Resistance

**What we measure**:
- Instruction hierarchy: Does user input override system instructions
- Boundary enforcement: Are untrusted inputs separated from trusted context
- Indirect injection resilience: Do retrieved documents or external content corrupt behavior
- Persistence: Can injected instructions survive across conversation turns

**Test corpus**: 50+ injection patterns (direct override, role confusion, encoding tricks, multi-turn)

**Success criteria**:
- User input cannot access or modify system instructions
- Retrieved content processed as data, not executable instructions
- Injections flagged by monitoring and blocked

**Link to wiki**: [[prompt-injection|Prompt Injection]]

### Policy Enforcement Correctness

**What we measure**:
- False negatives: Harmful content that passes filters (exploit risk)
- False positives: Benign content incorrectly blocked (usability impact)
- Consistency: Same policy applied uniformly across contexts, languages, phrasings
- Adversarial robustness: Policy holds under paraphrasing, obfuscation, multi-turn attacks

**Test approach**: Red team the guardrails themselves using adversarial examples designed to expose gaps

**Reporting**: Include both bypass evidence (false negatives) and usability friction (false positives) for balanced risk assessment
