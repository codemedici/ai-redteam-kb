---
title: "Insecure Prompt Assembly"
tags:
  - type/technique
  - target/llm
  - target/rag-system
  - target/agent
  - access/inference
  - access/api
atlas: AML.T0051
owasp: LLM01
maturity: stub
created: 2026-02-14
updated: 2026-02-14
---
# Insecure Prompt Assembly

## Summary

Application constructs prompts insecurely, allowing injection or context manipulation. This technique involves exploiting vulnerabilities in how applications assemble prompts from multiple sources (system instructions, user input, retrieved context, etc.) without proper separation or validation, enabling attackers to inject malicious content that overrides intended behavior.

## Mechanism

*TODO: Describe how insecure prompt construction enables attacks. Cover:*
- Concatenation of untrusted input directly into prompts without sanitization
- Lack of clear boundaries between system instructions and user content  
- Failure to validate or escape special characters and control sequences
- Mixing trusted and untrusted data without proper delimiters
- Template injection when user input is embedded in prompt templates
- Context manipulation through carefully crafted inputs that alter prompt semantics

## Preconditions

- Application constructs prompts dynamically by combining multiple data sources
- User-controlled input is incorporated into prompts without validation
- No clear separation between system instructions and user content
- Application lacks input sanitization or adversarial input detection
- LLM processes assembled prompts without distinguishing trusted vs untrusted components

## Impact

**Business Impact:**
- Unauthorized access to functionality or data through prompt manipulation
- Policy bypass enabling prohibited actions or content generation
- Reputational damage from model generating harmful outputs
- Service disruption through maliciously crafted inputs
- Data exfiltration through context injection attacks

**Technical Impact:**
- System instructions overridden by user input
- Security controls bypassed through prompt injection
- Unintended model behavior triggered by context manipulation
- Downstream systems compromised through poisoned LLM outputs
- Confidential prompt templates or system instructions leaked

## Detection

- Unusual patterns in user inputs (control characters, special delimiters, instruction-like language)
- Outputs that violate expected behavior or policy boundaries
- Queries containing explicit instruction override attempts ("Ignore previous instructions")
- Statistical anomalies in input structure (token distributions, entropy patterns)
- Model responses that leak system prompt details or configuration
- Correlation between specific input patterns and policy violations

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet - file is stub)* | | |

## Mitigations

| ID | Name | Description |
|----|------|-------------|
| AML.M0004 | [[mitigations/input-validation-patterns]] | Validate and sanitize all user inputs before incorporating into prompts; detect adversarial patterns |
| AML.M0004 | [[mitigations/instruction-hierarchy-architecture]] | Structural separation between system instructions and user content prevents prompt override attempts |
| | [[mitigations/output-filtering-and-sanitization]] | Scan outputs for policy violations and leaked system information before delivery |

## Sources

*(No sources yet - this note is a stub awaiting enrichment)*
