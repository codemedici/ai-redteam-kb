---
title: "Developer Security Training"
tags:
  - type/mitigation
  - target/llm
  - target/code
  - target/developers
  - source/llms-in-cybersecurity
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---

# Developer Security Training

## Summary

Developer security training educates engineering teams on secure coding practices, common vulnerability patterns, and security-first development principles to prevent introduction of security flaws during implementation. When applied to LLM-assisted development, security training must specifically address unique risks of code-generating AI tools: that LLMs are trained on historical, insecure codebases and systematically suggest deprecated patterns, outdated libraries, and known-vulnerable code structures. Training establishes a "verify, don't trust" culture where developers critically evaluate LLM suggestions rather than blindly accepting them, treating AI-generated code with the same scrutiny as externally-sourced code.

## Defends Against

| ID | Technique | Description |
|----|------|-------------|
| | [[techniques/llm-code-generation-vulnerabilities]] | Educated developers recognize and reject insecure LLM code suggestions |
| AML.T0020 | [[techniques/data-poisoning-attacks]] | Training on supply chain risks helps developers identify backdoored code patterns |
| | [[techniques/backdoor-poisoning]] | Awareness of common backdoor patterns (hardcoded credentials, admin bypasses) enables detection |
| | [[techniques/third-party-model-risks]] | Understanding of AI model risks drives careful evaluation of LLM-generated code |

## Implementation

### Training Program Structure

**1. Foundational Security Training (All Developers)**

**Core Topics:**
- **OWASP Top 10** - Common web application vulnerabilities
- **Secure coding principles** - Least privilege, defense in depth, fail securely
- **Input validation** - Sanitization, parameterization, encoding
- **Cryptography basics** - When to use what algorithms, key management
- **Authentication & authorization** - Session management, access controls
- **Data protection** - Encryption at rest/transit, PII handling

**Delivery:**
- 8-hour initial workshop (in-person or virtual)
- Quarterly 1-hour refresher sessions
- Self-paced online modules (Pluralsight, Coursera, etc.)
- Hands-on labs (Hack The Box, PortSwigger Web Security Academy)

**Assessment:**
- Written exam (70% passing score)
- Practical coding challenge (fix vulnerable code)
- Annual recertification required

**2. LLM-Specific Security Training (Developers Using AI Tools)**

**Specialized Topics:**

**A. Understanding LLM Training Biases**

Content:
- How LLMs are trained on historical codebases
- Why outdated patterns are over-represented
- Examples of deprecated libraries LLMs frequently suggest
- Statistical bias toward "popular but old" code

**Lab Exercise:**
```python
# Exercise: Identify security issues in LLM-generated code

# LLM-generated authentication function
def authenticate_user(username, password):
    import hashlib
    
    # Hash password with MD5
    hashed = hashlib.md5(password.encode()).hexdigest()
    
    # Query database
    query = f"SELECT * FROM users WHERE username='{username}' AND password='{hashed}'"
    result = db.execute(query)
    
    return result.fetchone() is not None

# Task: Identify ALL security issues
# 1. _______________
# 2. _______________
# 3. _______________
```

**Answer Key:**
1. MD5 is deprecated for password hashing (use bcrypt/Argon2)
2. SQL injection via string concatenation (use parameterized queries)
3. Plain password transmitted/logged (should be handled securely)

**B. Common LLM Vulnerability Patterns**

Training content by vulnerability class:

**Injection Flaws**
```python
# ❌ LLM often suggests (VULNERABLE)
query = f"SELECT * FROM products WHERE id = {product_id}"

# ✅ Secure alternative
query = "SELECT * FROM products WHERE id = ?"
cursor.execute(query, (product_id,))
```

**Insecure Deserialization**
```python
# ❌ LLM often suggests (VULNERABLE)
import pickle
data = pickle.loads(user_input)

# ✅ Secure alternative
import json
data = json.loads(user_input)
```

**Weak Cryptography**
```python
# ❌ LLM often suggests (VULNERABLE)
import hashlib
hash = hashlib.md5(password.encode()).hexdigest()

# ✅ Secure alternative
import bcrypt
hash = bcrypt.hashpw(password.encode(), bcrypt.gensalt())
```

**Path Traversal**
```python
# ❌ LLM often suggests (VULNERABLE)
filename = request.args.get('file')
with open(f'/uploads/{filename}', 'r') as f:
    content = f.read()

# ✅ Secure alternative
from pathlib import Path
base = Path('/uploads')
requested = (base / filename).resolve()
if not requested.is_relative_to(base):
    raise ValueError("Invalid path")
with open(requested, 'r') as f:
    content = f.read()
```

**C. Verify, Don't Trust: LLM Code Review Checklist**

Mandatory review process for AI-generated code:

```markdown
## LLM Code Review Checklist

Before accepting LLM-generated code, verify:

### Input Handling
- [ ] All user inputs are validated and sanitized
- [ ] SQL queries use parameterization (no string concatenation)
- [ ] Command execution uses argument arrays, not shell strings
- [ ] File paths are validated against directory traversal

### Cryptography
- [ ] No MD5 or SHA1 for security purposes
- [ ] Password hashing uses bcrypt, Argon2, or scrypt
- [ ] Random values use `secrets` module, not `random`
- [ ] AES uses GCM or CBC with HMAC, not ECB

### Serialization
- [ ] JSON/MessagePack used instead of pickle
- [ ] XML parsing has entity expansion disabled
- [ ] YAML uses safe_load, not full_load

### Dependencies
- [ ] All suggested libraries are current versions
- [ ] No deprecated packages
- [ ] Dependencies scanned for CVEs
- [ ] License compatibility verified

### Authentication/Authorization
- [ ] No hardcoded credentials
- [ ] No admin bypass "for debugging"
- [ ] Session tokens are cryptographically random
- [ ] Authorization checks present on protected operations

### Error Handling
- [ ] No sensitive data in error messages
- [ ] Exceptions don't expose stack traces to users
- [ ] Failures default to secure state (fail closed, not open)
```

**3. Role-Specific Training**

**Senior Developers / Code Reviewers**

Additional content:
- Recognizing subtle security flaws
- Advanced exploitation techniques
- Secure architecture patterns
- Threat modeling for LLM-assisted projects

**DevOps / SRE**

Additional content:
- Securing CI/CD pipelines against AI-generated code
- Runtime security monitoring
- Incident response for LLM-related vulnerabilities
- Infrastructure-as-code security

**Security Champions**

Additional content:
- Red teaming LLM code suggestions
- Building custom security linters
- Training delivery and mentorship
- Security metrics and reporting

### Training Delivery Methods

**1. Interactive Workshops**

**Format:**
- 2-hour sessions, hands-on focused
- Small groups (10-15 developers)
- Mix of lecture (30%) and labs (70%)

**Example Session: "Secure Python Code with LLMs"**

**Schedule:**
- 0:00-0:15: Intro to LLM training biases
- 0:15-0:45: Lab 1 - Identify vulnerabilities in LLM-generated code
- 0:45-1:00: Review and discussion
- 1:00-1:15: Secure coding patterns
- 1:15-1:45: Lab 2 - Refactor insecure code to secure
- 1:45-2:00: Q&A and takeaways

**2. Capture-The-Flag (CTF) Events**

**Setup:**
- Quarterly internal CTF competitions
- Challenges based on real LLM-suggested vulnerable code
- Teams compete to find and exploit vulnerabilities
- Winners get recognition and prizes

**Example Challenge:**

```python
# Challenge: "AI Assistant Gone Wrong"
# An LLM-generated API has multiple vulnerabilities.
# Find and exploit them to capture the flag.

from flask import Flask, request
import sqlite3
import hashlib

app = Flask(__name__)

@app.route('/api/user/<username>')
def get_user(username):
    # LLM-generated code
    conn = sqlite3.connect('users.db')
    query = f"SELECT * FROM users WHERE username = '{username}'"
    result = conn.execute(query).fetchone()
    return {'user': result}

@app.route('/api/login', methods=['POST'])
def login():
    username = request.json['username']
    password = request.json['password']
    
    # LLM-generated authentication
    password_hash = hashlib.md5(password.encode()).hexdigest()
    
    conn = sqlite3.connect('users.db')
    query = f"SELECT * FROM users WHERE username = '{username}' AND password = '{password_hash}'"
    user = conn.execute(query).fetchone()
    
    if user or username == "admin":  # Debug backdoor
        return {'token': 'valid_token_' + username}
    return {'error': 'Invalid credentials'}, 401

# Flag is in the database, in the admin user's profile
```

**Vulnerabilities to find:**
1. SQL injection in `/api/user/<username>`
2. SQL injection in `/api/login`
3. Weak password hashing (MD5)
4. Admin backdoor
5. Information disclosure in error messages

**3. Security Champions Program**

**Structure:**
- 1 security champion per team (5-10 developers)
- Champions receive advanced training
- Champions lead team security reviews
- Monthly champions meetup to share learnings

**Champion Responsibilities:**
- Review high-risk code changes
- Mentor team on secure coding
- Customize LLM security linters for team's stack
- Report security trends to security team

**4. Microlearning / Just-In-Time Training**

**IDE Integration:**

```python
# Example: VSCode extension shows security tip when LLM suggests code

# When Copilot suggests:
import pickle
data = pickle.loads(user_data)

# Extension shows popup:
"""
⚠️ Security Alert: Insecure Deserialization

LLMs often suggest pickle for serialization, but it's vulnerable to RCE.

Secure alternative:
import json
data = json.loads(user_data)

Learn more: [Internal wiki: Serialization Security]
"""
```

**Slack Bot Integration:**

```
Developer: /ask-security Is MD5 okay for password hashing?

SecurityBot: ❌ No! MD5 is cryptographically broken and too fast for password hashing.

✅ Use bcrypt, Argon2, or scrypt instead.

LLMs frequently suggest MD5 because it appears in older code. Always verify crypto suggestions.

📚 Training module: [Cryptography for LLM-Assisted Development]
```

### Training Effectiveness Metrics

**1. Knowledge Assessment**

```python
# Pre-training and post-training quiz scoring

def calculate_training_effectiveness(pre_test_scores, post_test_scores):
    """Measure knowledge gain from training"""
    
    improvements = []
    for dev_id in pre_test_scores:
        pre_score = pre_test_scores[dev_id]
        post_score = post_test_scores[dev_id]
        improvement = post_score - pre_score
        improvements.append(improvement)
    
    avg_improvement = sum(improvements) / len(improvements)
    
    return {
        'average_improvement': avg_improvement,
        'pass_rate_pre': len([s for s in pre_test_scores.values() if s >= 70]) / len(pre_test_scores),
        'pass_rate_post': len([s for s in post_test_scores.values() if s >= 70]) / len(post_test_scores)
    }
```

**2. Code Quality Metrics**

```python
# Track security issues in code reviews before/after training

def track_security_issue_trends(code_reviews):
    """Measure reduction in security issues after training"""
    
    # Group by time period
    before_training = [r for r in code_reviews if r['date'] < training_date]
    after_training = [r for r in code_reviews if r['date'] >= training_date]
    
    # Count security issues
    issues_before = sum(r['security_issues_found'] for r in before_training) / len(before_training)
    issues_after = sum(r['security_issues_found'] for r in after_training) / len(after_training)
    
    reduction = (issues_before - issues_after) / issues_before * 100
    
    return {
        'avg_issues_before': issues_before,
        'avg_issues_after': issues_after,
        'reduction_percentage': reduction
    }
```

**3. Time-to-Detect Metrics**

```python
# Measure how quickly developers identify vulnerabilities in LLM code

def measure_detection_speed(vulnerability_identification_tests):
    """Track improvement in vulnerability detection speed"""
    
    test_results = []
    for test in vulnerability_identification_tests:
        time_to_identify = test['completion_time']
        accuracy = test['vulnerabilities_found'] / test['total_vulnerabilities']
        test_results.append({
            'time': time_to_identify,
            'accuracy': accuracy
        })
    
    # Goal: <5 minutes to identify 80%+ of vulnerabilities
    proficient = len([t for t in test_results if t['time'] < 300 and t['accuracy'] >= 0.8])
    proficiency_rate = proficient / len(test_results)
    
    return proficiency_rate
```

**4. Incident Reduction**

```python
# Track security incidents related to LLM-generated code

def calculate_incident_reduction(incidents_log, training_dates):
    """Measure reduction in LLM-related security incidents after training"""
    
    incidents_by_quarter = {}
    
    for incident in incidents_log:
        if 'llm_generated' in incident['tags']:
            quarter = get_quarter(incident['date'])
            incidents_by_quarter[quarter] = incidents_by_quarter.get(quarter, 0) + 1
    
    # Compare quarters before/after training rollout
    pre_training_avg = avg([incidents_by_quarter[q] for q in pre_training_quarters])
    post_training_avg = avg([incidents_by_quarter[q] for q in post_training_quarters])
    
    reduction = (pre_training_avg - post_training_avg) / pre_training_avg * 100
    
    return reduction
```

## Limitations & Trade-offs

### 1. Training Requires Time Investment

**Cost:**
- Initial 8-hour foundational training per developer
- 2-hour LLM-specific training
- Quarterly 1-hour refreshers
- Lost development time during training

**Impact:**
- Feature development slows temporarily
- May delay releases
- Budget allocation required

**Mitigation:**
- Stagger training (avoid training entire team at once)
- Schedule during lower-activity periods
- Measure ROI via incident reduction

### 2. Knowledge Retention Decay

**Problem:** Developers forget training content over time

**Research:** Retention drops to ~50% after 6 months without reinforcement

**Mitigation:**
- Quarterly refreshers
- Just-in-time training (IDE tips, Slack bot)
- Practical application (code reviews, CTFs)
- Security champions maintaining team knowledge

### 3. Varying Skill Levels

**Challenge:** Developers have different baseline security knowledge

**Issues:**
- Advanced developers bored by basic content
- Beginners overwhelmed by advanced topics
- One-size-fits-all training ineffective

**Solution:**
- Tiered training paths (beginner, intermediate, advanced)
- Pre-training assessment to place developers
- Self-paced modules for flexibility

### 4. Resistance to Additional Process

**Reality:** Some developers view security as "slowing them down"

**Symptoms:**
- Low training attendance
- Checklist fatigue
- "Security is someone else's job" attitude

**Mitigation:**
- Executive sponsorship and messaging
- Gamification (CTF competitions, leaderboards)
- Demonstrate real costs of vulnerabilities
- Make security tools developer-friendly
- Recognize and reward security champions

### 5. Training Lags Emerging Threats

**Problem:** New LLM vulnerabilities emerge faster than training updates

**Example:** New jailbreak technique discovered → months before training updated

**Mitigation:**
- Dedicated security team monitoring threat landscape
- Rapid communication channels (Slack, email alerts)
- Monthly security newsletter highlighting new risks
- Training content continuously updated

## Testing & Validation

### Validate Training Effectiveness

**Practical Skills Assessment:**

```python
# Example: Post-training coding challenge

def secure_coding_challenge():
    """
    Challenge: Fix all security issues in this LLM-generated code
    Time limit: 30 minutes
    Pass criteria: Identify and fix 80%+ of vulnerabilities
    """
    
    vulnerable_code = '''
import pickle
import hashlib
from flask import Flask, request

app = Flask(__name__)

@app.route('/api/data', methods=['POST'])
def process_data():
    # Deserialize user input
    data = pickle.loads(request.data)
    
    # Hash password
    password_hash = hashlib.md5(data['password'].encode()).hexdigest()
    
    # Store in database
    query = f"INSERT INTO users VALUES ('{data['username']}', '{password_hash}')"
    db.execute(query)
    
    return {'status': 'success'}

@app.route('/api/user/<user_id>')
def get_user(user_id):
    query = f"SELECT * FROM users WHERE id = {user_id}"
    result = db.execute(query).fetchone()
    return {'user': result}
'''
    
    expected_fixes = [
        'Replace pickle with json',
        'Replace MD5 with bcrypt/Argon2',
        'Use parameterized SQL query in process_data',
        'Use parameterized SQL query in get_user',
        'Add input validation',
        'Add error handling',
        'Use HTTPS-only cookies for sessions'
    ]
    
    return {
        'code': vulnerable_code,
        'expected_fixes': expected_fixes,
        'time_limit_minutes': 30
    }
```

**Red Team Exercise:**

After training, conduct red team exercise:
1. Give developers LLM-generated codebase with planted vulnerabilities
2. Developers conduct code review
3. Measure detection rate and time
4. Provide feedback on missed vulnerabilities

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No documented cases yet)* | | |

## Sources

> "Developer Training: Educate on LLM biases toward old code; 'Verify, don't trust' policy for generated code; Security-focused code review checklists for LLM output. Code-generating LLMs introduce systemic security vulnerabilities by learning from historical codebases containing outdated, deprecated, and inherently insecure programming practices."
> — [[sources/bibliography#LLMs in Cybersecurity]], p. 88-94
