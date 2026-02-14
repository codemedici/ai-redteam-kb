---
title: "Output Encoding"
tags:
  - type/mitigation
  - target/llm
  - target/llm-app
  - target/agent
owasp: LLM02
maturity: draft
created: 2026-02-14
updated: 2026-02-14
---
# Output Encoding

## Summary

Context-aware encoding of LLM-generated content before rendering or execution in downstream systems. Treats LLM outputs as untrusted input requiring sanitization—the same principle applied to user-submitted data in traditional web applications. Prevents injection attacks (XSS, SQL injection, command injection) by ensuring malicious payloads are rendered as inert data rather than executable code.

## Defends Against

| ID | Technique | Description |
|----|-----------|-------------|
| | [[techniques/insufficient-output-encoding]] | Primary defense: encodes LLM outputs to prevent XSS, command injection, SQL injection, and other downstream execution attacks |
| | [[techniques/prompt-injection]] | Mitigates impact: even if prompt injection succeeds in manipulating LLM output, encoding prevents malicious payloads from executing in browser/shell/database |
| | [[techniques/jailbreak-policy-bypass]] | Limits exploitation: jailbreak-generated malicious content rendered safely rather than executed |

## Implementation

### Context-Aware Encoding Strategy

**Core principle:** Encoding must match the execution context where LLM output will be used.

| Context | Encoding Method | Examples |
|---------|----------------|----------|
| **HTML** | HTML entity encoding | `< → &lt;`, `> → &gt;`, `" → &quot;`, `' → &#x27;` |
| **JavaScript** | JavaScript escaping | `\ → \\`, `' → \'`, `" → \"`, newline → `\n` |
| **SQL** | Parameterized queries | Use `cursor.execute("SELECT * FROM users WHERE id = ?", (user_id,))` not string concatenation |
| **Shell commands** | Shell escaping / avoid shell entirely | Use `subprocess.run(['rm', filename])` not `os.system(f"rm {filename}")` |
| **URLs** | URL encoding | Space → `%20`, `< → %3C`, etc. |
| **Markdown** | Sanitize or disable HTML | Strip `<script>`, `javascript:` URIs, or use strict markdown-only rendering |

### HTML Output Encoding

**Problem:** LLM generates text containing `<script>alert('XSS')</script>` → Web app renders it → Browser executes script

**Solution:** HTML-escape all LLM outputs before insertion into HTML

**Example (Python with Jinja2):**
```python
from jinja2 import Template

# Vulnerable (no escaping)
html = f"<div>{llm_response}</div>"  # ❌ XSS if llm_response contains <script>

# Safe (automatic escaping)
template = Template("<div>{{ llm_response }}</div>")  # ✅ Jinja2 auto-escapes by default
html = template.render(llm_response=llm_response)

# Safe (manual escaping)
import html
safe_html = f"<div>{html.escape(llm_response)}</div>"  # ✅ Explicit escape
```

**Example (JavaScript with React):**
```javascript
// Vulnerable (dangerouslySetInnerHTML)
<div dangerouslySetInnerHTML={{ __html: llmResponse }} />  // ❌ XSS

// Safe (default React escaping)
<div>{llmResponse}</div>  // ✅ React auto-escapes
```

**Key libraries:**
- Python: `html.escape()`, Jinja2 auto-escaping (enabled by default)
- JavaScript: React (default escaping), DOMPurify (sanitization)
- Java: OWASP Java Encoder, `StringEscapeUtils`
- .NET: `HttpUtility.HtmlEncode()`, Razor auto-escaping

### SQL Output Encoding (Parameterized Queries)

**Problem:** LLM generates `' OR '1'='1` → Concatenated into SQL → SQL injection

**Solution:** Use parameterized queries / prepared statements—NEVER concatenate LLM output into SQL strings

**Example (Python with SQLite):**
```python
# Vulnerable (string concatenation)
cursor.execute(f"SELECT * FROM products WHERE name = '{llm_response}'")  # ❌ SQLi

# Safe (parameterized query)
cursor.execute("SELECT * FROM products WHERE name = ?", (llm_response,))  # ✅ Parameterized
```

**Example (Node.js with PostgreSQL):**
```javascript
// Vulnerable
const query = `SELECT * FROM users WHERE username = '${llmResponse}'`;  // ❌ SQLi
db.query(query);

// Safe (parameterized)
db.query('SELECT * FROM users WHERE username = $1', [llmResponse]);  // ✅ Parameterized
```

**Why parameterized queries work:** The database treats the parameter as **data**, not **code**, regardless of content. SQL injection payloads are rendered inert.

### Command Injection Prevention

**Problem:** LLM generates `; rm -rf /` → Passed to `os.system()` → Command execution

**Solution:** Use subprocess with argument arrays (no shell interpretation) OR strict shell escaping

**Example (Python):**
```python
import subprocess
import shlex

# Vulnerable (shell=True with string concatenation)
os.system(f"rm {llm_response}")  # ❌ Command injection
subprocess.run(f"rm {llm_response}", shell=True)  # ❌ Still vulnerable

# Safe (argument array, no shell)
subprocess.run(['rm', llm_response])  # ✅ Shell metacharacters treated as literal filename

# Safe (shell escaping if shell required)
safe_input = shlex.quote(llm_response)
subprocess.run(f"rm {safe_input}", shell=True)  # ✅ Escaped (but argument array preferred)
```

**Best practice:** Avoid shell execution entirely. Use direct subprocess invocation with argument arrays.

### Markdown Sanitization

**Problem:** LLM generates `[click here](javascript:alert(1))` → Markdown renderer converts to `<a href="javascript:...">` → XSS

**Solution:** Use markdown libraries with HTML sanitization enabled OR disallow raw HTML in markdown

**Example (Python with markdown):**
```python
import markdown
from markdown.extensions import Extension
from markdown.treeprocessors import Treeprocessor

# Vulnerable (allows raw HTML)
html = markdown.markdown(llm_response)  # ❌ XSS if LLM generates <script>

# Safe (sanitize HTML)
html = markdown.markdown(llm_response, extensions=['extra'], 
                         safe_mode='escape')  # ✅ Escapes HTML

# Safe (strip javascript: URIs)
# Use libraries like Bleach or DOMPurify to sanitize href attributes
```

**Example (JavaScript with marked.js):**
```javascript
import marked from 'marked';
import DOMPurify from 'dompurify';

// Vulnerable
const html = marked.parse(llmResponse);  // ❌ XSS possible

// Safe (sanitize after markdown parsing)
const rawHtml = marked.parse(llmResponse);
const safeHtml = DOMPurify.sanitize(rawHtml);  // ✅ Strips malicious content
```

### Content Security Policy (CSP)

**Defense-in-depth:** Even with encoding, deploy CSP headers to block inline scripts

**Example CSP header:**
```
Content-Security-Policy: default-src 'self'; script-src 'self'; object-src 'none'; base-uri 'self';
```

**Effect:** Blocks execution of inline `<script>` tags even if encoding fails

**Configuration (Express.js):**
```javascript
const helmet = require('helmet');
app.use(helmet.contentSecurityPolicy({
  directives: {
    defaultSrc: ["'self'"],
    scriptSrc: ["'self'"],
    objectSrc: ["'none'"],
  }
}));
```

## Limitations & Trade-offs

**Not a complete solution:**
- Encoding prevents execution but doesn't detect malicious intent—attackers can still craft harmful text that's rendered (phishing, misinformation)
- Encoding failures (bugs, missed contexts) can still result in injection—defense-in-depth required
- Overly aggressive encoding can break legitimate functionality (e.g., markdown formatting intended by user)

**Context sensitivity:**
- Incorrect context selection (e.g., HTML encoding for SQL) provides no protection
- Complex rendering pipelines (markdown → HTML → JavaScript) require encoding at each layer
- Some contexts are difficult to encode safely (e.g., CSS, certain JavaScript contexts)

**Performance:**
- Encoding adds processing overhead, especially for large LLM responses
- Real-time encoding in high-throughput applications may require optimization

**Usability:**
- Aggressive sanitization can strip legitimate content (e.g., code examples, formatted text)
- Balance needed between security and preserving LLM output quality

## Testing & Validation

**Unit Tests:**
- Test encoding functions with known injection payloads
- Verify HTML encoding: `<script>alert(1)</script>` → `&lt;script&gt;alert(1)&lt;/script&gt;`
- Verify SQL parameterization: `' OR '1'='1` does not cause injection
- Verify shell escaping: `; rm -rf /` treated as literal filename

**Integration Tests:**
- Submit injection payloads via LLM prompts
- Verify outputs are rendered safely in browser / executed safely in shell
- Confirm CSP blocks inline scripts even if encoding bypassed

**Automated Scanning:**
- Use OWASP ZAP, Burp Suite to scan LLM-integrated endpoints for XSS
- Use SQLMap to test database query endpoints for SQL injection
- Deploy WAF (Web Application Firewall) to detect bypass attempts in production

**Monitoring:**
- Log encoding failures (e.g., unexpected characters, encoding errors)
- Alert on CSP violation reports (indicates attempted XSS)
- Track injection pattern detections in LLM outputs (even if encoded, indicates attack attempt)

## Procedure Examples

| Name | Tactic | Description |
|------|--------|-------------|
| *(No case studies yet)* | | |

## Sources

> "Encode LLM outputs to prevent injection attacks in downstream systems—treat model-generated content as untrusted input requiring context-aware sanitization."
> — OWASP LLM Top 10 2023: LLM02 Insecure Output Handling

> "Applications must treat LLM outputs as untrusted input requiring encoding and sanitization before rendering in web pages, executing in shells, or inserting into databases."
> — OWASP LLM Top 10 2025: LLM05 Improper Output Handling
