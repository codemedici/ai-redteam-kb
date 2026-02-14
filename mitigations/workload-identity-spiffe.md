---
title: "Workload Identity with SPIFFE/SPIRE"
tags:
  - type/mitigation
  - target/agent
  - source/securing-ai-agents
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Workload Identity with SPIFFE/SPIRE

## Summary

Workload identity is a modern identity framework that replaces traditional secret-based authentication with platform-attested, dynamically-issued identity. Instead of managing and distributing static credentials (API keys, client secrets), workload identity derives identity from verifiable facts about the workload's runtime environment. This approach is fundamental to securing autonomous AI agents, which are dynamic, ephemeral entities that traditional IAM systems (OAuth, SAML) were never designed to handle.

SPIFFE (Secure Production Identity Framework for Everyone) and SPIRE (SPIFFE Runtime Environment) provide the open-source standard and implementation for workload identity, enabling Zero Trust security for agent-to-agent and agent-to-service communication.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| AML.T0051 | [[techniques/agent-identity-crisis]] | Provides cryptographically verifiable identity for dynamic, ephemeral agents without secret sprawl |

## Implementation

### Core Principle: Platform Attestation Over Secret Possession

**Paradigm shift:** Don't trust the workload to tell you who it is; ask the platform it's running on.

**Traditional approach (broken for agents):**
- Agent presents pre-placed secret (API key, client_id + client_secret)
- Secret management nightmare for thousands of ephemeral agents
- Compromised secret = keys to kingdom
- No way to verify agent's actual runtime context

**Workload identity approach:**
- Agent doesn't possess secret
- Platform (Kubernetes, cloud provider, VM hypervisor) provides cryptographically verifiable attestation
- Identity document (SVID) dynamically issued based on verified platform evidence
- Short-lived, automatically rotated credentials

### SPIFFE/SPIRE Architecture

**SPIFFE** (Secure Production Identity Framework for Everyone):
- Open-source standard for workload identity
- Universal, platform-agnostic
- Defines standard for verifiable identity document (SVID): X.509 certificates or JWTs
- Standard API (Workload API) for requesting SVIDs

**SPIRE** (SPIFFE Runtime Environment):
- Production-ready implementation of SPIFFE
- Two components: SPIRE Server and SPIRE Agent

### Kubernetes Implementation Example

**Deployment architecture:**
1. **SPIRE Server:** Cluster-wide, authoritative identity issuer
   - Maintains registration of valid workloads and their identity policies
   - Signs SVIDs for authenticated agents
   - Integrates with certificate authority (can be self-signed or external CA)

2. **SPIRE Agent:** DaemonSet on every Kubernetes node
   - Detects new pods on node
   - Performs platform attestation
   - Proxies SVID requests to SPIRE Server
   - Delivers SVIDs to workloads via Unix domain socket

**Attestation workflow:**

1. **Discovery:** AI agent pod starts on K8s node, SPIRE Agent (DaemonSet) detects it via kubelet API

2. **Attestation:** SPIRE Agent queries Kubernetes API server for pod metadata:
   - Namespace
   - Service account
   - Labels and annotations
   - Node identity
   - Platform provides cryptographically verifiable proof (signed by K8s API server)

3. **SVID issuance:** 
   - SPIRE Agent forwards attestation evidence to SPIRE Server
   - SPIRE Server evaluates against registration policy
   - If authorized, issues short-lived SVID (X.509 certificate or JWT)
   - SVID contains SPIFFE ID (e.g., `spiffe://trust-domain/ns/default/sa/financial-agent`)

4. **Identity established:** 
   - Agent receives cryptographically verifiable identity document
   - No pre-placed secrets required
   - SVID automatically rotated before expiration

**SVID lifetime:** Typically 5 minutes to 1 hour (configurable), dramatically reducing blast radius of compromise.

### Registration Policy Example

```yaml
# SPIRE Server registration entry for financial analysis agent
Entry {
  spiffe_id = "spiffe://acme.com/ns/finance/sa/quarterly-analysis-agent"
  parent_id = "spiffe://acme.com/k8s-node"
  selectors = [
    "k8s:ns:finance",
    "k8s:sa:quarterly-analysis-agent",
    "k8s:pod-label:app:financial-analysis"
  ]
  ttl = 3600  # 1 hour SVID lifetime
}
```

**Selectors:** Platform-specific criteria that must match for SVID issuance. Agent cannot forge these - verified by platform.

### Integration with Authorization Systems

**mTLS with SVIDs:**
- Agent uses X.509 SVID for mutual TLS authentication
- Service validates SVID against trusted SPIRE CA
- SPIFFE ID extracted from certificate's URI SAN field
- Authorization decision based on verified SPIFFE ID

**JWT SVIDs for API authentication:**
- Agent requests JWT SVID for specific audience (e.g., database API)
- JWT contains claims: SPIFFE ID, audience, expiration
- API service validates JWT signature against SPIRE Server's JWKS endpoint
- Authorization based on verified identity claims

**Dynamic, task-specific credentials:**
- Parent agent spawns sub-agent for temporary task
- Sub-agent gets its own SVID with restricted SPIFFE ID
- Sub-agent's SVID has shorter TTL (e.g., 5 minutes)
- Automatic cleanup when task completes - no manual credential revocation

### Cloud Provider Integration

**AWS IAM Roles Anywhere:**
- SPIRE SVID (X.509) used to assume AWS IAM role
- No long-lived AWS access keys needed
- Agent gets temporary AWS credentials via STS AssumeRole with certificate

**Azure Managed Identity + SPIRE:**
- SPIRE provides base identity
- Azure Managed Identity provides cloud-scoped credentials
- Federated identity binding between SPIFFE ID and Azure AD

**GCP Workload Identity:**
- SPIRE SVID mapped to GCP service account
- No GCP service account key files
- Automatic credential projection into pods

### Audit and Attribution

**Cryptographic certainty:**
- Every action attributed to specific SPIFFE ID
- Platform attestation proves agent origin
- Tamper-proof audit trail

**Structured logging:**
```json
{
  "timestamp": "2026-02-14T18:30:00Z",
  "action": "database.read",
  "spiffe_id": "spiffe://acme.com/ns/finance/sa/quarterly-analysis-agent",
  "attestation": {
    "platform": "kubernetes",
    "node": "worker-node-5",
    "namespace": "finance",
    "pod": "quarterly-analysis-7f9a3c"
  },
  "resource": "sales_table",
  "result": "success"
}
```

**Benefits:**
- Know exactly which agent performed action
- Can trace back to platform-verified identity
- Detect anomalies (e.g., agent accessing unexpected resources)
- Incident response: identify compromised agent's scope

## Limitations & Trade-offs

**Operational complexity:**
- Requires running SPIRE infrastructure (server + agents on every node)
- Platform-specific attestation plugins (Kubernetes, AWS, Azure, GCP)
- Certificate rotation and CA management
- Learning curve for security and ops teams

**Platform dependency:**
- Relies on platform's integrity for attestation
- If Kubernetes API server compromised, attestation unreliable
- Need secure boot and node attestation to establish platform trust root

**Not a complete solution:**
- Workload identity solves *authentication* (who is the agent?)
- Still need *authorization* system (what can agent do?)
- Still need behavioral monitoring (is agent acting maliciously despite valid identity?)

**Performance considerations:**
- SVID rotation creates periodic overhead
- mTLS handshakes slightly slower than unencrypted connections
- SPIRE Server is single point of failure (requires HA deployment)

**Migration burden:**
- Legacy systems may not support SPIFFE/mTLS
- May need to run hybrid model (SPIRE + traditional secrets) during transition
- Requires updating all services to validate SVIDs

## Testing & Validation

**SVID issuance verification:**
1. Deploy test agent pod with known selectors
2. Verify SPIRE Agent detects pod and performs attestation
3. Confirm SVID issued with correct SPIFFE ID
4. Validate SVID contains expected claims (namespace, service account)
5. Verify SVID signed by trusted SPIRE CA

**Rotation validation:**
1. Deploy agent with short SVID TTL (e.g., 2 minutes)
2. Monitor SVID rotation before expiration
3. Verify no service interruption during rotation
4. Confirm old SVID rejected after expiration

**Negative testing:**
1. Attempt to forge SPIFFE ID (should fail - platform attestation prevents)
2. Tamper with pod labels (SVID should not be issued for mismatched selectors)
3. Replay old SVID (should be rejected after expiration)
4. Test SPIRE Server failure scenarios (agent should use cached SVID until server recovery)

**Authorization integration:**
1. Configure service to accept only specific SPIFFE IDs
2. Test agent with authorized SPIFFE ID (should succeed)
3. Test agent with unauthorized SPIFFE ID (should be rejected)
4. Verify audit logs capture SPIFFE ID for all requests

**Cloud integration testing:**
1. Verify agent can assume AWS IAM role using SVID
2. Confirm temporary AWS credentials issued
3. Test credential expiration and renewal
4. Validate no long-lived cloud credentials exist in agent environment

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "The principle of workload identity is that identity should be derived from verifiable facts about the workload's environment, not from a secret it possesses. This is the core of a Zero Trust approach: don't trust the workload to tell you who it is; ask the platform it's running on."
> — [[sources/bibliography#Securing AI Agents]], p. 54

> "SPIFFE (Secure Production Identity Framework for Everyone) is an open-source standard for workload identity that is universal, platform-agnostic, and defines a standard for a verifiable identity document called an SVID (SPIFFE Verifiable Identity Document) and a standard API (the Workload API) for workloads to request SVIDs."
> — [[sources/bibliography#Securing AI Agents]], p. 54-55

> "The key mechanism in SPIFFE is platform attestation. Instead of the agent presenting a secret, the SPIRE Agent queries the trusted platform to gather verifiable evidence about the agent's origin... The SPIRE Agent queries the Kubernetes API server for metadata about the pod - its namespace, service account, labels - and the platform provides cryptographically verifiable proof."
> — [[sources/bibliography#Securing AI Agents]], p. 55
