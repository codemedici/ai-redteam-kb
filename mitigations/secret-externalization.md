---
title: "Secret Externalization"
tags:
  - type/mitigation
  - target/llm
  - target/agent
  - source/ai-native-llm-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Secret Externalization

## Summary

Secret externalization is a build-time defensive pattern that removes all sensitive material—API keys, JWTs, database connection strings, RBAC lists, tenant IDs—from LLM system prompts and stores them in dedicated secret management infrastructure. The system prompt is reduced to behavioral directives only, following the principle of least context: the model receives only what it needs to act, never the credentials it needs to act *with*. Secret-bearing logic moves into stateless microservices (MCP servers, OpenAI function backends) that the model invokes by name; the model sees only JSON schemas, never the secrets behind them.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0051.002 | [[techniques/system-prompt-leakage]] | Eliminates secrets from prompts so extraction yields only behavioral directives, not credentials or sensitive configuration |
| | [[techniques/secrets-in-prompts-and-logs]] | Prevents secrets from appearing in prompt text, context windows, or log captures |

## Implementation

### Principle of Least Context

Strip system prompts to behavioral directives only. Replace inline secrets with tool invocations:

**Before (vulnerable):**
```
You are a payment assistant. Use the Stripe key sk-live-xxx to process charges.
The database connection string is postgres://admin:secret@db.internal:5432/prod.
```

**After (externalized):**
```
You are a payment assistant. To process a charge, call tool `payment_charge`.
To look up order history, call tool `order_lookup`.
```

The model never sees credentials—it only knows tool names and their JSON schemas.

### Secret Storage Architecture

| Layer | Control | Implementation |
|-------|---------|----------------|
| **Storage** | Centralized secret vault | HashiCorp Vault, Azure Key Vault, AWS KMS — secrets stored encrypted at rest |
| **Injection** | Runtime sidecar pattern | Secrets injected into tool microservices at startup via sidecar or init container; never baked into images or prompt files |
| **Tool hardening** | MCP servers / OpenAI functions | Secret-bearing logic runs in stateless microservices; model sees only JSON schema, never underlying credentials |
| **Encryption** | Envelope encryption | AES-256 + KMS CMK; decrypt inside confidential-computing enclave where supported |
| **Rotation** | Automated credential rotation | Secrets rotate on schedule and immediately on suspected compromise |

### Architecture Pattern

```
User Query → LLM (behavioral prompt only)
                    ↓ tool call
              Tool Gateway (validates request)
                    ↓
              Microservice (secret injected at runtime from Vault)
                    ↓
              External API / Database
```

### Key Metrics

- **Mean-time-to-externalize-secret**: Hours from secret commit to removal from prompt (target <24h)
- **Secrets-in-prompt count**: Number of credentials or sensitive values still inline in any system prompt (target: 0)

## Limitations & Trade-offs

**Limitations:**
- **Increased architectural complexity**: Requires secret management infrastructure, tool microservices, and injection pipelines
- **Latency overhead**: Tool calls through microservices add round-trip time vs. inline credentials
- **Operational burden**: Secret rotation, vault management, and access policies require ongoing maintenance
- **Behavioral directives still leak**: Even without secrets, extracted prompts reveal application logic, tool names, and filtering rules

**Trade-offs:**
- **Security vs. Simplicity**: Externalization is more secure but adds deployment complexity
- **Completeness vs. Practicality**: Some context (tool descriptions, behavioral rules) must remain in the prompt and can still be valuable reconnaissance

## Testing & Validation

1. **Prompt audit**: Scan all system prompts for credential patterns (regex for API keys, connection strings, tokens)
2. **Red team extraction**: Attempt system prompt extraction; verify no secrets appear in leaked content
3. **Secret scanning CI gate**: Integrate secret detection (e.g., TruffleHog, GitLeaks patterns) into prompt deployment pipeline
4. **Rotation verification**: Confirm that leaked credentials (if any historical) have been rotated and are no longer valid

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Store keys in Vault (HashiCorp/Azure KMS), inject at runtime via sidecar; never bake into image or prompt file. Strip to behavioral directives only; replace 'here is the Stripe key: sk-xxx' with 'call tool payment_charge'. Move secret-bearing logic into stateless microservices; model sees only JSON schema."
> — [[sources/bibliography#AI-Native LLM Security]], p. 346-351

> "Envelope encryption (AES-256 + KMS CMK); decrypt inside confidential-computing enclave."
> — [[sources/bibliography#AI-Native LLM Security]], p. 346-351
