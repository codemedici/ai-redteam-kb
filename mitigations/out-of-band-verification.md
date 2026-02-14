---
title: "Out-of-Band Verification"
tags:
  - type/mitigation
  - target/human
  - source/llms-in-cybersecurity
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Out-of-Band Verification

## Summary

Out-of-band verification requires confirming high-risk requests through a separate, trusted communication channel independent of the original request medium. When receiving urgent or sensitive requests via email, chat, or AI-generated messages, recipients verify authenticity by calling a known phone number, using a pre-established secure channel, or confirming through an independent system. This mitigation defeats LLM-powered phishing attacks that rely on single-channel deception, as attackers cannot simultaneously compromise multiple independent communication channels to maintain the illusion of legitimacy.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/llm-powered-phishing-and-social-engineering]] | Defeats phishing attacks by requiring verification through independent channel that attacker cannot control |
| | [[techniques/business-email-compromise]] | Prevents BEC fraud by requiring verbal or in-person confirmation of wire transfers and sensitive requests |

## Implementation

### Standard Operating Procedures

**Policy framework for out-of-band verification:**

**When to require verification:**
- **Financial transactions** above threshold (e.g., wire transfers >$10,000)
- **Urgent requests** claiming emergency or time pressure
- **Unusual requests** outside normal workflow or authority patterns
- **Sensitive data access** requests for credentials, PII, or confidential information
- **Account changes** (password resets, permission grants, account modifications)
- **Communications from executives** requesting immediate action

**How to verify:**

```markdown
## Out-of-Band Verification Checklist

1. **Identify the trusted contact method:**
   - Known phone number (from company directory, not from suspicious message)
   - In-person confirmation (if feasible)
   - Secure messaging platform with verified identity
   - Company intranet or verified internal system

2. **Do NOT use contact information from the suspicious message:**
   - Attackers include fake phone numbers in phishing emails
   - "Call this number to verify" in phishing email is still in-band

3. **Initiate contact yourself:**
   - Look up contact information independently
   - Use corporate directory, saved contacts, or verified sources
   - Never use links or numbers provided in suspicious communication

4. **Confirm request details:**
   - Describe the request: "I received an email asking me to..."
   - Verify specifics: amounts, recipients, urgency claims
   - Ask open-ended questions: "Did you send me a request about...?"

5. **Document verification:**
   - Record who you spoke with, when, and what was confirmed
   - Keep audit trail for financial transactions
   - Report suspicious requests to security team
```

### Technical Implementation for AI Systems

**LLM-powered assistants enforcing out-of-band verification:**

```python
class OutOfBandVerificationEnforcer:
    """
    Enforce out-of-band verification for high-risk agent actions
    
    Prevents LLM-powered social engineering by requiring
    confirmation through independent channel
    """
    
    HIGH_RISK_ACTIONS = [
        'wire_transfer',
        'grant_permissions',
        'reset_password',
        'delete_data',
        'send_sensitive_data',
        'approve_expenditure'
    ]
    
    VERIFICATION_THRESHOLDS = {
        'wire_transfer': {'amount_usd': 10000},
        'grant_permissions': {'privilege_level': 'admin'},
        'approve_expenditure': {'amount_usd': 5000}
    }
    
    def __init__(self, notification_service):
        self.notification_service = notification_service
        self.pending_verifications = {}
    
    def requires_verification(self, action, parameters):
        """
        Determine if action requires out-of-band verification
        
        Args:
            action: Action being requested
            parameters: Action parameters (e.g., amount, recipient)
        
        Returns:
            (requires: bool, reason: str)
        """
        if action not in self.HIGH_RISK_ACTIONS:
            return False, None
        
        # Check threshold-based requirements
        threshold = self.VERIFICATION_THRESHOLDS.get(action)
        if threshold:
            for key, limit in threshold.items():
                if parameters.get(key, 0) >= limit:
                    return True, f"{action} exceeds threshold: {key} >= {limit}"
        
        # High-risk actions always require verification
        return True, f"{action} is classified as high-risk"
    
    def request_verification(self, action, parameters, requester, approver):
        """
        Initiate out-of-band verification process
        
        Args:
            action: Action requiring verification
            parameters: Action parameters
            requester: User requesting action
            approver: Person who must verify (e.g., manager, account owner)
        
        Returns:
            Verification token (to be confirmed out-of-band)
        """
        # Generate verification token
        verification_token = generate_secure_token()
        
        # Create verification request
        verification_request = {
            'token': verification_token,
            'action': action,
            'parameters': parameters,
            'requester': requester,
            'approver': approver,
            'created_at': datetime.now(),
            'expires_at': datetime.now() + timedelta(hours=24),
            'status': 'pending',
            'verified_via': None
        }
        
        self.pending_verifications[verification_token] = verification_request
        
        # Send out-of-band notification
        self._send_verification_notification(verification_request)
        
        return verification_token
    
    def _send_verification_notification(self, request):
        """
        Send verification notification through independent channel
        
        Uses phone call, SMS to verified number, or secure authenticated app
        """
        approver_contact = self._get_verified_contact_info(request['approver'])
        
        message = f"""
VERIFICATION REQUIRED

Action: {request['action']}
Requester: {request['requester']}
Details: {self._format_parameters(request['parameters'])}

To approve, respond with verification code:
{request['token'][:8]}

To deny or report suspicious activity, contact Security.

This request expires in 24 hours.
"""
        
        # Send via multiple independent channels
        if approver_contact.get('phone'):
            self.notification_service.send_sms(
                to=approver_contact['phone'],
                message=message
            )
        
        if approver_contact.get('verified_email'):
            self.notification_service.send_email(
                to=approver_contact['verified_email'],
                subject="[ACTION REQUIRED] Verification Request",
                body=message,
                signed=True  # Cryptographic signature
            )
        
        # Also send to authenticated mobile app if available
        if approver_contact.get('mobile_app_id'):
            self.notification_service.push_notification(
                user_id=approver_contact['mobile_app_id'],
                title="Verification Required",
                body=message
            )
    
    def verify_action(self, verification_token, verification_method):
        """
        Confirm verification received through out-of-band channel
        
        Args:
            verification_token: Token from original request
            verification_method: How verification was received (phone, sms, app)
        
        Returns:
            Verification result
        """
        request = self.pending_verifications.get(verification_token)
        
        if not request:
            return {'status': 'invalid_token', 'message': 'Verification token not found'}
        
        if request['status'] != 'pending':
            return {'status': 'already_processed', 'message': 'Verification already processed'}
        
        if datetime.now() > request['expires_at']:
            request['status'] = 'expired'
            return {'status': 'expired', 'message': 'Verification request expired'}
        
        # Mark as verified
        request['status'] = 'verified'
        request['verified_at'] = datetime.now()
        request['verified_via'] = verification_method
        
        # Log security event
        log_security_event("out_of_band_verification_completed", {
            'action': request['action'],
            'requester': request['requester'],
            'approver': request['approver'],
            'verification_method': verification_method,
            'verification_latency_seconds': (datetime.now() - request['created_at']).total_seconds()
        })
        
        return {
            'status': 'verified',
            'message': 'Action approved via out-of-band verification',
            'token': verification_token
        }
    
    def _get_verified_contact_info(self, user_id):
        """
        Retrieve verified contact information from trusted source
        
        Must be from corporate directory, NOT user-provided or editable
        """
        # Query corporate identity system (LDAP, Active Directory, HR system)
        user_record = query_corporate_directory(user_id)
        
        return {
            'phone': user_record.verified_mobile_number,
            'verified_email': user_record.corporate_email,
            'mobile_app_id': user_record.authenticated_device_id
        }
    
    def _format_parameters(self, parameters):
        """Format action parameters for human readability"""
        formatted = []
        for key, value in parameters.items():
            formatted.append(f"{key}: {value}")
        return "\n".join(formatted)

# Integration with agent tool execution
verification_enforcer = OutOfBandVerificationEnforcer(notification_service)

@app.before_tool_execution
def enforce_out_of_band_verification(tool_name, arguments, user_context):
    """
    Check if tool requires out-of-band verification before execution
    """
    requires_verification, reason = verification_enforcer.requires_verification(
        tool_name,
        arguments
    )
    
    if requires_verification:
        # Initiate verification process
        verification_token = verification_enforcer.request_verification(
            action=tool_name,
            parameters=arguments,
            requester=user_context.user_id,
            approver=user_context.manager_id  # or determined by policy
        )
        
        # Block execution until verified
        raise VerificationRequiredError(
            f"This action requires out-of-band verification. "
            f"Verification request sent to {user_context.manager_id}. "
            f"Token: {verification_token[:8]}"
        )
```

### User Training Materials

**Employee training content:**

```markdown
## Recognizing LLM-Powered Phishing

### Red Flags Requiring Out-of-Band Verification

**Urgency language:**
- "Immediately"
- "Before end of day"
- "CEO needs this ASAP"
- "Time-sensitive"

**Unusual requests:**
- Requests outside normal workflow
- Requests from authority figures you rarely interact with
- Requests bypassing normal approval processes

**Perfect grammar (suspiciously polished):**
- No typos or grammatical errors (LLMs generate perfect text)
- Overly formal or flowery language
- Generic greetings ("Dear valued employee")

### Out-of-Band Verification Process

**DO:**
✅ Look up contact information independently (corporate directory)
✅ Call the requester on a known, verified number
✅ Ask open-ended questions ("What's this about?")
✅ Verify specific details of the request
✅ Document the verification (who, when, what confirmed)
✅ Report suspicious requests to security@company.com

**DON'T:**
❌ Use contact information from the suspicious message
❌ Click links or download attachments before verification
❌ Feel pressured by urgency claims
❌ Assume email address/caller ID proves identity
❌ Trust "verification codes" or "callback numbers" in the message

### Example Scenario

**Suspicious Email:**
> From: ceo@company.com
> Subject: URGENT: Wire Transfer Needed
> 
> I need you to process an urgent wire transfer to a new vendor for $45,000. Time-sensitive acquisition deal closing today. Use account details below and confirm via the verification line: +1-555-SCAM-NOW.

**Correct Response:**
1. ❌ Do NOT call the number in the email (attacker's number)
2. ✅ Look up CEO's verified phone number in corporate directory
3. ✅ Call CEO directly: "I received an email about a $45K wire transfer. Did you send this?"
4. ✅ If CEO says no → report to security immediately
5. ✅ If CEO says yes → follow standard wire transfer approval process (not bypassing controls)
```

## Limitations & Trade-offs

**Limitations:**

- **User compliance:** Requires employees to follow procedures even under pressure; social engineering may convince them to skip verification
- **Latency:** Adds delay to urgent legitimate requests (minutes to hours for verification)
- **Contact information trust:** Relies on corporate directory being accurate and secure; compromised directory undermines verification
- **Sophisticated attacks:** Coordinated multi-channel attacks may compromise both original and verification channels (rare but possible)

**Trade-offs:**

- **Security vs. Speed:** Verification adds friction to legitimate urgent requests
- **Usability vs. Protection:** Users may find verification burdensome and seek workarounds
- **Coverage vs. Overhead:** Lowering thresholds improves security but increases verification burden

**Bypass scenarios:**

- **Social engineering the verification:** Attacker convinces victim that verification process itself is suspicious
- **Compromised directory:** Attacker modifies corporate directory to include their phone number
- **Trust exploitation:** Victim knows requester personally and skips verification ("I know it's really them")

## Testing & Validation

**Functional Testing:**

1. **Threshold enforcement:** Verify high-risk actions trigger verification requirement
2. **Notification delivery:** Confirm out-of-band notifications reach approvers through independent channels
3. **Token validation:** Test that invalid, expired, or already-used tokens are rejected
4. **Contact lookup:** Verify corporate directory integration returns correct verified contact information

**Security Testing (Simulated Phishing):**

1. **Baseline compliance:** Measure verification procedure adherence before training
2. **Phishing simulation:** Send LLM-generated phishing emails with urgent requests
3. **Track responses:**
   - % who perform out-of-band verification
   - % who skip verification and comply with request
   - % who report suspicious message
4. **Post-training improvement:** Measure compliance after training

**User Awareness Validation:**

1. **Red team testing:** Security team sends simulated urgent requests from spoofed executive accounts
2. **Track verification rate:** What percentage of recipients verify via known phone number before acting?
3. **Feedback loop:** Provide immediate educational feedback to users who fail simulation
4. **Trend monitoring:** Track improvement in verification compliance over time

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Out-of-band verification: Call known number (not one in suspicious message) to verify urgent requests before acting."
> — [[sources/bibliography#LLMs in Cybersecurity]], p. 82-86

## Related

- **Combined with:** [[mitigations/shared-secrets-verification]], [[mitigations/approval-workflow-patterns]], [[mitigations/user-education-and-feedback]]
- **Defends against:** [[techniques/llm-powered-phishing-and-social-engineering]], [[techniques/business-email-compromise]]
