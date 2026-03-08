---
name: geo
description: Run a Generative Engine Optimization (GEO) readiness audit on the current project or a live website. This skill should be used when the user asks to "audit for AI search readiness", "check GEO readiness", "run a geo audit", "check if this site is ready for AI search engines", "analyze AI visibility", "check my project for GEO", or mentions generative engine optimization, llms.txt, AI crawlers, AI citation readiness, or shipping a site with good AI discoverability.
---

# GEO Readiness Audit

Audit a web project for Generative Engine Optimization (GEO) readiness — how well-positioned it is to appear in AI-generated search results from ChatGPT, Perplexity, Claude, and Google AI Overviews.

## Mode Selection

**If $ARGUMENTS is empty or not a URL:** Run in **local project mode** (primary). Analyze the source files in the current working directory.

**If $ARGUMENTS is a URL:** Run in **live site mode** (secondary). Fetch and analyze the published site.

## Reference Material

Before starting, read the detailed methodology and scoring criteria in the co-located `references/methodology.md` file. Check `~/.factory/skills/geo/references/methodology.md` (personal install) or `.factory/skills/geo/references/methodology.md` (project install).

---

## LOCAL PROJECT MODE (Primary)

This is the main use case: audit the project you're actively developing before it ships.

### Phase 1: Discover Project Structure

Use Glob and LS to understand the project layout:

1. **Identify the framework** — Look for `package.json`, `next.config.*`, `nuxt.config.*`, `astro.config.*`, `svelte.config.*`, `vite.config.*`, `netlify.toml`, `vercel.json`, `_config.yml`, `hugo.toml`, or a plain HTML project
2. **Find the public/static directory** — Check for `public/`, `static/`, `dist/`, `out/`, `build/`, or root-level static files
3. **Find page templates** — Scan for `.html`, `.jsx`, `.tsx`, `.vue`, `.svelte`, `.astro`, `.md`, `.mdx` files in pages/routes directories
4. **Find layout/head components** — Look for root layout, `_app`, `_document`, `head` components, or `<head>` sections where meta tags and structured data live

### Phase 2: Analyze Six Dimensions

Score each dimension 0-100.

#### 1. Content Structure (25% weight)

Use Grep and Read to analyze page templates and content files:

- **Heading hierarchy**: Grep for `<h1`, `<h2`, `<h3` (or JSX/framework equivalents). Check for single H1 per page, logical nesting
- **Semantic HTML**: Grep for `<article`, `<section`, `<main`, `<nav`, `<header`, `<footer` across templates
- **Image alt text**: Grep for `<img` tags and check for `alt=` attributes. Calculate coverage
- **Meta description**: Check layout/head components for `<meta name="description"` or framework-specific meta config (Next.js `metadata`, Nuxt `useHead`, etc.)
- **Canonical URL**: Check for `<link rel="canonical"` in head components

#### 2. Content Quality (20% weight)

Read content files (.md, .mdx, .html) and analyze:

- **Content depth**: Estimate word count of actual content. Flag pages with <500 words
- **Statistics/data**: Look for numeric data, percentages, data tables
- **Citations**: Look for external links, reference patterns, blockquotes with attribution
- **Author attribution**: Check for author meta tags, bylines in templates, author structured data
- **E-E-A-T signals**: First-person experience, credentials, original data

#### 3. Crawler Access (20% weight)

Check the public/static directory for these files:

- **robots.txt**: Does it exist? Does it explicitly allow AI search crawlers (OAI-SearchBot, ChatGPT-User, PerplexityBot, Claude-SearchBot)? Does it block any? Does it distinguish between training crawlers and search crawlers?
- **llms.txt**: Does it exist at the root? Is it well-structured with a title, description, and links to key content? Check both root and `.well-known/` paths
- **sitemap.xml**: Does it exist? Does it reference key pages?
- **Markdown for agents**: This has two implementation paths:
  - **Self-hosted** (works with any hosting): Check for `.md` versions of key pages (e.g., `index.md` alongside `index.html`). Check HTML for `<link rel="alternate" type="text/markdown"` tags pointing to markdown versions. Check hosting config (netlify.toml, vercel.json, .htaccess, nginx config) for `Content-Type: text/markdown` headers on `.md` files
  - **Cloudflare**: Check for Cloudflare config or `wrangler.toml` that might indicate Markdown for Agents is enabled at the edge
- **Content-Signal headers**: Check hosting config for `Content-Signal` response headers

#### 4. Structured Data (15% weight)

Grep for `application/ld+json` across all templates and pages:

- Is JSON-LD present at all?
- What schema types are defined? (Organization, LocalBusiness, Product, FAQ, Article, BreadcrumbList, WebSite, WebPage)
- Are required properties populated per type?
- Is the JSON valid and properly structured?
- Check for framework-specific structured data patterns (next-seo, nuxt schema.org, etc.)

#### 5. FAQ / Q&A Content (15% weight)

Search across content files and templates:

- **FAQPage schema**: Grep for FAQPage in JSON-LD blocks
- **FAQ page patterns**: Look for pages/routes named "faq", "questions", "help"
- **On-page Q&A**: Heading patterns with question marks, definition lists, Q&A component patterns
- **Quantity and quality**: Count FAQ items. Ideal: 8-12 per page, 50-120 words per answer

#### 6. Content Freshness (5% weight)

Check for date signals:

- `datePublished` and `dateModified` in JSON-LD structured data
- `article:published_time` / `article:modified_time` meta tags
- Date patterns in content frontmatter (common in MD/MDX files)
- Score leniently for homepages (they often lack publish dates)

### Phase 3: OG Metadata (supplemental, not scored)

Check head/layout components for:

- `og:title`, `og:description`, `og:image`, `og:url`, `og:type`, `og:site_name`
- `twitter:card`, `twitter:title`, `twitter:description`, `twitter:image`
- Is `og:image` pointing to an actual image file that exists in the project?
- Check for framework-specific OG implementations (Next.js `opengraph-image`, Nuxt `defineOgImage`, etc.)

---

## LIVE SITE MODE (Secondary)

When a URL is provided, fetch and analyze the published site.

### Phase 1: Fetch Core Resources

Use `curl` for all requests:

1. `curl -sL -D- <url>` — target URL with headers and body
2. `curl -sL <domain>/robots.txt`
3. `curl -sL <domain>/llms.txt` and `curl -sL <domain>/.well-known/llms.txt`
4. `curl -sL <domain>/sitemap.xml`
5. `curl -sL -H "Accept: text/markdown" -D- <url>` — test markdown content negotiation
6. Discover and fetch 3-5 key subpages from homepage links (about, FAQ, services, contact, blog)

### Phase 2: Same Six Dimensions

Apply the same six-dimension analysis to the fetched HTML content. See methodology reference for detailed scoring criteria.

### Phase 3: OG Metadata

Check the fetched HTML for OG and Twitter Card meta tags.

---

## Output Format

Present the audit as a structured report:

```
# GEO Readiness Audit: [project name or domain]
Mode: [Local Project | Live Site]
Audited: [date]
Framework: [detected framework, if local mode]
Pages/files analyzed: [list]

## Overall Score: [X]/100 — [Good|Needs Improvement|Critical]

## Dimension Scores

| Dimension | Score | Rating | Weight |
|-----------|-------|--------|--------|
| Content Structure | X/100 | [rating] | 25% |
| Content Quality | X/100 | [rating] | 20% |
| Crawler Access | X/100 | [rating] | 20% |
| Structured Data | X/100 | [rating] | 15% |
| FAQ / Q&A Content | X/100 | [rating] | 15% |
| Content Freshness | X/100 | [rating] | 5% |

## Detailed Findings

### [Dimension Name] — [Score] [Rating]

**What's working:**
- [positive findings]

**Issues found:**
- [Severity: Critical|Warning|Info] [finding with explanation]

**Recommendations:**
- [specific, actionable recommendation with file paths for local mode]

[repeat for each dimension]

### OG Metadata (Supplemental)
[findings and recommendations]

## Priority Actions

Top 3-5 highest-impact changes ranked by effort vs. impact:

1. [action] — [why it matters] — [estimated effort] — [file to edit]
2. ...

## What We Didn't Check

Note these important GEO factors that require analysis beyond this scan:
- Brand search volume (strongest predictor of AI citation)
- Cross-platform presence (social, directories, third-party mentions)
- Third-party mentions (91% of AI answers cite third-party sources)
- Content on pages not analyzed in this scan
```

## Rating Scale

| Rating | Score | Meaning |
|--------|-------|---------|
| Good | 70-100 | Well-optimized for AI search |
| Needs Improvement | 40-69 | Opportunities exist to improve AI visibility |
| Critical | 0-39 | Significant issues likely reducing AI search visibility |

## Scoring Notes

- The overall score is the weighted average of all six dimensions
- In local mode, point to specific files and line numbers where issues were found
- In local mode, provide code snippets or examples when recommending fixes
- Be generous when signals are present but imperfect
- Be strict when critical signals are completely absent
- OG metadata does not factor into the numeric score
