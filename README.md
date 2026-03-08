# GEO Readiness Audit — Claude Code & Factory Droid Skill

A skill/command for [Claude Code](https://claude.ai/code) and [Factory Droid](https://factory.ai) that audits any website for **Generative Engine Optimization (GEO)** readiness — how well-positioned a site is to appear in AI-generated search results from ChatGPT, Perplexity, Claude, and Google AI Overviews.

Run `/geo https://example.com` and get a scored audit report with actionable recommendations.

## What It Checks

The audit evaluates six dimensions, each weighted by research evidence linking that factor to actual AI citation behavior:

| Dimension | Weight | What It Measures |
|-----------|--------|-----------------|
| **Content Structure** | 25% | Heading hierarchy, semantic HTML, alt text, meta descriptions |
| **Content Quality** | 20% | Statistics, source citations, data tables, authorship, E-E-A-T signals |
| **Crawler Access** | 20% | robots.txt for AI crawlers, llms.txt, markdown negotiation, sitemap, Content-Signal headers |
| **Structured Data** | 15% | JSON-LD presence, schema types, required properties, validity |
| **FAQ / Q&A Content** | 15% | FAQPage schema, on-page Q&A patterns, query alignment |
| **Content Freshness** | 5% | Date metadata in JSON-LD, meta tags, HTTP headers |

Plus a supplemental **OG metadata** check (not scored, but reported).

The audit also auto-discovers and checks 3-5 key subpages (about, FAQ, services, contact, blog).

## Output

A structured report with:
- Overall score (0-100) with Good / Needs Improvement / Critical rating
- Per-dimension scores and detailed findings
- Specific issues found with severity levels
- Prioritized action items ranked by effort vs. impact
- Notes on important GEO factors outside scan scope (brand search volume, cross-platform presence, third-party mentions)

## Installation

### Claude Code

Copy the command and its reference files to your personal commands directory:

```bash
# Clone the repo
git clone https://github.com/adamprime/geo-readiness-claudecode.git
cd geo-readiness-claudecode

# Copy to your Claude Code commands directory
cp .claude/commands/geo.md ~/.claude/commands/geo.md
mkdir -p ~/.claude/commands/geo/references
cp .claude/commands/geo/references/methodology.md ~/.claude/commands/geo/references/methodology.md
```

Then restart Claude Code. The `/geo` command will be available in any project.

### Factory Droid

Copy the skill to your personal skills directory:

```bash
# Clone the repo
git clone https://github.com/adamprime/geo-readiness-claudecode.git
cd geo-readiness-claudecode

# Copy to your Factory Droid skills directory
mkdir -p ~/.factory/skills/geo/references
cp .factory/skills/geo/SKILL.md ~/.factory/skills/geo/SKILL.md
cp .factory/skills/geo/references/methodology.md ~/.factory/skills/geo/references/methodology.md
```

Then restart Droid. The `/geo` command will be available, and Droid will also auto-invoke the skill when you mention GEO readiness, AI search optimization, llms.txt, etc.

### Project-Level Installation

To add the skill to a specific project (so teammates get it too), copy to the project's `.factory/skills/` or `.claude/commands/` directory instead:

```bash
# Factory Droid (project-level)
mkdir -p .factory/skills/geo/references
cp path/to/geo-readiness-claudecode/.factory/skills/geo/SKILL.md .factory/skills/geo/SKILL.md
cp path/to/geo-readiness-claudecode/.factory/skills/geo/references/methodology.md .factory/skills/geo/references/methodology.md

# Claude Code (project-level)
cp path/to/geo-readiness-claudecode/.claude/commands/geo.md .claude/commands/geo.md
mkdir -p .claude/commands/geo/references
cp path/to/geo-readiness-claudecode/.claude/commands/geo/references/methodology.md .claude/commands/geo/references/methodology.md
```

## Usage

```
/geo https://example.com
```

Or just describe what you want:

```
Can you audit mysite.com for AI search readiness?
```

(Factory Droid will auto-invoke the skill based on context.)

## Research Basis

The scoring methodology is grounded in published research including:

- Princeton GEO Paper (Aggarwal et al., ACM SIGKDD 2024) — foundational GEO framework
- Presence AI (Feb 2026) — 1,200+ page citation rate analysis
- Hashmeta (Jan 2025) — 100K ChatGPT response analysis
- AirOps (Jul 2025) — 12,000+ URL citation pattern study
- DiscoveredLabs (Dec 2025) — AI citation overlap analysis
- Cloudflare Markdown for Agents (Feb 2026) — emerging content negotiation standard

Full methodology with all citations is in `references/methodology.md`.

## What is GEO?

Traditional SEO optimizes for ten blue links. **Generative Engine Optimization (GEO)** addresses a different question: when an AI constructs a narrative answer to a user's query, does your business appear in that answer?

Only 12% of AI citations overlap with Google's top 10 search results. Ranking well on Google does not guarantee visibility in AI search — and vice versa.

## GEO Signal

This skill is based on the research behind [GEO Signal](https://geosignal.dev/) — a platform for auditing and monitoring AI search readiness. If you want a full hosted audit with ongoing monitoring, check it out.

## License

MIT
