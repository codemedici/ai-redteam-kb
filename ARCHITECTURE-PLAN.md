# Vault Architecture Plan v2

## Design Principles

1. **Actionability-first**: Structure mirrors the practitioner's engagement workflow, not academic taxonomy
2. **ATLAS as canonical taxonomy**: Every attack maps to ATLAS technique IDs; ATLAS is the Rosetta Stone
3. **Tags over folders**: Cross-cutting concerns (trust boundaries, target types, frameworks) are tags, not directories
4. **Flat + tagged**: Attacks and defenses are flat collections with rich frontmatter metadata
5. **Graph = knowledge graph**: Color encodes content type; links encode relationships; clusters emerge from connections

## Folder Structure

```
/attacks/           → All attack techniques (flat, tagged)         ✅ 41 notes
/defenses/          → All controls and mitigations (flat, tagged)  ✅ 13 notes
/methodology/       → Engagement process, phases, frameworks       ✅ 56 notes
/playbooks/         → Engagement-specific quick-start guides       ✅ 9 notes
/atlas/             → MITRE ATLAS reference taxonomy (read-only)   ✅ 229 notes
/case-studies/      → Real-world incidents and war stories         ⬜ NOT CREATED
/sources/           → Bibliography, book references                ✅ 2 notes
```

## Frontmatter Tag Schema

Every note gets structured frontmatter:

```yaml
---
tags:
  - type/{attack,defense,methodology,playbook,overview,case-study,reference}
  - trust-boundary/{model,data-knowledge,agent-runtime,deployment-governance}
  - atlas/{aml.tXXXX}           # MITRE ATLAS technique ID
  - owasp/{llmXX}               # OWASP LLM Top 10 mapping
  - source/{book-slug}           # Source book for citations
  - needs-review                 # Pending human review
  - target/{llm-app,rag-system,agent,ml-pipeline,model-api}
  - access/{black-box,gray-box,white-box}
  - severity/{critical,high,medium,low}
  - phase/{recon,threat-model,execution,analysis,reporting}
created: YYYY-MM-DD
source: "[[sources/bibliography#Book Title]]"
pages: "XX-XX"
description: "One-liner summary"
---
```

## Graph View Configuration

### Color Groups (6, Type-Based)

| Type | Color | Query |
|------|-------|-------|
| Attacks | Red | `path:attacks` |
| Defenses | Green | `path:defenses` |
| Methodology | Blue | `path:methodology` |
| Playbooks | Teal | `path:playbooks` |
| ATLAS Reference | Purple | `path:atlas` |
| Sources/Templates | Gray | `path:sources OR path:_templates` |

### Graph Settings
- **showArrow: true** — directional relationships matter
- **showTags: false** — tags are metadata, not visual noise
- **hideUnresolved: true** — clean graph
- **showOrphans: false** — orphans need fixing

## Link Relationships

### Required Links Per Attack Note

```markdown
## Related
- **Mitigated by**: [[defense-1]], [[defense-2]]
- **Enables**: [[other-attack]] (attack chaining)
- **Enabled by**: [[prerequisite-attack]]
- **ATLAS**: [[atlas-technique-page]]
- **Case studies**: [[case-study-1]]
```

### Required Links Per Defense Note

```markdown
## Related
- **Mitigates**: [[attack-1]], [[attack-2]]
- **Framework alignment**: OWASP LLM01, NIST AI RMF XX
- **Complements**: [[other-defense]]
```

## File Naming Convention

- **Attacks**: descriptive kebab-case, no prefix. `prompt-injection.md`
- **Defenses**: descriptive kebab-case, no prefix. `adversarial-training.md`
- **Methodology**: no prefix. Keep current names.
- ATLAS IDs and trust boundaries in frontmatter tags, not filenames.

## Scoping Engine (Future)

### Input: Scoping Questions
1. Target type: LLM app / RAG system / Agent / ML Pipeline / Model API
2. Industry: Healthcare / Finance / Defense / General
3. Access level: Black-box / Gray-box / White-box
4. Primary concerns: Data leakage / Manipulation / IP theft / Availability / Compliance
5. Scope: Model only / Full stack / Supply chain

### Output: Filtered Engagement Package
- Relevant attack notes (filtered by tags)
- Corresponding defense notes
- Applicable methodology phases
- Framework checklist (OWASP/ATLAS/NIST)
- Quick-start engagement guide

---

# Gap Analysis (2026-02-12)

## ✅ Completed (Migration Steps 1-10)

| Step | Status | Notes |
|------|--------|-------|
| 1. Create new folder structure | ✅ | attacks/, defenses/, methodology/, playbooks/, atlas/, sources/ |
| 2. Move attacks from trust-boundaries/ | ✅ | 41 notes in attacks/ |
| 3. Move defenses from trust-boundaries/ | ✅ | 13 notes in defenses/ |
| 4. Merge operations/ → methodology/ | ✅ | Content absorbed |
| 5. Move engagements/ → playbooks/ | ✅ | 9 notes |
| 6. Add frontmatter tags | ✅ | 119 notes tagged (type/, trust-boundary/, atlas/, owasp/, source/) |
| 7. Update internal wikilinks | ⚠️ PARTIAL | 33 broken links remain |
| 8. Remove trust-boundaries/ tree | ✅ | Gone |
| 9. Reconfigure graph.json | ✅ | 6 color groups, type-based |
| 10. Verify | ⚠️ | Broken links + missing frontmatter fields |

## ⚠️ Gaps

### Broken Wikilinks (33 total)

**Missing defense notes (13):** `access-segmentation-and-rbac`, `adversarial-robustness-controls`, `approval-workflow-patterns`, `content-signing-and-provenance`, `data-quarantine-procedures`, `dual-llm-judge-pattern`, `embedding-integrity-verification`, `incident-response-procedures`, `kill-switch-and-session-termination`, `output-filtering-and-sanitization`, `rate-limiting-and-throttling`, `tool-sandboxing-architecture`, `user-context-binding`

**Missing methodology notes (5):** `methodology/mitre-atlas-mapping`, `methodology/mlops-security`, `methodology/owasp-llm-top-10`, `methodology/supply-chain-security`, `methodology/threat-modeling-for-ai`

**Missing structural links (7):** `application-agent-runtime`, `deployment-governance`, `controls`, `issues`, `engagements`, `contact`, `source-validation-and-trust-scoring`

**ATLAS references (5):** `AML.T0020`, `AML.T0024`, `AML.T0049`, `AML.T0051`, `AML.T0056`

**Malformed link (1):** `attacks/vector-embedding-weaknessesattack-variants-overview` (missing `|`)

### Missing Frontmatter Fields

| Field | Plan Status | Current | Gap |
|-------|-------------|---------|-----|
| `type/` | Required | ✅ 119 notes | Done |
| `trust-boundary/` | Required | ✅ 110 notes | 9 notes missing |
| `atlas/` | Required (attacks) | ⚠️ 13 of 41 attacks | **28 attacks missing ATLAS IDs** |
| `owasp/` | Required (attacks) | ⚠️ 19 notes | Many attacks unmapped |
| `source/` | For extracted notes | ✅ 22 notes | Correct (only extracted notes need this) |
| `target/` | Required | ❌ 0 notes | **Not implemented** |
| `access/` | Required | ❌ 0 notes | **Not implemented** |
| `severity/` | Required | ❌ 0 notes | **Not implemented** |
| `phase/` | Required | ❌ 0 notes | **Not implemented** |
| `tools:` | Optional | ❌ 0 notes | Not implemented |
| `description` | Nice-to-have | ⚠️ ~30 notes | Partial (old Docusaurus notes have it) |

### Missing Content

| Item | Status |
|------|--------|
| `case-studies/` folder | ❌ Not created |
| Related links (attack→defense) | ⚠️ 12/41 attacks have them, 13/13 defenses |
| Duplicate attack notes | ⚠️ `prompt-injection.md` + `prompt-injection-llm-manipulation.md`, `model-extraction.md` + `model-extraction-stealing.md` |
| Books extracted | 2/12 complete, 1 paused, 1 deferred |

---

# Sprint Plan

## Sprint 1: Fix Broken Links + Deduplicate (HIGH PRIORITY)

**Goal:** Zero broken wikilinks, no duplicate notes.

1. **Fix malformed link** in vector-embedding-weaknesses.md
2. **Merge duplicate notes:**
   - `prompt-injection.md` (Docusaurus stub) → merge into `prompt-injection-llm-manipulation.md`
   - `model-extraction.md` (Docusaurus stub) → merge into `model-extraction-stealing.md`
3. **Create 13 missing defense stubs** (placeholder notes with frontmatter + description + "Mitigates" links)
4. **Create 5 missing methodology stubs**
5. **Fix 7 structural links** (redirect to correct targets or remove)
6. **Fix 5 ATLAS links** → point to correct atlas/ paths
7. **Verify: zero broken links**

**Estimated effort:** Medium (~1 session)

## Sprint 2: Enrich Frontmatter (MEDIUM PRIORITY)

**Goal:** All attack/defense notes have the complete tag schema for scoping engine.

1. **Add `target/` tags** to all 41 attack notes + 13 defense notes
2. **Add `access/` tags** (black-box/gray-box/white-box) to all 41 attack notes
3. **Add `severity/` tags** to all 41 attack notes
4. **Add missing `atlas/` IDs** to 28 attack notes (map to ATLAS where applicable)
5. **Add `description`** to notes missing it

**Estimated effort:** Medium (~1 session, scriptable)

## Sprint 3: Cross-References (MEDIUM PRIORITY)

**Goal:** Every attack links to its defenses and vice versa. Attack chains documented.

1. **Add "Mitigated by" links** to remaining 29 attack notes
2. **Add "Enables/Enabled by" links** for attack chains (e.g., system-prompt-leakage → tool-privilege-escalation)
3. **Verify bidirectional links** (attack→defense and defense→attack)
4. **Add ATLAS cross-references** where atlas/ pages exist

**Estimated effort:** Medium-High (~1-2 sessions)

## Sprint 4: Next Book Extraction (ONGOING)

**Goal:** Continue building content from remaining 9 AI-focused books.

Candidates (ranked by expected vault gap coverage):
1. **Adversarial AI** — broad attack/defense coverage, likely fills many stub notes
2. **Developer's Playbook for LLM Security** — practical, tool-focused, fills `tools:` tags
3. **Generative AI Security** — fills governance/compliance gaps
4. **LLMs in Cybersecurity** — offensive use cases, unique angle

**Estimated effort:** High (~2-3 sessions per book)

## Sprint 5: Scoping Engine Prototype (FUTURE)

**Goal:** Script that takes scoping inputs → outputs filtered engagement package.

**Prerequisites:** Sprint 2 (complete frontmatter), Sprint 3 (cross-references)

1. Build `kb-scope.py` that reads frontmatter tags
2. Filter notes by target type, access level, trust boundary
3. Output: attack checklist + defense recommendations + methodology phases
4. Generate engagement-specific playbook from template

**Estimated effort:** Medium (~1 session)

## Sprint 6: Case Studies (FUTURE)

**Goal:** Real-world incidents linked to attack/defense notes.

1. Create `case-studies/` folder
2. Extract case studies from books already processed
3. Link to relevant attack and defense notes
4. Add `type/case-study` tag

**Estimated effort:** Medium (~1 session)
