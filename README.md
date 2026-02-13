# AI Red Teaming Knowledge Base

**Obsidian vault for AI security research, methodology, and threat intelligence.**

## Overview

This knowledge base documents a comprehensive AI red teaming methodology, MITRE ATLAS tactics & techniques, real-world case studies, and engagement frameworks for assessing AI system security.

**Content Structure:**

- **`atlas/`** - MITRE ATLAS reference (tactics, techniques, case studies)
- **`methodology/`** - 9-phase AI red teaming lifecycle
- **`engagements/`** - Engagement type specifications (LLM app, RAG, agentic, supply chain)
- **`trust-boundaries/`** - Architectural attack surfaces and security boundaries
- **`operations/`** - Scoping, pricing, proposals, and operational guides

## Using This Vault

### Recommended Workflow

1. **Graph View** (Ctrl/Cmd+G) - Visualize connections between concepts
2. **Search** (Ctrl/Cmd+Shift+F) - Full-text search across all notes
3. **Backlinks** - See what links to the current note (right sidebar)
4. **Tags** - Browse by topic, framework, or engagement type

### Key Entry Points

- **[[ai-redteam-kb]]** - Start here
- **Methodology** - Complete AI red teaming lifecycle
- **[[frameworks/atlas/atlas|MITRE ATLAS]]** - Tactics, techniques, case studies
- **trust-boundaries-overview|Trust Boundaries]]** - Attack surface model

### Plugins Enabled

This vault is configured with the following plugins (install via Settings → Community Plugins):

- **Dataview** - Dynamic queries and indexes
- **Excalidraw** - Visual diagrams and attack flows
- **Templater** - Note templates
- **Tag Wrangler** - Tag management
- **Recent Files** - Quick access to recent work
- **Advanced Tables** - Table editing
- **Waypoint** - Automatic Maps of Content (MOCs)

### Graph View Color Coding

- 🔴 **Red** - ATLAS Tactics
- 🟠 **Orange** - ATLAS Techniques
- 🟢 **Green** - Case Studies
- 🔵 **Blue** - Methodology
- 🟣 **Purple** - Engagements
- 🟡 **Yellow** - Trust Boundaries

## Maintenance

This is a living knowledge base. Best practices:

- Use wikilinks `[[note-name]]` for internal references
- Tag with framework alignment (`#atlas`, `#owasp`, `#nist-ai-rmf`)
- Update backlinks when refactoring content
- Review graph view periodically to identify orphaned notes or missing connections

## Citation

This knowledge base integrates content from multiple security frameworks:

- **MITRE ATLAS** - https://atlas.mitre.org
- **OWASP LLM Top 10** - https://owasp.org/www-project-top-10-for-large-language-model-applications/
- **NIST AI Risk Management Framework** - https://www.nist.gov/itl/ai-risk-management-framework

## License

Internal research and operational documentation.
