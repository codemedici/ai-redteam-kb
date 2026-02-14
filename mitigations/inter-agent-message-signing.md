---
title: "Inter-Agent Message Signing"
tags:
  - type/mitigation
  - target/agent
  - source/ai-native-llm-security
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Inter-Agent Message Signing

## Summary

Inter-agent message signing uses cryptographic signatures to verify the authenticity and integrity of messages exchanged between agents in multi-agent systems, preventing identity impersonation and manipulation of agent coordination logic. By requiring agents to sign all inter-agent communications and verify signatures before processing messages, this mitigation ensures that agents cannot be tricked into believing requests come from trusted peers with elevated permissions or authority they don't actually possess.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/agentic-ai-security-risks-owasp-aivss]] | Prevents agent identity impersonation and multi-agent exploitation through cryptographic verification |
| | [[techniques/prompt-injection]] | Prevents injected instructions from masquerading as legitimate inter-agent messages |
| | [[techniques/agent-based-genai-security]] | Secures machine-to-machine communication between LAMs through mutual authentication and data integrity checks |

## Implementation

### Cryptographic Signing Infrastructure

**Generate signing keys for each agent:**

```python
from cryptography.hazmat.primitives import hashes, serialization
from cryptography.hazmat.primitives.asymmetric import rsa, padding
from cryptography.hazmat.backends import default_backend
from dataclasses import dataclass
from datetime import datetime
import json
import base64

@dataclass
class AgentIdentity:
    """Agent cryptographic identity"""
    agent_id: str
    public_key: bytes
    private_key: bytes  # Stored securely, never transmitted
    
    @classmethod
    def generate(cls, agent_id: str):
        """Generate new agent identity with RSA keypair"""
        private_key = rsa.generate_private_key(
            public_exponent=65537,
            key_size=2048,
            backend=default_backend()
        )
        
        public_key = private_key.public_key()
        
        # Serialize keys
        private_pem = private_key.private_bytes(
            encoding=serialization.Encoding.PEM,
            format=serialization.PrivateFormat.PKCS8,
            encryption_algorithm=serialization.NoEncryption()
        )
        
        public_pem = public_key.public_bytes(
            encoding=serialization.Encoding.PEM,
            format=serialization.SubjectPublicKeyInfo
        )
        
        return cls(
            agent_id=agent_id,
            public_key=public_pem,
            private_key=private_pem
        )

class AgentMessageSigner:
    """Sign and verify inter-agent messages"""
    
    def __init__(self, agent_identity: AgentIdentity):
        self.agent_identity = agent_identity
        
        # Load private key for signing
        self.private_key = serialization.load_pem_private_key(
            agent_identity.private_key,
            password=None,
            backend=default_backend()
        )
    
    def sign_message(self, message: dict) -> dict:
        """
        Sign message with agent's private key
        
        Args:
            message: Message payload (dict)
        
        Returns:
            Signed message with signature field
        """
        # Add metadata
        message_to_sign = {
            **message,
            "sender_id": self.agent_identity.agent_id,
            "timestamp": datetime.now().isoformat(),
            "nonce": generate_nonce()  # Prevent replay attacks
        }
        
        # Serialize message
        message_bytes = json.dumps(message_to_sign, sort_keys=True).encode('utf-8')
        
        # Generate signature
        signature = self.private_key.sign(
            message_bytes,
            padding.PSS(
                mgf=padding.MGF1(hashes.SHA256()),
                salt_length=padding.PSS.MAX_LENGTH
            ),
            hashes.SHA256()
        )
        
        # Return signed message
        return {
            **message_to_sign,
            "signature": base64.b64encode(signature).decode('utf-8')
        }

class AgentMessageVerifier:
    """Verify signed inter-agent messages"""
    
    def __init__(self, public_key_registry: dict):
        """
        Initialize verifier with public key registry
        
        Args:
            public_key_registry: Maps agent_id -> public_key (PEM format)
        """
        self.public_key_registry = public_key_registry
        self.verified_nonces = set()  # Prevent replay attacks
    
    def verify_message(self, signed_message: dict) -> dict:
        """
        Verify message signature and authenticity
        
        Args:
            signed_message: Signed message with signature field
        
        Returns:
            Verified message payload (without signature)
        
        Raises:
            MessageVerificationError if signature invalid
        """
        # Extract fields
        sender_id = signed_message.get("sender_id")
        signature_b64 = signed_message.get("signature")
        nonce = signed_message.get("nonce")
        
        if not all([sender_id, signature_b64, nonce]):
            raise MessageVerificationError("Missing required signature fields")
        
        # Check for replay attack
        if nonce in self.verified_nonces:
            raise MessageVerificationError(f"Replay attack detected: nonce {nonce} already seen")
        
        # Get sender's public key
        public_key_pem = self.public_key_registry.get(sender_id)
        if not public_key_pem:
            raise MessageVerificationError(f"Unknown sender: {sender_id}")
        
        public_key = serialization.load_pem_public_key(
            public_key_pem,
            backend=default_backend()
        )
        
        # Reconstruct message for verification
        message_copy = {k: v for k, v in signed_message.items() if k != "signature"}
        message_bytes = json.dumps(message_copy, sort_keys=True).encode('utf-8')
        
        # Verify signature
        signature = base64.b64decode(signature_b64)
        
        try:
            public_key.verify(
                signature,
                message_bytes,
                padding.PSS(
                    mgf=padding.MGF1(hashes.SHA256()),
                    salt_length=padding.PSS.MAX_LENGTH
                ),
                hashes.SHA256()
            )
        except Exception as e:
            raise MessageVerificationError(f"Signature verification failed: {e}")
        
        # Record nonce to prevent replay
        self.verified_nonces.add(nonce)
        
        # Clean old nonces periodically (keep only last 10k)
        if len(self.verified_nonces) > 10000:
            # In production: use time-based expiration instead
            self.verified_nonces = set(list(self.verified_nonces)[-5000:])
        
        log_security_event("message_verified", {
            "sender_id": sender_id,
            "timestamp": message_copy.get("timestamp")
        })
        
        return message_copy

def generate_nonce() -> str:
    """Generate random nonce for replay protection"""
    import secrets
    return secrets.token_hex(16)
```

### Multi-Agent Communication Layer

**Integrate signing into agent communication:**

```python
from typing import Callable

class SecureAgentCommunicator:
    """Secure communication layer for multi-agent systems"""
    
    def __init__(self, agent_identity: AgentIdentity, public_key_registry: dict):
        self.signer = AgentMessageSigner(agent_identity)
        self.verifier = AgentMessageVerifier(public_key_registry)
        self.agent_id = agent_identity.agent_id
    
    def send_message(self, recipient_id: str, message_type: str, payload: dict):
        """
        Send signed message to another agent
        
        Args:
            recipient_id: Target agent ID
            message_type: Message type (e.g., "task_request", "data_share")
            payload: Message payload
        """
        # Create message
        message = {
            "recipient_id": recipient_id,
            "message_type": message_type,
            "payload": payload
        }
        
        # Sign message
        signed_message = self.signer.sign_message(message)
        
        # Transmit to recipient
        self._transmit_message(recipient_id, signed_message)
        
        log_communication("message_sent", {
            "from": self.agent_id,
            "to": recipient_id,
            "type": message_type
        })
    
    def receive_message(self, signed_message: dict) -> dict:
        """
        Receive and verify signed message from another agent
        
        Args:
            signed_message: Signed message from sender
        
        Returns:
            Verified message payload
        
        Raises:
            MessageVerificationError if verification fails
        """
        # ✅ VERIFY SIGNATURE
        try:
            verified_message = self.verifier.verify_message(signed_message)
        except MessageVerificationError as e:
            log_security_event("message_verification_failed", {
                "recipient": self.agent_id,
                "error": str(e)
            })
            raise
        
        # Check message is actually for this agent
        if verified_message.get("recipient_id") != self.agent_id:
            raise MessageVerificationError(
                f"Message addressed to {verified_message.get('recipient_id')}, "
                f"not {self.agent_id}"
            )
        
        log_communication("message_received", {
            "from": verified_message["sender_id"],
            "to": self.agent_id,
            "type": verified_message.get("message_type")
        })
        
        return verified_message
    
    def _transmit_message(self, recipient_id: str, message: dict):
        """Transmit message to recipient (implementation varies by transport)"""
        # Implementation depends on message transport (queue, API, etc.)
        pass
```

### Workflow Signing for Multi-Agent Orchestration

**Sign workflow manifests to prevent manipulation:**

```python
class WorkflowManifest:
    """Signed workflow definition for multi-agent orchestration"""
    
    def __init__(self, workflow_id: str, steps: list, orchestrator_id: str):
        self.workflow_id = workflow_id
        self.steps = steps
        self.orchestrator_id = orchestrator_id
        self.created_at = datetime.now()
    
    def to_dict(self) -> dict:
        """Serialize workflow for signing"""
        return {
            "workflow_id": self.workflow_id,
            "steps": self.steps,
            "orchestrator_id": self.orchestrator_id,
            "created_at": self.created_at.isoformat()
        }

class SignedWorkflowEnforcer:
    """Enforce signed workflow execution in multi-agent systems"""
    
    def __init__(self, signer: AgentMessageSigner, verifier: AgentMessageVerifier):
        self.signer = signer
        self.verifier = verifier
    
    def create_signed_workflow(self, workflow: WorkflowManifest) -> dict:
        """
        Create signed workflow manifest
        
        Orchestrator signs workflow to prevent manipulation
        """
        workflow_dict = workflow.to_dict()
        return self.signer.sign_message(workflow_dict)
    
    def execute_step(self, signed_workflow: dict, step_index: int):
        """
        Execute workflow step with signature verification
        
        Agents verify workflow signature before executing steps
        """
        # ✅ VERIFY WORKFLOW SIGNATURE
        verified_workflow = self.verifier.verify_message(signed_workflow)
        
        # Check orchestrator authorization
        orchestrator_id = verified_workflow["orchestrator_id"]
        if not self._is_authorized_orchestrator(orchestrator_id):
            raise WorkflowAuthorizationError(
                f"Agent {orchestrator_id} not authorized to orchestrate workflows"
            )
        
        # Execute step
        steps = verified_workflow["steps"]
        if step_index >= len(steps):
            raise WorkflowError(f"Step index {step_index} out of range")
        
        step = steps[step_index]
        return self._execute_workflow_step(step)
    
    def _is_authorized_orchestrator(self, orchestrator_id: str) -> bool:
        """Check if agent is authorized to orchestrate workflows"""
        # Implementation: check against authorized orchestrator registry
        return True  # Placeholder
    
    def _execute_workflow_step(self, step: dict):
        """Execute individual workflow step"""
        # Implementation varies by step type
        pass
```

## Limitations & Trade-offs

**Limitations:**

- **Cannot prevent authorized agent misuse:** If agent's private key is compromised, attacker can sign malicious messages
- **Key management complexity:** Secure storage and rotation of private keys adds operational overhead
- **Performance impact:** Cryptographic operations add latency to inter-agent communication
- **Does not prevent logic errors:** Correctly signed messages may still contain flawed or malicious instructions

**Trade-offs:**

- **Security vs. Performance:** Signing/verification adds computational overhead to every message
- **Key rotation vs. Availability:** Frequent key rotation improves security but risks disrupting active workflows
- **Centralized vs. Distributed:** Centralized key registry is simpler but creates single point of failure
- **Replay protection granularity:** Storing all nonces provides strong replay protection but consumes memory

**Bypass Scenarios:**

- **Private key compromise:** If attacker obtains agent's private key, they can forge signed messages
- **Public key registry manipulation:** If attacker can modify the public key registry, they can substitute malicious keys
- **Timing attacks:** Side-channel attacks on signature verification may leak information
- **Nonce database overflow:** If nonce storage is limited, old nonces may be reused (replay attack)

## Testing & Validation

**Functional Testing:**

1. **Signature Verification:** Verify that unsigned messages are rejected
2. **Replay Protection:** Confirm that replayed messages with valid signatures are detected and blocked
3. **Key Rotation:** Test that agents can update keys without disrupting operations
4. **Workflow Signing:** Verify that workflow manifests require valid orchestrator signatures

**Security Testing:**

1. **Forged Signatures:** Attempt to forge signatures without access to private key
2. **Signature Stripping:** Test if signature verification can be bypassed by removing signature field
3. **Nonce Reuse:** Attempt replay attacks with previously valid nonces
4. **Public Key Substitution:** Test if attacker can substitute public keys in registry

**Monitoring & Metrics:**

- **Verification Failures:** Track frequency of signature verification failures
- **Replay Attempts:** Alert on detected replay attacks
- **Key Usage:** Monitor which agents are signing/verifying messages
- **Performance Impact:** Measure latency added by signing operations

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Cryptographic signing/token-based verification of all inter-agent messages prevents identity impersonation in multi-agent systems."
> — [[sources/bibliography#AI-Native LLM Security]], pp. 359, 364

> "Signed workflow manifests verified against trust root prevent manipulation of coordination logic in multi-agent exploitation attacks."
> — [[sources/bibliography#AI-Native LLM Security]], pp. 358, 363-364

> "The scenario where multiple LAMs work together or interact with other systems necessitates secure machine to machine communication. Implementing secure protocols, mutual authentication, cross domain access mapping and control, and data integrity checks are key in this context."
> — [[sources/bibliography#Generative AI Security]], p. 216 (§7.4.2)

## Related

- **Combines with:** [[mitigations/workload-identity-spiffe]], [[mitigations/token-lifecycle-management]]
- **Complements:** [[mitigations/agent-memory-isolation]], [[mitigations/telemetry-and-monitoring-architecture]]
