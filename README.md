# GEO Readiness Audit — Claude Code & Factory Droid Skill

A skill/command for [Claude Code](https://claude.ai/code) and [Factory Droid](https://factory.ai) that audits any web project for **Generative Engine Optimization (GEO)** readiness — how well-positioned it is to appear in AI-generated search results from ChatGPT, Perplexity, Claude, and Google AI Overviews.

**Primary mode:** Run `/geo` in your project directory to audit your source code before you ship.

**Secondary mode:** Run `/geo https://example.com` to audit a live site.

## Why Local-First?

The real value is catching GEO issues while you're developing, not after you've shipped. This skill analyzes your source files directly — page templates, layout components, static assets, hosting config — and tells you what's missing before it goes live. It understands Next.js, Nuxt, SvelteKit, Astro, Hugo, Jekyll, plain HTML, and other web frameworks.

## What It Checks

The audit evaluates six dimensions, each weighted by research evidence linking that factor to actual AI citation behavior:

| Dimension | Weight | What It Measures |
|-----------|--------|-----------------|
| **Content Structure** | 25% | Heading hierarchy, semantic HTML, alt text, meta descriptions |
| **Content Quality** | 20% | Statistics, source citations, data tables, authorship, E-E-A-T signals |
| **Crawler Access** | 20% | robots.txt for AI crawlers, llms.txt, markdown for agents, sitemap, Content-Signal headers |
| **Structured Data** | 15% | JSON-LD presence, schema types, required properties, validity |
| **FAQ / Q&A Content** | 15% | FAQPage schema, on-page Q&A patterns, query alignment |
| **Content Freshness** | 5% | Date metadata in JSON-LD, meta tags, HTTP headers |

Plus a supplemental **OG metadata** check (not scored, but reported).

### Markdown for Agents

The skill checks for both approaches to making your content available to AI in markdown:

- **Cloudflare Markdown for Agents** — edge-level HTML-to-markdown conversion (Pro+ plans)
- **Self-hosted** — serving `.md` versions of key pages with `<link rel="alternate" type="text/markdown">` tags and proper `Content-Type` headers. Works with any hosting provider (Netlify, Vercel, Apache, Nginx, etc.)

## Output

A structured report with:
- Overall score (0-100) with Good / Needs Improvement / Critical rating
- Per-dimension scores and detailed findings
- Specific issues with severity levels and **file paths** (in local mode)
- Prioritized action items ranked by effort vs. impact
- Notes on important GEO factors outside scan scope

## Installation

### Claude Code

```bash
git clone https://github.com/adamprime/geo-readiness-claudecode.git
cd geo-readiness-claudecode

cp .claude/commands/geo.md ~/.claude/commands/geo.md
mkdir -p ~/.claude/commands/geo/references
cp .claude/commands/geo/references/methodology.md ~/.claude/commands/geo/references/methodology.md
```

Restart Claude Code. `/geo` is now available in any project.

### Factory Droid

```bash
git clone https://github.com/adamprime/geo-readiness-claudecode.git
cd geo-readiness-claudecode

mkdir -p ~/.factory/skills/geo/references
cp .factory/skills/geo/SKILL.md ~/.factory/skills/geo/SKILL.md
cp .factory/skills/geo/references/methodology.md ~/.factory/skills/geo/references/methodology.md
```

Restart Droid. `/geo` is available in any project, and Droid will also auto-invoke the skill when you mention GEO readiness, AI search optimization, etc.

### Project-Level Installation

To share with teammates, copy into the project's `.factory/skills/` or `.claude/commands/` directory instead of `~/`.

## Usage

```
# Audit the project you're currently working on (primary use case)
/geo

# Audit a live site
/geo https://example.com
```

Or describe what you want naturally:

```
Check this project for GEO readiness before I ship
```

## Research Basis

The scoring methodology is grounded in published research including:

- Princeton GEO Paper (Aggarwal et al., ACM SIGKDD 2024) — foundational GEO framework
- Presence AI (Feb 2026) — 1,200+ page citation rate analysis
- Hashmeta (Jan 2025) — 100K ChatGPT response analysis
- AirOps (Jul 2025) — 12,000+ URL citation pattern study
- DiscoveredLabs (Dec 2025) — AI citation overlap analysis
- Cloudflare Markdown for Agents (Feb 2026) — emerging content negotiation standard

Full methodology with all citations is in the `references/methodology.md` files.

## What is GEO?

Traditional SEO optimizes for ten blue links. **Generative Engine Optimization (GEO)** addresses a different question: when an AI constructs a narrative answer to a user's query, does your business appear in that answer?

Only 12% of AI citations overlap with Google's top 10 search results. Ranking well on Google does not guarantee visibility in AI search — and vice versa.

## GEO Signal

This skill is based on the research behind [GEO Signal](https://geosignal.dev/) — a platform for auditing and monitoring AI search readiness. If you want a full hosted audit with ongoing monitoring, check it out.

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for version history.

## License

MIT
