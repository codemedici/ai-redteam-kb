---
tags:
  - trust-boundary/general
  - type/defense
description: "Architectural pattern for cryptographically separating system instructions from user content to prevent prompt injection attacks."
---
# Instruction Hierarchy Architecture

## Summary

Instruction hierarchy architecture is a fundamental defensive pattern that creates cryptographic separation between trusted system instructions and untrusted user content in LLM applications. This control addresses the core vulnerability in LLM systems: the inability of models to reliably distinguish instructions from data. By enforcing structural boundaries and trust markers, this architecture prevents prompt injection attacks where malicious user input overrides or manipulates system behavior. This is a primary preventive control for prompt injection and related instruction manipulation attacks.

## Applicable Issues

This control addresses the following security issues:

- **[[attacks/prompt-injection|Prompt Injection]]**: Prevents direct and indirect prompt injection by maintaining clear separation between system and user content
- **[[attacks/system-prompt-leakage|System Prompt Leakage]]**: Protects system prompts from extraction by enforcing structural boundaries
- **[[attacks/jailbreak-policy-bypass|Jailbreak & Policy Bypass]]**: Prevents safety guardrail bypass by ensuring system instructions cannot be overridden
- **[[attacks/insecure-prompt-assembly|Insecure Prompt Assembly]]**: Provides secure prompt assembly patterns that prevent instruction blending

[[attacks/|See [Model Issues]] and [[attacks/|Application/Agent Runtime Issues]] for complete issue catalogs]

## Implementation Approach

**Core Components:**

1. **Structured Message Formats**
   - Use structured formats (ChatML, OpenAI format, Anthropic format) with explicit role markers
   - Implement special tokens or markers that distinguish system, user, and assistant content
   - Enforce format validation before content reaches the model
   - Prevent format manipulation through input sanitization

2. **Trust Boundary Enforcement**
   - Cryptographically sign or hash system instructions to detect tampering
   - Use immutable system prompt storage separate from user input
   - Implement prompt assembly middleware that enforces structure
   - Validate trust markers before model processing

3. **Content Labeling and Demarcation**
   - Clearly label all content sources (system, user, RAG-retrieved, web content)
   - Use visual or structural markers that models can recognize
   - Implement content provenance tracking
   - Separate trusted and untrusted content in prompt assembly

**Integration Points:**

- **Prompt Assembly Layer**: Integrate hierarchy enforcement into prompt construction pipeline
- **Input Processing**: Validate and structure user input before concatenation
- **RAG Integration**: Label retrieved content with trust markers
- **Model API**: Ensure model APIs respect structured formats

**Configuration:**

- **Format Selection**: Choose structured format compatible with model (ChatML, JSON, etc.)
- **Trust Marker Strategy**: Define approach for marking trusted vs. untrusted content
- **Validation Strictness**: Balance between security (strict) and flexibility (lenient)
- **Error Handling**: Define behavior when format violations are detected

## Architecture Considerations

**Design Pattern: Trust Boundary Separation**

Instruction hierarchy implements a trust boundary pattern where:
- System instructions are cryptographically or structurally protected
- User content is clearly demarcated and untrusted
- Model processing respects these boundaries
- Violations are detected and prevented

**System Integration:**

```
User Input → Format Validation → Trust Marker Injection → Prompt Assembly
                ↓                        ↓
         Structure Check         Content Labeling
                ↓                        ↓
         System Prompt (Trusted) + User Content (Untrusted) → Model
```

**Scalability:**

- **Performance Impact**: Structured formats add minimal overhead; validation can be optimized
- **Multi-Model Support**: Format abstraction layer supports different model APIs
- **RAG Integration**: Hierarchy must account for retrieved content from multiple sources
- **Streaming Responses**: Architecture must support streaming while maintaining boundaries

## Testing & Validation

**Functional Testing:**

1. **Format Enforcement**: Verify structured format is correctly applied to all prompts
2. **Trust Marker Validation**: Confirm trust markers are present and validated
3. **Boundary Preservation**: Test that system instructions cannot be overridden by user content
4. **RAG Integration**: Validate retrieved content is properly labeled and separated

**Security Testing:**

1. **Injection Attempts**: Attempt various prompt injection techniques to verify they fail
2. **Format Manipulation**: Try to break structured format through encoding or obfuscation
3. **Trust Marker Bypass**: Attempt to forge or remove trust markers
4. **Boundary Confusion**: Test scenarios where boundaries might be confused (multi-turn, summarization)

**Monitoring & Metrics:**

- **Format Violation Rate**: Track instances where format validation fails
- **Trust Marker Presence**: Monitor that all prompts include required trust markers
- **Injection Attempt Detection**: Alert on patterns indicating injection attempts
- **Boundary Violation Attempts**: Track attempts to override system instructions

## Limitations & Trade-offs

**Limitations:**

- **Model-Dependent Effectiveness**: Some models may not fully respect structured boundaries
- **Cannot prevent all injection**: Sophisticated attacks may find ways to confuse boundaries
- **Requires model support**: Architecture effectiveness depends on model's ability to process structured formats
- **RAG complexity**: Indirect injection via RAG requires additional labeling and validation

**Trade-offs:**

- **Security vs. Flexibility**: Strict hierarchy improves security but may limit prompt flexibility
- **Performance vs. Validation**: Comprehensive validation adds latency
- **Format Standardization vs. Model Diversity**: Different models may require different formats
- **Complexity vs. Usability**: Structured formats increase system complexity

**Bypass Scenarios:**

- **Model Limitations**: Models that don't fully understand structured formats may ignore boundaries
- **Format Confusion**: Attacks that exploit format parsing ambiguities
- **Multi-Turn Attacks**: Persistent injection across conversation turns
- **RAG Injection**: Indirect injection via retrieved content that bypasses user input validation

## Related Controls

This control works best when combined with:

- **[[defenses/output-filtering-and-sanitization|Output Filtering & Sanitization]]**: Filters outputs to remove any leaked system content
- **[[defenses/input-validation-patterns|Input Validation Patterns]]**: Validates user input before prompt assembly
- **[[defenses/dual-llm-judge-pattern|Dual-LLM Judge Pattern]]**: Validates outputs to ensure hierarchy was respected
- **[[defenses/anomaly-detection-architecture|Anomaly Detection Architecture]]**: Detects attempts to violate instruction hierarchy

[[defenses/|See [Controls Index]] for complete control catalog]

## Framework References

**NIST AI RMF:**
- MAP 2.1: Trustworthiness characteristics are identified
- MEASURE 2.3: AI system performance or assurance criteria are measured
- MANAGE 1.1: Determination of whether AI system achieves intended purpose

**OWASP LLM Top 10:**
- LLM01: Prompt Injection (instruction hierarchy is a primary mitigation)

**MITRE ATLAS:**
- Defensive techniques for preventing prompt injection and instruction manipulation

## References

- [[attacks/prompt-injection|Prompt Injection Issue]] - Detailed attack vectors and mitigations
- [[attacks/system-prompt-leakage|System Prompt Leakage Issue]] - Related attack patterns
- [[attacks/insecure-prompt-assembly|Insecure Prompt Assembly Issue]] - Secure assembly patterns
- [[methodology/phase-5-execution|Methodology: Phase 5 Execution]] - Testing approaches for instruction hierarchy
