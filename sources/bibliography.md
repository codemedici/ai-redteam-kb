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
- **AI-Native LLM Security** — 7/8 units extracted (~55KB), u8 skipped (incremental overlap)

### Completed (2026-02-12)
- **Generative AI Security** — 3/13 units extracted (Tier 1: governance/compliance focus)
  - u1: Global AI Governance & Coordination → `frameworks/ai-governance-coordination.md`
  - u2: Global AI Regulation Landscape → `frameworks/ai-regulation-landscape.md`
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

*Last updated: 2026-02-12*
