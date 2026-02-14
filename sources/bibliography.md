# Bibliography & Source Materials

This note tracks PDF sources used to augment the AI Red Teaming Knowledge Base.

## Books

### Downloaded (2026-02-11)

| Title | Filename | Status | Notes |
|-------|----------|--------|-------|
| EA AI Security Handbook | `_EA_AISecurityHandbook_1125.pdf` | ✅ Downloaded | |
| EA Red Team Engineering | `_EA_RedTeamEngineering_1025.pdf` | ✅ Downloaded | |
| AI Native LLM Security | `9781836203759-AI_NATIVE_LLM_SECURITY.pdf` | ✅ Downloaded | |
| Adversarial AI: Attacks, Mitigations and Defense Strategies | `Adversarial-AI---Attacks-Mitigations-and-Defense-Strategies.pdf` | ✅ Downloaded | |
| Agentic Design Patterns | `Agentic_Design_Patterns_bok:978-3-032-01402-3.pdf` | ✅ Downloaded | |
| Deep Learning (Ian Goodfellow) | `Deep+Learning+Ian+Goodfellow.pdf` | ✅ Downloaded | |
| Generative AI Security | `Generative_AI_Security_bok:978-3-031-54252-7.pdf` | ✅ Downloaded | |
| Large Language Models in Cybersecurity | `LargeLanguageModelsinCybersecurity.pdf` | ✅ Downloaded | |
| LLMs in Production | `LLMs_in_Production.pdf` | ✅ Downloaded | |
| Red-Teaming AI | `Red-Teaming-AI-Print bio.pdf` | ✅ Downloaded | |
| Securing AI Agents | `Securing_AI_Agents_bok:978-3-032-02130-4.pdf` | ✅ Downloaded | |
| The Developer's Playbook for LLM Security | `the.developers.playbook.for.llm.security.pdf` | ✅ Downloaded | |

**Total: 12 books**

---

## Source Directory Structure

```
/home/whoami/.openclaw/workspace/ai-redteam-sources/
├── books/           (source PDFs)
├── papers/          (academic papers - TBD)
└── processed/       (extracted content, notes)
```

---

## Processing Status

### Completed
- **Red-Teaming AI** — 10/12 units extracted (280KB), u11/u12 skipped (quantum/speculative)
- **AI-Native LLM Security** — 8/8 units extracted (~70KB), **all units completed** (u8 enriched existing notes with code examples)

### Completed (2026-02-12)
- **Generative AI Security** — 3/13 units extracted (Tier 1: governance/compliance focus)
  - u1: Global AI Governance & Coordination → deleted (corporate/policy, not practitioner content)
  - u2: Global AI Regulation Landscape → deleted (corporate/policy, not practitioner content)
  - u3: GenAI Security Program Blueprint → `playbooks/genai-security-program-blueprint.md`

### Paused
- **Securing AI Agents** — 4/10 units extracted (pilot phase)

### Deferred
- **EA Red Team Engineering** — Traditional red teaming; deferred until AI RT picture complete

### Queued
- Remaining 8 books

---

## Extraction Notes

When processing sources:
- Extract relevant sections to vault notes
- Link back to this bibliography with `[[sources/bibliography]]`
- Tag notes with `#source/<book-shortname>`
- Track page numbers for citations
- Create atomic notes for specific concepts

---

## Citation Format

When referencing sources in vault notes:

```markdown
> Content excerpt here.
> 
> Source: [[sources/bibliography#Book Title]], p. XX
```

---

## Next Steps

1. Parse PDFs to identify key sections relevant to:
   - MITRE ATLAS expansion
   - Methodology refinement
   - New techniques and case studies
   - Trust boundary controls
2. Create vault notes with proper citations
3. Update existing notes with sourced content
4. Build cross-references

---

## Detailed Source Information

### Generative AI Security

**Full Citation**:  
Huang, K., Wang, Y., Goertzel, B., Li, Y., Wright, S., & Ponnapalli, J. (Eds.). (2024). *Generative AI Security: Theories and Practices*. Future of Business and Finance. Springer Nature Switzerland AG. ISBN 978-3-031-54252-7.

**Content Focus**:
- Part I: Foundations of GenAI and Security Landscape
- Part II: Securing GenAI Systems (Regulations, Policies, Data/Model/App Security)
- Part III: Operationalizing GenAI Security (LLMOps, DevSecOps, Prompt Engineering, Tools)

**Key Contributions to Vault**:
- **Global AI Governance**: IAEA-inspired international coordination framework, comparative analysis of national regulations (EU AI Act, US EO 14110, China CAC, UK White Paper, etc.)
- **Security Program Building**: Comprehensive playbook covering policies (13 key elements), processes (risk management, development, access governance), procedures (authentication, deployment, incident response, data management), and governance structures (centralized/semi/decentral)
- **Operational Frameworks**: LLMOps vs. MLOps, DevSecOps integration, prompt engineering for security ops, GenAI governance tooling

**Pages**: 367 pages total

**Chapter Structure**:
- Ch 1: Foundations of Generative AI (neural networks, transformers, diffusion models) — Educational, skipped
- Ch 2: Navigating GenAI Security Landscape — Threat overview, skipped (covered by existing attack notes)
- Ch 3: AI Regulations — **✅ Extracted** (Global coordination, country-specific regulations)
- Ch 4: Build Your Security Program — **✅ Extracted** (Policies, processes, procedures, governance)
- Ch 5: GenAI Data Security — Data provenance, trilemma, data-centric AI (partially extracted in u3, full dedicated note deferred)
- Ch 6: GenAI Model Security — Frontier models, Black Cloud case study (deferred)
- Ch 7: GenAI Application Level Security — CSA CCM mapping, LLM Gateway, cloud AI services (deferred)
- Ch 8: LLMOps & DevSecOps — **Integrated into u3** (partially)
- Ch 9: Prompt Engineering — Operational techniques (deferred)
- Ch 10: GenAI Tools — Governance platforms, observability (deferred)

**Extraction Strategy**: Governance/compliance/operationalization focus (not attack/defense techniques already covered). Tier 1 units prioritized (u1-u3), Tier 2/3 deferred pending backlog review.

**State Directory**: `/home/whoami/.openclaw/workspace/ai-redteam-sources/state/books/generative-ai-security/`

**Extracted By**: Shadowbane (agent:main:subagent:96d6de3a-15aa-4dc9-9ed2-453db9b16cb1)  
**Extracted**: 2026-02-12

---

### Adversarial AI

**Full Citation**:  
Sotiropoulos, Christos. (2024). *Adversarial AI: Attacks, Mitigations, and Defense Strategies - A Comprehensive Guide for Adversarial Machine Learning from Attacks to Defense*. Packt Publishing. ISBN 978-1-80512-072-3.

**Content Focus**:
- Part I: Foundations and Attack Vectors (Data Poisoning, Model Tampering, Supply Chain)
- Part II: Inference and Privacy Attacks (Evasion, Model Extraction, Privacy Attacks)
- Part III: Privacy-Preserving ML (Differential Privacy, Federated Learning, Homomorphic Encryption)
- Part IV: Generative AI Attacks (GANs, Prompt Attacks, LLM Poisoning, Multimodal)
- Part V: Defense and Operations (Threat Modeling, MLSecOps, Maturity Models)

**Key Contributions to Vault**:
- **GAN Weaponization**: Comprehensive coverage of deepfake generation (StyleGAN, StarGAN, Pix2PixHD, FSAL), deepfake detection (DFDC challenge), GANs in cyberattacks (face verification bypass, biometric auth compromise, PassGAN password cracking, MalGAN malware evasion, steganography), GAN-assisted adversarial attacks (UAP, GAP, AdvGAN, AI-GAN), and defenses (Defense-GAN, privacy-preserving GANs, digital watermarking)
- **Adversarial Training**: Implementation with ART, PGD-based training (Madry defense), integration with MLOps
- **Attack Techniques**: Detailed mechanics for FGSM, PGD, C&W, JSMA with ART code examples
- **Data Poisoning**: Label flipping, data injection, clean-label attacks, incremental poisoning, federated learning attacks
- **Privacy Attacks**: Model inversion, membership inference
- **MLSecOps**: Operational security patterns, threat modeling, maturity assessment

**Pages**: 586 pages total

**Chapter Structure**:
- Ch 1-2: AI/ML Foundations — Skipped (educational intro)
- Ch 3: Security & Adversarial AI — **✅ Extracted** (foundational controls)
- Ch 4: Data Poisoning — **✅ Extracted** (comprehensive poisoning attacks)
- Ch 5: Model Tampering — **✅ Extracted** (trojan injection)
- Ch 6: Supply Chain — **✅ Extracted** (supply chain model poisoning)
- Ch 7: Evasion Attacks — **✅ Extracted** (adversarial examples, ART implementations)
- Ch 8: Model Extraction — **✅ Complete** (existing note comprehensive)
- Ch 9: Privacy Attacks — **✅ Extracted** (model inversion)
- Ch 10: Privacy-Preserving ML — **✅ Extracted** (differential privacy, federated learning)
- Ch 11: GenAI Intro — Skipped (GAN basics, educational)
- Ch 12: Weaponizing GANs — **✅ Extracted** (comprehensive GAN attack techniques)
- Ch 13: LLM Intro — Skipped (LLM basics, educational)
- Ch 14: Prompt Attacks — **✅ Extracted** (prompt injection)
- Ch 15: LLM Poisoning — **✅ Extracted** (RAG embedding poisoning)
- Ch 16: Advanced GenAI — **✅ Complete** (overlaps with existing notes)
- Ch 17: Threat Modeling — **✅ Extracted** (secure by design)
- Ch 18: MLSecOps — **✅ Extracted** (operational security)
- Ch 19: Maturing AI Security — **✅ Complete** (governance, low priority)

**Extraction Strategy**: Attack techniques and defenses with practical implementations. Comprehensive coverage of adversarial ML attack surface. ART (Adversarial Robustness Toolbox) code examples throughout.

**State Directory**: `/home/whoami/.openclaw/workspace/ai-redteam-sources/state/books/adversarial-ai/`

**Extracted By**: Shadowbane (multiple extraction sessions)  
**Last Extracted**: 2026-02-14 (Chapter 12: Weaponizing GANs)

---

### AI-Native LLM Security

**Full Citation**:  
Malik, S., Huang, S., & Dawson, J. (2025). *AI-Native LLM Security: Integrating AI Systems in Modern Architectures*. Packt Publishing. ISBN 978-1-83620-375-9.

**Content Focus**:
- Part I: Foundations of LLM Security (Introduction, Components, Threats, Trust Boundaries)
- Part II: OWASP Top 10 for LLM Applications (Detailed Risk Profiles, Mitigations, Code Examples)
- Part III: Secure Architecture (Design Patterns, Lifecycle Security, Operational Resilience, Regulatory Alignment)
- Appendices: OWASP AIVSS Agentic Risks, OWASP 2025 Updates

**Key Contributions to Vault**:
- **OWASP Top 10 Deep Profiles (Ch7)**: Detailed risk categorization framework (5 areas: Injection Flaws, Broken Auth, Sensitive Data Exposure, Broken Access Control, Security Misconfigurations), vulnerability code examples (Python Flask apps demonstrating prompt injection, insecure plugin design, data leakage, supply chain risks), real-world incidents (Samsung ChatGPT leak, redis-py vulnerability, Expedia plugin exploit), and attack/defense patterns for all 10 OWASP LLM risks
- **OWASP AIVSS Agentic Security (Appendix B)**: Comprehensive framework for agent-specific risks (10 core security risks including Prompt Leakage, Function Calling Manipulation, Context Window Manipulation, Credential Theft, Tool Permission Abuse)
- **OWASP 2025 Evolution (Appendix A)**: New attack categories (System Prompt Leakage LLM07:2025, Vector and Embedding Weaknesses LLM08:2025), ranking changes (Supply Chain promoted #5→#3, Sensitive Info promoted #6→#2), architectural shift (standalone LLMs → RAG + Agent ecosystems)
- **Trust Boundary Framework (Ch4)**: Mapping LLM architectures to security boundaries
- **Secure Architecture Patterns (Ch10)**: Zero-trust LLM design, isolation strategies, reference architectures
- **Mitigation Strategies (Ch8)**: Defense techniques for each OWASP category with implementation guidance
- **Lifecycle Security (Ch11)**: DevSecOps for LLMs, secure data pipelines, training, deployment
- **Operational Resilience (Ch12)**: Monitoring, anomaly detection, incident response for LLM systems

**Pages**: 416 pages total

**Chapter Structure**:
- Ch 1-3: LLM Security Fundamentals — Skipped (introductory material)
- Ch 4: Trust Boundaries — **✅ Extracted** (trust boundary mapping framework)
- Ch 5-6: Security Basics — Skipped (basic security concepts)
- Ch 7: OWASP Deep Profiles (p125-151) — **✅ Extracted** (enriched existing technique notes with code examples, real-world cases, detailed risk profiles)
- Ch 8: OWASP Mitigations — **✅ Extracted** (mitigation strategies per OWASP category)
- Ch 9: Skipped
- Ch 10: Secure Architecture — **✅ Extracted** (zero-trust patterns, isolation, reference architectures)
- Ch 11: Development Lifecycle — **✅ Extracted** (DevSecOps for LLMs)
- Ch 12: Operational Resilience — **✅ Extracted** (monitoring, incident response)
- Ch 13: Regulatory Alignment — Skipped (compliance basics)
- Appendix A: OWASP 2025 Updates — **✅ Extracted** (System Prompt Leakage, Vector Weaknesses, evolution mapping)
- Appendix B: OWASP AIVSS Agentic Risks — **✅ Extracted** (10 agentic security risks framework)

**Code Examples Extracted:**
- Prompt Injection (Direct & Indirect) — Flask app with vulnerable banking assistant (p128-130)
- Insecure Plugin Design — Database plugin with no auth, hardcoded keys, SQL injection vulnerability (p136-138)
- Data Leakage — Preprocessing error leading to test/train leakage (p140-141)
- Supply Chain Vulnerabilities — Loading untrusted models, security misconfigurations (p149-151)
- Model Theft — API-based extraction via synthetic dataset generation (p144-145)

**Real-World Incidents Documented:**
- Samsung ChatGPT Data Leak (March 2023) — Employees leaked semiconductor source code
- Redis-py Supply Chain Incident (March 2023) — OpenAI bug exposed 1.2% of ChatGPT Plus payment data
- Expedia Plugin Exploit (2023) — Cross-plugin request forgery via indirect prompt injection
- GPT-2 Memorization Research — 33.5% success rate extracting training data (604 unique examples from 1,800 candidates)

**Extraction Strategy**: Enrich-first approach — added code examples, real-world cases, and detailed risk profiles to existing technique notes (prompt-injection.md, unsafe-tool-invocation.md, sensitive-info-disclosure.md, supply-chain-model-poisoning.md, model-extraction-llm.md). Created new notes for OWASP AIVSS agentic framework and 2025 evolution. All units completed.

**Notes Enriched (u8):**
- `techniques/prompt-injection.md` — Added vulnerable Flask banking assistant code example
- `techniques/unsafe-tool-invocation.md` — Added insecure plugin design code example (Expedia exploit case)
- `techniques/sensitive-info-disclosure.md` — Added data leakage preprocessing example, Samsung incident
- `techniques/supply-chain-model-poisoning.md` — Added redis-py incident, security misconfiguration code
- `techniques/model-extraction-llm.md` — Fully rewritten with model theft code example, GPT-2 research findings
- `frameworks/OWASP-top-10-LLM.md` — Added 5-area risk categorization framework

**State Directory**: `/home/whoami/.openclaw/workspace/ai-redteam-sources/state/books/ai-native-llm-security/`

**Extracted By**: Subagent enrich-ai-native-u8 (agent:main:subagent:e5618fbf-431a-44a2-b428-de633bafe5c2)  
**Extracted**: 2026-02-14

---

*Last updated: 2026-02-14*
