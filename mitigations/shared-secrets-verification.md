---
title: "Shared Secrets Verification"
tags:
  - type/mitigation
  - target/human
  - source/llms-in-cybersecurity
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Shared Secrets Verification

## Summary

Shared secrets verification uses pre-arranged code words, phrases, or challenge-response protocols known only to legitimate parties to verify identity during high-risk communications. When receiving urgent or sensitive requests, recipients challenge the requester with a question only the real person would know, or request a pre-established code word that proves authenticity. This mitigation is particularly effective against LLM-powered phishing and deepfake attacks because attackers cannot access private, out-of-band information shared between trusted parties, even if they can perfectly clone communication style, voice, or appearance.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/llm-powered-phishing-and-social-engineering]] | Defeats AI-generated phishing by requiring knowledge that only legitimate sender possesses |
| | [[techniques/business-email-compromise]] | Prevents BEC fraud through challenge-response verification of executive requests |

## Implementation

### Pre-Established Code Words

**Organizational implementation:**

```markdown
## Shared Secret Protocol for Executive Communications

### Setup Phase

**For each executive-employee relationship:**

1. **Establish emergency code word:**
   - In-person meeting or secure video call
   - Choose memorable but uncommon word/phrase
   - Document in secure password manager (encrypted)
   - Never transmit code word via email or insecure channels

2. **Establish verification challenge-response:**
   - Personal question only executive would know answer to
   - Example: "What was the topic of our last 1-on-1?" 
   - Answer stored securely by employee

3. **Define usage policy:**
   - Code word required for: wire transfers, urgent financial requests, sensitive data requests
   - Challenge-response required for: unusual requests, after-hours communications
   - Mandatory training on when to request code word

### Usage Example

**Scenario: Suspicious urgent request**

Employee receives:
> From: CEO <ceo@company.com>
> Subject: URGENT: Wire Transfer Needed
> 
> I need you to initiate a $75,000 wire transfer immediately...

Employee response:
> I'd be happy to help. For verification, can you please provide the code word we established for urgent financial requests?

**Legitimate CEO:**
> The code word is "pineapple express"

**Attacker:**
> What code word? Just do it, it's urgent!
[or]
> I don't remember setting up a code word. Can you just proceed?
[or]
> I'm traveling and don't have access to that information right now.

→ Employee recognizes failure to provide code word and escalates to security team.
```

**Technical implementation for agent systems:**

```python
class SharedSecretVerification:
    """
    Manage shared secrets for authentication
    
    Used by AI agents to verify human requester identity
    before executing sensitive actions
    """
    
    def __init__(self, secret_store):
        self.secret_store = secret_store  # Encrypted secret storage
    
    def establish_shared_secret(self, user_id, agent_id, secret_type='code_word'):
        """
        Establish shared secret between user and agent
        
        Args:
            user_id: User establishing secret
            agent_id: Agent that will verify secret
            secret_type: Type of secret (code_word, challenge_response, passphrase)
        
        Returns:
            Secret ID for future reference
        """
        if secret_type == 'code_word':
            secret_value = self._generate_code_word()
            
            # Present to user (in-person or secure channel only)
            display_to_user_securely(user_id, {
                'type': 'code_word',
                'value': secret_value,
                'usage': 'Provide this word when requesting high-risk agent actions',
                'warning': 'Never share via email or insecure channels'
            })
        
        elif secret_type == 'challenge_response':
            challenge = self._generate_challenge_question(user_id)
            response = prompt_user_securely(user_id, challenge)
            secret_value = {'challenge': challenge, 'response': response}
        
        # Store encrypted
        secret_id = self.secret_store.store_encrypted(
            user_id=user_id,
            agent_id=agent_id,
            secret_type=secret_type,
            secret_value=secret_value,
            created_at=datetime.now()
        )
        
        log_security_event("shared_secret_established", {
            'user_id': user_id,
            'agent_id': agent_id,
            'secret_type': secret_type,
            'secret_id': secret_id
        })
        
        return secret_id
    
    def verify_shared_secret(self, user_id, agent_id, provided_secret):
        """
        Verify provided secret against stored secret
        
        Args:
            user_id: User providing secret
            agent_id: Agent verifying secret
            provided_secret: Secret value provided by user
        
        Returns:
            Verification result
        """
        # Retrieve stored secret
        stored_secret = self.secret_store.retrieve(
            user_id=user_id,
            agent_id=agent_id
        )
        
        if not stored_secret:
            return {
                'verified': False,
                'reason': 'no_shared_secret_established',
                'action': 'reject_request'
            }
        
        # Compare secrets (case-insensitive for code words)
        if stored_secret['type'] == 'code_word':
            match = provided_secret.lower().strip() == stored_secret['value'].lower()
        
        elif stored_secret['type'] == 'challenge_response':
            # Agent asks challenge, user provides response
            match = provided_secret.lower().strip() == stored_secret['value']['response'].lower()
        
        else:
            match = provided_secret == stored_secret['value']
        
        if match:
            log_security_event("shared_secret_verified", {
                'user_id': user_id,
                'agent_id': agent_id,
                'verification_method': stored_secret['type']
            })
            
            return {
                'verified': True,
                'message': 'Shared secret verified successfully'
            }
        else:
            log_security_event("shared_secret_verification_failed", {
                'user_id': user_id,
                'agent_id': agent_id,
                'provided_value_hash': hashlib.sha256(provided_secret.encode()).hexdigest(),
                'severity': 'high'
            })
            
            return {
                'verified': False,
                'reason': 'secret_mismatch',
                'action': 'reject_and_alert'
            }
    
    def _generate_code_word(self):
        """Generate memorable but secure code word"""
        # Use word list of uncommon but memorable words
        word_list = [
            'pineapple', 'elephant', 'telescope', 'harmonica',
            'volcano', 'butterfly', 'carousel', 'saxophone'
        ]
        
        # Combine two words for higher entropy
        word1 = random.choice(word_list)
        word2 = random.choice([w for w in word_list if w != word1])
        
        return f"{word1} {word2}"
    
    def _generate_challenge_question(self, user_id):
        """Generate personal challenge question"""
        # In production, derive from user profile or past interactions
        challenges = [
            "What project did we discuss in our last meeting?",
            "What was the last document you asked me to summarize?",
            "What's your preferred communication channel for urgent matters?",
            "What city were you in when we last spoke?"
        ]
        
        return random.choice(challenges)

# Integration with agent tool execution
secret_verifier = SharedSecretVerification(encrypted_secret_store)

@app.before_high_risk_action
def require_shared_secret(action, user_id, agent_id):
    """
    Require shared secret verification before high-risk action
    
    High-risk actions: financial transactions, data deletion, permission grants
    """
    # Request secret from user
    user_message = f"""
This action requires verification. Please provide:
1. Your established code word, OR
2. Answer to verification question: {stored_secret['challenge']}

Your response:
"""
    
    provided_secret = get_user_response(user_message)
    
    # Verify
    verification_result = secret_verifier.verify_shared_secret(
        user_id=user_id,
        agent_id=agent_id,
        provided_secret=provided_secret
    )
    
    if not verification_result['verified']:
        raise AuthenticationFailedError(
            "Shared secret verification failed. Action blocked."
        )
    
    # Proceed with action if verified
    return verification_result
```

### Personal Challenge Questions

**Individual implementation:**

```markdown
## Personal Verification Protocol

### Family/Personal Relationships

**For verifying family members in emergencies:**

1. **Establish personal facts only you both know:**
   - "What's our family's favorite vacation spot?"
   - "What did we nickname your car?"
   - "What's your mother's maiden name?" (classic but still effective)
   - "What restaurant did we go to for your birthday?"

2. **Use in emergency scenarios:**
   - Unexpected phone calls requesting money
   - Urgent travel emergency claims
   - Medical emergency financial requests

3. **Example exchange:**

   Suspicious call: "Mom, I'm in trouble. I need you to wire money immediately."
   
   Verification: "Before I do anything, tell me: What's the name we gave to our cat when you were 10?"
   
   Legitimate child: "Mr. Whiskers"
   
   Scammer: "I don't remember, I'm upset right now..." [fails verification]

### Professional Relationships

**For executive-assistant or manager-employee:**

1. **Recent context questions:**
   - "What did we discuss in our last 1-on-1?"
   - "What project are you currently focused on?"
   - "Who attended the meeting we had yesterday?"

2. **Behavioral patterns:**
   - "You usually email me before calling. Why are you calling directly?"
   - "You never request wire transfers after hours. Is this really you?"

3. **Mutual acquaintances:**
   - "What's our CFO's name?" (tests if caller actually works at company)
   - "Who reports to me?" (tests organizational knowledge)
```

### Passphrase Systems

**More sophisticated challenge-response:**

```python
class ChallengeResponseSystem:
    """
    Implement challenge-response authentication using passphrases
    
    More sophisticated than simple code words
    """
    
    def __init__(self):
        self.challenge_bank = self._load_challenge_bank()
    
    def generate_challenge(self, user_id):
        """
        Generate authentication challenge
        
        Returns random challenge from pre-established bank
        """
        user_challenges = self.challenge_bank.get(user_id, [])
        
        if not user_challenges:
            raise ValueError(f"No challenges established for user {user_id}")
        
        # Select random challenge
        challenge = random.choice(user_challenges)
        
        return {
            'challenge_id': challenge['id'],
            'question': challenge['question'],
            'expected_response_hash': challenge['response_hash']
        }
    
    def verify_response(self, challenge_id, provided_response):
        """
        Verify provided response against expected response
        
        Uses hash comparison to avoid storing plaintext secrets
        """
        challenge = self._get_challenge_by_id(challenge_id)
        
        # Hash provided response
        provided_hash = hashlib.sha256(
            provided_response.lower().strip().encode()
        ).hexdigest()
        
        # Compare hashes
        if provided_hash == challenge['expected_response_hash']:
            log_security_event("challenge_response_success", {
                'challenge_id': challenge_id
            })
            return {'verified': True}
        else:
            log_security_event("challenge_response_failure", {
                'challenge_id': challenge_id,
                'severity': 'high'
            })
            return {'verified': False, 'action': 'reject_and_alert'}
    
    def establish_challenge_bank(self, user_id, qa_pairs):
        """
        Establish bank of challenge-response pairs for user
        
        Args:
            user_id: User establishing challenges
            qa_pairs: List of (question, answer) tuples
        
        Setup must be done in person or via secure authenticated session
        """
        challenges = []
        
        for question, answer in qa_pairs:
            challenge = {
                'id': generate_challenge_id(),
                'question': question,
                'response_hash': hashlib.sha256(
                    answer.lower().strip().encode()
                ).hexdigest(),
                'created_at': datetime.now()
            }
            challenges.append(challenge)
        
        self.challenge_bank[user_id] = challenges
        self._persist_challenge_bank()
        
        log_security_event("challenge_bank_established", {
            'user_id': user_id,
            'challenge_count': len(challenges)
        })
```

## Limitations & Trade-offs

**Limitations:**

- **Memory dependency:** Users must remember code words or answers; forgotten secrets block legitimate access
- **Social engineering vulnerability:** Sophisticated attackers may trick users into revealing secrets
- **Setup overhead:** Requires initial secure setup session; doesn't scale to large organizations without automation
- **Rotation complexity:** Changing secrets requires out-of-band communication
- **Single point of failure:** If secret is compromised (written down, shared insecurely), security is lost

**Trade-offs:**

- **Security vs. Usability:** Strong secrets are hard to remember; simple secrets are easier to guess
- **Coverage vs. Burden:** Requiring secrets for many scenarios improves security but frustrates users
- **Simplicity vs. Sophistication:** Code words are simple but less secure than challenge-response systems

**Attack scenarios:**

- **Social engineering the secret:** Attacker tricks user into revealing code word ("For security audit, please confirm your code word is...")
- **Written secrets:** User writes down code word insecurely (sticky note, unencrypted file)
- **Phishing the setup:** Attacker intercepts initial secret establishment process

## Testing & Validation

**Functional Testing:**

1. **Secret storage:** Verify secrets encrypted at rest and in transit
2. **Verification accuracy:** Test correct secrets accepted, incorrect rejected
3. **Challenge randomization:** Verify random challenge selection from bank
4. **Lockout policy:** Test account lockout after multiple failed verification attempts

**Security Testing (Social Engineering Simulations):**

1. **Code word requests:** Test if users will provide code words when asked directly
2. **Reset requests:** Test if users will reset/share secrets via email (should refuse)
3. **Urgent bypass requests:** Test if users skip verification under time pressure

**User Awareness:**

1. **Pre-training baseline:** Measure code word usage before training
2. **Phishing simulation:** Send urgent requests without code words, track compliance
3. **Post-training improvement:** Measure increased verification requests after training

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Shared secrets: Pre-arranged code words for urgent requests. Defeats LLM-powered phishing as attackers cannot access private out-of-band information."
> — [[sources/bibliography#LLMs in Cybersecurity]], p. 82-86

## Related

- **Combined with:** [[mitigations/out-of-band-verification]], [[mitigations/approval-workflow-patterns]], [[mitigations/user-education-and-feedback]]
- **Defends against:** [[techniques/llm-powered-phishing-and-social-engineering]], [[techniques/business-email-compromise]]
