# Vault Architecture Plan v2

## Design Principles

1. **Actionability-first**: Structure mirrors the practitioner's engagement workflow, not academic taxonomy
2. **ATLAS as canonical taxonomy**: Every attack maps to ATLAS technique IDs; ATLAS is the Rosetta Stone
3. **Tags over folders**: Cross-cutting concerns (trust boundaries, target types, frameworks) are tags, not directories
4. **Flat + tagged**: Attacks and defenses are flat collections with rich frontmatter metadata
5. **Graph = knowledge graph**: Color encodes content type; links encode relationships; clusters emerge from connections

## New Folder Structure

```
/attacks/           → All attack techniques (flat, tagged)
/defenses/          → All controls and mitigations (flat, tagged)
/methodology/       → Engagement process, phases, frameworks, decision guides
/playbooks/         → Engagement-specific quick-start guides (future: generated from scoping)
/atlas/             → MITRE ATLAS reference taxonomy (read-only reference)
/case-studies/      → Real-world incidents and war stories
/sources/           → Bibliography, book references
```

### What Changed From Previous Structure

| Before | After | Why |
|--------|-------|-----|
| `trust-boundaries/model/issues/` | `attacks/` (flat) | Trust boundaries are tags, not folders |
| `trust-boundaries/*/controls/` | `defenses/` (flat) | Same — tags, not folders |
| `trust-boundaries/*/index.md` | Removed | Overview pages become MOCs in methodology/ |
| `engagements/` | `playbooks/` | Clearer name; will hold generated engagement guides |
| `operations/` | `methodology/` (merged) | Operations content is methodology |
| 4 boundary sub-trees | Flat + tags | Eliminates artificial single-boundary classification |

### What Stays The Same

- `methodology/` — keeps existing content, absorbs operations/
- `atlas/` — read-only reference, unchanged structure
- `sources/` — bibliography stays

## Frontmatter Tag Schema

Every note gets structured frontmatter:

```yaml
---
type: attack | defense | methodology | case-study | reference | overview
atlas-id: AML.T0051.001          # ATLAS technique ID (attacks only)
atlas-tactic:                     # ATLAS tactic(s)
  - execution
  - initial-access
owasp-llm:                        # OWASP LLM Top 10 mapping
  - LLM01
trust-boundary:                   # Can be MULTIPLE (key insight)
  - model
  - data-knowledge
  - application-agent
  - deployment-governance
target:                           # What system types this applies to
  - llm-app
  - rag-system
  - agent
  - ml-pipeline
  - model-api
access-level:                     # Required access for this attack/test
  - black-box
  - gray-box
  - white-box
severity: critical | high | medium | low
phase:                            # Engagement lifecycle phase
  - reconnaissance
  - threat-modeling
  - execution
  - analysis
  - reporting
tools: []                         # Relevant tools
source: "Book Name (Author), Chapter X, p.XX-XX"
---
```

## Graph View Configuration

### Color Groups (Type-Based)

| Type | Color | Hex | Purpose |
|------|-------|-----|---------|
| Attacks | Red | #e74c3c | Things you test/exploit |
| Defenses | Green | #2ecc71 | Things you recommend |
| Methodology | Blue | #3498db | How to plan and execute |
| Case Studies | Amber | #f39c12 | Real-world examples |
| ATLAS Reference | Purple | #9b59b6 | Canonical taxonomy |
| Sources/Overviews | Gray | #95a5a6 | Supporting material |

### Why Type-Based Works Best

- **Intuitive**: Red = danger/attack, Green = safe/defense
- **Scales**: More attacks = more red nodes, no new colors needed
- **Links do the heavy lifting**: Attack→Defense, Attack→Attack chains, Attack→ATLAS mapping
- **Clean**: 6 colors maximum, no visual noise
- **Practitioner-friendly**: Scan graph → see red cluster → that's your test surface

### Graph View Settings

- **showArrow: true** — directional relationships matter (attack → defense = "mitigated by")
- **showTags: false** — tags are metadata, not visual noise
- **hideUnresolved: true** — clean graph without dead-end links
- **showOrphans: false** — orphan notes need fixing, not displaying

## File Naming Convention

### Attacks
- Drop the `MI-`, `DI-`, `AI-`, `GI-` prefixes (trust boundary info moves to tags)
- Use descriptive kebab-case: `prompt-injection.md`, `data-poisoning.md`, `membership-inference.md`
- ATLAS ID in frontmatter, not filename

### Defenses
- Drop the `MC-`, `AC-`, `GC-` prefixes
- Use descriptive kebab-case: `adversarial-training.md`, `input-validation.md`

### Methodology
- No prefix (already distinct folder)
- Keep current names

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

## Scoping Engine (Future)

### Input: Scoping Questions
1. Target type: LLM app / RAG system / Agent / ML Pipeline / Model API
2. Industry: Healthcare / Finance / Defense / General
3. Access level: Black-box / Gray-box / White-box
4. Primary concerns: Data leakage / Manipulation / IP theft / Availability / Compliance
5. Scope: Model only / Full stack / Supply chain

### Output: Filtered Engagement Package
- List of relevant attack notes (filtered by tags)
- Corresponding defense notes
- Applicable methodology phases
- Framework checklist (OWASP/ATLAS/NIST items)
- Quick-start engagement guide

## Migration Sequence

1. Create new folder structure
2. Move attack files from trust-boundaries/*/issues/ → attacks/ (strip prefixes)
3. Move defense files from trust-boundaries/*/controls/ → defenses/ (strip prefixes)
4. Move operations/ content → methodology/
5. Move engagements/ content → playbooks/
6. Add frontmatter tags to all moved files
7. Update all internal wikilinks
8. Remove empty trust-boundaries/ tree
9. Reconfigure graph.json with new color groups
10. Verify: zero broken links, correct tags, clean graph
