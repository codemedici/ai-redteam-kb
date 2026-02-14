---
title: "Output Filtering And Sanitization"
tags:
  - type/mitigation
  - target/llm-app
  - target/agent
  - source/developers-playbook-llm
maturity: comprehensive
created: 2026-02-14
updated: 2026-02-14
---
# Output Filtering And Sanitization

## Summary

Post-generation filtering of LLM outputs to prevent harmful content, data leakage, and injection propagation. Based on **zero trust principles**: never trust LLM outputs, always verify and filter before exposing to users or downstream systems.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/insecure-prompt-assembly]] | Sanitizes LLM-generated content before use in downstream prompts |
| | [[techniques/insufficient-output-encoding]] | HTML-escapes and encodes outputs to prevent injection attacks |
| | [[techniques/jailbreak-policy-bypass]] | Toxicity filters catch harmful content generated through jailbreak attempts |
| | [[techniques/output-integrity-attack]] | Validates and filters outputs to prevent integrity violations |
| | [[techniques/pii-in-corpus]] | PII detection redacts sensitive data leaked from training corpus |
| | [[techniques/prompt-injection]] | Catches malicious content generated through prompt manipulation |
| | [[techniques/prompt-log-data-leakage]] | Redacts sensitive data from outputs before logging to prevent log-based data exposure |
| | [[techniques/sensitive-info-disclosure]] | PII redaction prevents disclosure of confidential information in outputs |
| | [[techniques/system-prompt-leakage]] | Filters outputs that attempt to reveal system prompts or instructions |
| | [[techniques/training-data-memorization]] | PII detection catches memorized sensitive data reproduced in outputs |
| | [[techniques/unauthorized-knowledge-disclosure]] | Filters LLM outputs to redact unauthorized information before delivery, catches cross-tenant or privilege-escalated knowledge leakage even if retrieval bypassed |
| | [[techniques/vector-embedding-weaknesses]] | Post-retrieval chunk sanitizer re-scans top-k chunks for PII or toxic content before inserting into context; catches unauthorized information before LLM generation |

## Implementation

> "Zero trust wasn't born out of science fiction but from a genuine need to revamp how we look at security. The model came into the limelight in 2009, thanks to John Kindervag of Forrester Research. Kindervag tossed out the conventional wisdom of 'trust but verify' and replaced it with something far more rigorous: **never trust, always verify**."
> 
> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 80

---

## Zero Trust Principles for LLM Applications

### Why Zero Trust for LLMs?

LLMs are fundamentally **untrusted entities** due to multiple vulnerabilities that cannot be fully eliminated through input-side controls alone:

> "Understanding these vulnerabilities is the first step in forming a comprehensive security strategy for LLMs based on the principles of zero trust. So, with these threats in mind, let's explore how adopting a zero trust architecture can protect us from the lurking dangers in the LLM ecosystem."
> 
> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 81

**Key threats requiring output filtering:**

1. **[[techniques/prompt-injection|Prompt Injection]]**
   - Direct and indirect manipulation of LLM behavior
   - Adversarial prompts bypass input validation
   - Output filtering catches malicious generated content

2. **[[techniques/sensitive-info-disclosure|Sensitive Information Disclosure]]**
   - LLM outputs confidential data from training corpus
   - Passwords, PII, proprietary information leakage
   - Output filtering redacts sensitive data before exposure

3. **Hallucination and Overreliance**
   - LLM fabricates plausible but false information
   - Users over-trust confident but inaccurate outputs
   - Output verification catches factual errors

4. **Toxic Output**
   - Chatbots generate harmful, offensive, or biased content
   - Historical examples: Tay, Lee Luda
   - Toxicity filters prevent harmful content delivery

> "Let's also not forget the issues we've seen with chatbots spewing toxic output. It's not just Tay and Lee Luda, whom we met in previous chapters; this problem has been persistent in chatbots and is something we must look for. You can't trust your chatbot to have good judgment or social graces."
> 
> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 81

---

### Kindervag's Zero Trust Fundamentals (Applied to LLMs)

**1. Secure all resources, everywhere**
> "This is like encrypting not just the UFO files but even the cafeteria menu. Every piece of data, whether internal or external, should be treated with the same level of security scrutiny."

**Application to LLMs:** Filter **all** LLM outputs regardless of source (trusted model, internal fine-tuned model, third-party API).

**2. Least privilege is the best privilege**
> "Mulder doesn't need access to the entire FBI database; he only needs what's relevant to his X-Files investigations. The same goes for anyone in a network—access should be role-specific and just enough to get the job done."

**Application to LLMs:** Limit LLM "agency"—restrict autonomous decision-making, database access, tool usage without human approval.

**3. The all-seeing eye**
> "In zero trust, every action is monitored and logged. Think of it as Scully skeptically watching every move Mulder makes. Constant monitoring allows for quick identification of any suspicious activity."

**Application to LLMs:** Log all LLM inputs and outputs for auditing, anomaly detection, incident response.

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 80

---

## Output Filtering Categories

### 1. PII Detection and Redaction

**Threat:** LLM outputs personally identifiable information from training data, user inputs, or database queries.

**PII Types to Filter:**
- Social Security numbers
- Credit card numbers
- Driver's license numbers
- Email addresses
- Phone numbers
- Home addresses
- Medical records
- Financial information

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 87

**Techniques:**

#### Regular Expressions (Pattern Matching)
> "The simplest method for detecting common forms of PII, such as emails, phone numbers, and Social Security numbers, is to use regular expressions to pattern match these items in text."

**Use Case:** Structured PII with predictable formats (SSNs, phone numbers, credit cards).

#### Named Entity Recognition (NER)
> "More advanced NLP techniques can identify entities like names, addresses, and other unique identifiers within text."

**Use Case:** Unstructured PII (names, addresses, organizations).

#### Dictionary-Based Matching
> "Scan for PII with a list of sensitive terms or identifiers. This method may be more prone to false positives."

**Use Case:** Domain-specific PII (employee IDs, project codenames).

#### Machine Learning Models
> "Train custom ML (machine learning) models to identify PII within a specific context, improving accuracy over time."

**Use Case:** Context-aware PII detection, reducing false positives.

#### Data Masking and Tokenization
> "These techniques replace identified PII with a placeholder or token, making the data useless for malicious purposes but still usable for system operations."

**Use Case:** Preserve output structure while removing sensitive values.

#### Contextual Analysis
> "This technique considers the surrounding text to decide whether a given string of characters represents PII, thereby reducing false positives."

**Use Case:** Disambiguate edge cases (e.g., "555-1234" in fictional context vs. real phone number).

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 87

---

### 2. Toxicity and Harmful Content Detection

**Threat:** LLM generates offensive, harmful, or biased content.

**Toxicity Categories:**
- Hate speech
- Profanity
- Sexual content
- Violent threats
- Harassment
- Discriminatory language

**Detection Approaches:**

**Keyword-based filtering:**
> "A list of commonly known offensive words, phrases, or sentiments that may indicate toxic content."

**Machine learning models:**
> "Custom models can be trained on a dataset labeled for toxicity to provide more nuanced detection. This approach can be especially important for words that are toxic only in specific situations."

**Commercial APIs:**
- OpenAI Moderation API
- Google Perspective API
- Azure Content Moderator

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 86

---

### 3. Preventing Unforeseen Code Execution

**Threat:** LLM outputs executable code (XSS, SQL injection, shell commands) that executes in downstream systems.

**Mitigation Strategies:**

**HTML Encoding:**
> "Before using LLM outputs in a web context, HTML-encode the content to neutralize any active code that could lead to XSS attacks."

**Safe Contextual Insertion:**
> "If the LLM output is part of a SQL query, ensure it's treated as data rather than executable code. Use prepared statements or parameterized queries to achieve this, mitigating SQL injection risks."

**Limit Syntax and Keywords:**
> "Institute a filtering layer that removes or escapes potentially dangerous programming language-specific syntax or keywords from the LLM's output."

**Disable Shell Interpretable Outputs:**
> "If the output interacts with shell commands, remove or escape characters with special meaning in shell scripting, limiting the chance of shell injection attacks."

**Tokenization:**
> "Tokenize the output and filter out unsafe tokens. For example, filter out `<script>` HTML tags or SQL commands like `DROP TABLE`."

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 88

---

## Working Code Examples (Python)

### Example 1: PII Detection with Regex (SSN)

Detects US Social Security numbers using pattern matching.

```python
import re

def contains_ssn(input_string):
    """
    Checks if a string contains a U.S. Social Security Number.
    
    Args:
        input_string: The text to check for SSNs.
    
    Returns:
        True if SSN found, False otherwise.
    """
    # Define regex pattern for SSN format: XXX-XX-XXXX
    ssn_pattern = r'\b\d{3}-\d{2}-\d{4}\b'
    
    # Search for the pattern in the input string
    match = re.search(ssn_pattern, input_string)
    
    # Check if a match was found
    if match:
        print(f"Found a Social Security Number: {match.group(0)}")
        return True
    else:
        print("No Social Security Number found.")
        return False

# Test the function
contains_ssn("My Social Security Number is 123-45-6789.")  # Found
contains_ssn("No number here!")  # Not found
```

**Note:** This is simple pattern matching and doesn't validate against invalid SSNs (e.g., `000-00-0000`). Extend with additional validation as needed.

**Alternative:** For full-featured PII detection, use commercial APIs like **Google Cloud Natural Language API** or **Amazon Comprehend** (costs may apply).

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 88-89

---

### Example 2: Toxicity Evaluation with OpenAI Moderation API

Checks toxicity of text using OpenAI's Moderation API.

```python
import openai

def check_toxicity(text):
    """
    Checks the toxicity of a text using the OpenAI Moderation API.
    
    Args:
        text: The text to check for toxicity.
    
    Returns:
        A toxicity score between 0 and 1, where a higher score indicates
        a higher probability of the text being toxic.
    """
    response = openai.Moderation.create(input=text)
    toxicity_score = response["results"][0]["confidence"]
    return toxicity_score

# Test the function
toxicity_score = check_toxicity("You are stupid.")
print(f"Toxicity score: {toxicity_score}")
```

**Threshold Guidance:** Choose threshold based on risk tolerance (e.g., 0.7 for moderate filtering, 0.5 for strict filtering).

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 89-90

---

### Example 3: End-to-End Output Filtering Workflow

Complete example combining toxicity check, PII detection, and logging.

```python
import openai
import re

# Initialize the OpenAI API client
openai.api_key = "your_openai_api_key_here"

def check_toxicity(text):
    """Check toxicity using OpenAI Moderation API."""
    response = openai.Moderation.create(input=text)
    toxicity_score = response["results"][0]["confidence"]
    return toxicity_score

def check_for_PII(text):
    """Check for SSN patterns in text."""
    ssn_pattern = r"\b\d{3}-\d{2}-\d{4}\b"
    return bool(re.search(ssn_pattern, text))

def get_LLM_response(prompt):
    """Get response from LLM."""
    model_engine = "text-davinci-002"  # Or other engine
    response = openai.Completion.create(
        engine=model_engine,
        prompt=prompt,
        max_tokens=100  # Limiting to 100 tokens for this example
    )
    return response.choices[0].text.strip()

def log_results(prompt, llm_output, is_safe):
    """Log all interactions to file for auditing."""
    with open("llm_safety_log.txt", "a") as log_file:
        log_file.write(f"Prompt: {prompt}\n")
        log_file.write(f"LLM Output: {llm_output}\n")
        log_file.write(f"Is Safe: {is_safe}\n")
        log_file.write("=" * 50 + "\n")

if __name__ == "__main__":
    prompt = "Tell me your thoughts on universal healthcare."
    
    # Get LLM response
    llm_output = get_LLM_response(prompt)
    
    # Check toxicity and PII
    toxicity_level = check_toxicity(llm_output)
    contains_PII = check_for_PII(llm_output)
    
    # Determine safety
    is_safe = True
    if toxicity_level > 0.7 or contains_PII:
        print("Warning: The output is not safe to return to the user.")
        is_safe = False
    else:
        print("The output is safe to return to the user.")
    
    # Log results
    log_results(prompt, llm_output, is_safe)
```

> **Important:** Remember to log all interactions to and from your LLM! This will be important for debugging, security auditing, and regulatory compliance.
> 
> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 90

---

### Example 4: HTML Sanitization for XSS Prevention

Sanitize LLM output before rendering in web context.

```python
import html

def sanitize_output(text):
    """
    Sanitize output for safe HTML rendering.
    
    Args:
        text: The LLM output to sanitize.
    
    Returns:
        HTML-escaped text safe for web display.
    """
    return html.escape(text)

# Example usage
unsafe_output = '<script>alert("XSS")</script>Hello!'
safe_output = sanitize_output(unsafe_output)
print(safe_output)  # Output: &lt;script&gt;alert(&quot;XSS&quot;)&lt;/script&gt;Hello!
```

**Note:** This is the simplest possible version. Add additional sanitization based on your needs (e.g., CSS injection prevention, URL validation).

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 91

---

### Example 5: Integrated Workflow with Sanitization

Complete workflow including sanitization step.

```python
if __name__ == "__main__":
    prompt = "Tell me your thoughts on universal healthcare."
    
    # Get LLM response
    llm_output = get_LLM_response(prompt)
    
    # Check toxicity and PII
    toxicity_level = check_toxicity(llm_output)
    contains_PII = check_for_PII(llm_output)
    
    # Determine safety and sanitize if safe
    is_safe = True
    if toxicity_level > 0.7 or contains_PII:
        print("Warning: The output is not safe to return to the user.")
        is_safe = False
    else:
        print("The output is safe to return to the user.")
        # Sanitize for web context
        llm_output = sanitize_output(llm_output)
    
    # Log results
    log_results(prompt, llm_output, is_safe)
```

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 92

---

## Implementation Best Practices

### 1. Layered Defense

**Apply multiple filters in sequence:**
1. PII detection and redaction
2. Toxicity evaluation
3. Code execution prevention (HTML encoding, SQL parameterization)
4. Custom business logic filters (domain-specific harmful content)

**Rationale:** No single filter is perfect. Defense-in-depth catches what individual filters miss.

---

### 2. Logging and Auditing

> "Remember to log all interactions to and from your LLM! This will be important for debugging, security auditing, and regulatory compliance."

**What to log:**
- Timestamp
- User/session ID
- Input prompt
- Raw LLM output
- Filter results (toxicity score, PII detected, etc.)
- Safety decision (safe/unsafe)
- Final sanitized output delivered to user

**Use cases:**
- Incident response and forensics
- Regulatory compliance (GDPR, HIPAA)
- Model behavior analysis
- Continuous improvement of filters

> Source: [[sources/bibliography#Developer's Playbook for LLM Security]], p. 90

---

### 3. Threshold Tuning

**Balance false positives vs. false negatives:**
- **Strict threshold (e.g., toxicity > 0.5):** Fewer harmful outputs, more false positives (legitimate content blocked)
- **Relaxed threshold (e.g., toxicity > 0.8):** Fewer false positives, more harmful outputs slip through

**Recommendation:** Start strict, tune based on user feedback and log analysis.

---

### 4. Continuous Monitoring and Improvement

**Monitor filter effectiveness:**
- Track false positive rate (safe content blocked)
- Track false negative rate (harmful content delivered)
- Analyze edge cases from logs

**Iterate:**
- Refine regex patterns for PII
- Retrain custom toxicity models
- Add domain-specific filter rules

---

### 5. Context-Aware Filtering

**Different risk profiles require different filters:**
- **Public-facing chatbot:** Strict toxicity filtering, comprehensive PII redaction
- **Internal code assistant:** Relaxed toxicity filtering (technical language), allow code output but sanitize before execution
- **Medical chatbot:** Strict medical misinformation filtering, HIPAA-compliant PII handling

**Customize filters based on:**
- User role (internal employee vs. external customer)
- Application domain (finance, healthcare, entertainment)
- Regulatory requirements (GDPR, HIPAA, CCPA)

---

### 6. Pessimistic Trust Boundary Definition (Prompt Injection Defense)

**Core principle:** Treat all outputs from LLM as **inherently untrusted** when the LLM processes untrusted input data (user prompts, external documents, RAG content).

> "Solid guidance exists for preventing SQL injection and, when followed, it can be 100% effective. But prompt injection mitigation strategies are more like phishing defenses than they are like SQL injection defenses."
> 
> Source: [[sources/developers-playbook-llm]], p. 35

Prompt injection defense is fundamentally different from SQL injection defense—there is no 100% effective pattern like parameterized queries. Instead, **pessimistic trust boundaries** assume every LLM output could be malicious when inputs are untrusted.

**Why Pessimistic Trust Matters:**

LLMs lack the ability to reliably distinguish between:
- Legitimate instructions and adversarial manipulation
- Safe outputs and dangerous commands
- Describing an action and executing it

**Therefore:** Even with input validation, structural prompts, and adversarial training, **assume outputs may still be compromised**.

**Implementation Approach:**

**1. Comprehensive Output Filtering**
- **Scrutinize all generated text** for malicious content before delivering to users or downstream systems
- **Pattern matching:** Detect command injection, SQL injection, path traversal in generated code
- **Semantic analysis:** Use secondary LLM or classifier to identify harmful intent in outputs
- **Content policy validation:** Ensure outputs comply with acceptable use policies

**Example:**
```python
def filter_llm_output(output_text):
    # Detect SQL injection patterns in generated code
    if re.search(r"(DROP|DELETE|INSERT|UPDATE)\s+(TABLE|DATABASE)", output_text, re.IGNORECASE):
        raise SecurityException("Output contains SQL modification commands")
    
    # Detect command injection
    if re.search(r"(os\.system|subprocess|eval|exec)\s*\(", output_text):
        raise SecurityException("Output contains dangerous code execution patterns")
    
    # Detect path traversal
    if "../" in output_text or "..%5C" in output_text:
        raise SecurityException("Output contains path traversal patterns")
    
    # Additional filtering...
    return output_text
```

**2. Least Privilege for LLM Backend Access**
- **Restrict LLM permissions:** Grant LLM service accounts minimum necessary database/API access
- **Read-only by default:** LLM should query data but not modify without explicit approval
- **Sandboxed tool execution:** Run LLM-generated code in isolated environments
- **No direct access to production systems:** Proxy all LLM interactions through secure middleware

**Example Architecture:**
```
LLM Output → Validation Layer → Privilege Check → Restricted Sandbox → Production
                   ↓                  ↓                  ↓
             Filter Dangerous    Verify LLM        Isolate from
                Content         Authorized         Prod Systems
```

**3. Human-in-the-Loop Controls for Dangerous Actions**
- **Manual validation required** before executing:
  - Financial transactions
  - Data modifications (DELETE, UPDATE operations)
  - External communications (emails, API calls)
  - Privilege changes
  - Irreversible operations

**Example Approval Flow:**
```python
def execute_llm_generated_action(action):
    if is_dangerous_action(action):
        approval_token = request_human_approval(action)
        if not approval_token.is_approved():
            raise SecurityException("Action requires human approval")
    
    # Proceed with execution only after approval
    execute_with_audit_log(action)
```

**4. Output Encoding for Downstream Systems**
- **HTML escape:** Before rendering LLM outputs in web pages (prevent XSS)
- **SQL parameterization:** Never concatenate LLM outputs into SQL queries
- **Command sanitization:** Validate LLM-generated shell commands before execution
- **API input validation:** Treat LLM outputs as untrusted when calling external APIs

**Comparison: Phishing Defense vs. SQL Injection Defense**

| Defense Type | SQL Injection | Prompt Injection (Phishing-Like) |
|--------------|---------------|----------------------------------|
| **Effectiveness** | 100% effective with parameterized queries | No 100% effective pattern exists |
| **Approach** | Technical control (prepared statements) | Defense-in-depth (multiple layers) |
| **Attack evolution** | Well-understood, stable attack surface | Constantly evolving, creative attacks |
| **User involvement** | None (technical mitigation) | User awareness + technical controls |
| **Trust model** | Never trust user input | Never trust LLM output when input untrusted |

**Philosophy: Acknowledge Complexity, Assume Potential Harm**

Because prompt injection defense cannot be perfect:
- **Accept that bypasses will occur** — design for resilience when they do
- **Limit blast radius** — pessimistic trust boundaries contain damage from compromised outputs
- **Monitor and respond** — detect anomalous outputs and trigger incident response
- **Continuous improvement** — update filters based on real-world attack patterns

**Advantages:**

- **Forces rigorous output filtering/sanitization:** Nothing reaches production without validation
- **Limits LLM "agency":** Prevents dangerous autonomous operations without approval
- **Reduces attack surface:** Even if input-side defenses fail, output-side controls catch malicious content
- **Defense-in-depth:** Combines with input validation, structured prompts, monitoring for layered security

**Trade-offs:**

- **Increased complexity:** More filtering and approval gates add system complexity
- **Performance overhead:** Output validation adds latency to LLM responses
- **Potential false positives:** Overly strict filtering may block legitimate outputs
- **User experience impact:** Approval workflows slow down operations

**Verdict:**

Pessimistic trust boundaries are **essential for high-risk LLM applications** where outputs can trigger dangerous actions (financial transactions, data modifications, external communications). By treating LLM outputs as inherently untrustworthy, organizations reduce risk even when sophisticated prompt injection attacks bypass input-side defenses.

> "Pessimistic Trust Boundary Definition: Core principle: Treat all outputs from LLM as inherently untrusted when taking in untrusted data as prompts. Advantages: Forces rigorous output filtering/sanitization; Limits 'agency' granted to LLM (prevents dangerous operations without approval). Implementation: (1) Comprehensive output filtering — scrutinize generated text for malicious content, (2) Least privilege — restrict LLM backend access, (3) Human-in-the-loop controls — require manual validation for dangerous actions. Philosophy: Acknowledge complexity of defense; assume every output is potentially harmful."
> 
> Source: [[sources/developers-playbook-llm]], p. 39-40

---

## Limitations & Trade-offs

**Limitations:**

1. **Imperfect Detection:**
   - Regex misses PII in non-standard formats
   - NER models have false positives/negatives
   - Toxicity models struggle with sarcasm, context-dependent language
   - Adversarial prompts can evade filters

2. **Performance Overhead:**
   - API calls to toxicity services add latency (OpenAI Moderation, Perspective API)
   - Complex regex matching on large outputs increases processing time
   - ML model inference for NER adds computational cost

3. **False Positives Impact User Experience:**
   - Medical terminology flagged as toxic
   - Phone numbers in legitimate business context redacted unnecessarily
   - Code examples HTML-escaped inappropriately for technical contexts

**Trade-offs:**
- **Security vs. Latency:** Comprehensive filtering adds response time delays
- **Strictness vs. Usability:** Aggressive filtering blocks legitimate content, frustrating users
- **Accuracy vs. Cost:** Advanced ML-based filters more accurate but expensive to run at scale
- **Coverage vs. Complexity:** More filter types improve security but increase system complexity

**Bypass Scenarios:**
- Adversarial encoding (Base64, hex, character substitution) evades pattern matching
- Context manipulation tricks toxicity classifiers (sarcasm, irony, technical language)
- Novel PII formats not covered by existing regex patterns
- Low-confidence harmful content slips through threshold-based filters

**Mitigation:** Defense-in-depth (multiple filter layers), human-in-the-loop for high-risk decisions, continuous filter improvement based on bypass analysis.

## Testing & Validation

**Functional Testing:**
1. **PII Detection Coverage:** Validate all common PII formats detected (SSN, credit cards, emails, phone numbers)
2. **Toxicity Classification Accuracy:** Test with known toxic and benign content, measure false positive/negative rates
3. **HTML Sanitization:** Verify XSS payloads properly escaped before rendering
4. **Filter Performance:** Measure latency impact of each filter stage
5. **Logging Completeness:** Confirm all inputs/outputs logged with correct PII redaction

**Security Testing:**
1. **Bypass Testing:** Attempt to evade filters using encoding, obfuscation, novel formats
2. **False Negative Rate:** Submit known harmful content and measure detection rate
3. **Adversarial Prompts:** Test jailbreak techniques and verify outputs still filtered
4. **Edge Cases:** Test context-dependent content (medical terms, technical jargon) for over-filtering
5. **Integration Testing:** Validate filters work correctly in production pipeline

**Monitoring & Continuous Improvement:**
- Track false positive reports from users
- Analyze bypassed harmful content from incident logs
- Measure filter effectiveness metrics over time (precision, recall)
- A/B test new filter rules before production deployment
- Review edge cases quarterly and update patterns

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Zero trust wasn't born out of science fiction but from a genuine need to revamp how we look at security. The model came into the limelight in 2009, thanks to John Kindervag of Forrester Research. Kindervag tossed out the conventional wisdom of 'trust but verify' and replaced it with something far more rigorous: **never trust, always verify**."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 80

> "Understanding these vulnerabilities is the first step in forming a comprehensive security strategy for LLMs based on the principles of zero trust. So, with these threats in mind, let's explore how adopting a zero trust architecture can protect us from the lurking dangers in the LLM ecosystem."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 81

> "Let's also not forget the issues we've seen with chatbots spewing toxic output. It's not just Tay and Lee Luda, whom we met in previous chapters; this problem has been persistent in chatbots and is something we must look for. You can't trust your chatbot to have good judgment or social graces."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 81

> "The simplest method for detecting common forms of PII, such as emails, phone numbers, and Social Security numbers, is to use regular expressions to pattern match these items in text."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 87

> "Remember to log all interactions to and from your LLM! This will be important for debugging, security auditing, and regulatory compliance."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 90

> "Remember, Fox Mulder trusted no one at the start of the X-Files series. It was his fundamental mantra. However, he found people he could trust over time... However, he never lost his sense of paranoia, and the need to investigate and verify kept him alive through many perils."
> — [[sources/bibliography#Developer's Playbook for LLM Security]], p. 92

**Full chapter reference:** The Developer's Playbook for LLM Security (Wilson, O'Reilly 2024), Chapter 7: Trust No One, p. 79-92
