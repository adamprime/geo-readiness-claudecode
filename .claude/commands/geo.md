---
description: Run a Generative Engine Optimization (GEO) readiness audit on a website URL
argument-hint: <url>
allowed-tools: [Read, Grep, Glob, Bash, WebSearch, FetchUrl]
---

# GEO Readiness Audit

Perform a comprehensive Generative Engine Optimization readiness audit on the provided URL. GEO evaluates how well a website is positioned to appear in AI-generated search results from ChatGPT, Perplexity, Claude, and Google AI Overviews.

## Target

The user provided: $ARGUMENTS

If no URL was provided, ask the user for one. The URL should be a website homepage or specific page to audit.

## Reference Material

Before starting, read the detailed methodology and scoring criteria in:
`~/.claude/commands/geo/references/methodology.md` (if installed globally) or the `references/methodology.md` file co-located with this command

## Audit Process

Execute all checks below. Use `curl` for HTTP requests. Parse HTML output to analyze content structure.

### Phase 1: Fetch Core Resources (run in parallel where possible)

1. **Fetch the target URL** — `curl -sL -D- <url>` to get both headers and HTML body
2. **Fetch robots.txt** — `curl -sL <domain>/robots.txt`
3. **Fetch llms.txt** — `curl -sL <domain>/llms.txt` and `curl -sL <domain>/.well-known/llms.txt`
4. **Fetch sitemap** — `curl -sL <domain>/sitemap.xml` (also check robots.txt for Sitemap: directive)
5. **Test markdown negotiation** — `curl -sL -H "Accept: text/markdown" -D- <url>` and check if response content-type is `text/markdown`
6. **Discover subpages** — From the homepage HTML, extract links to key internal pages: about, contact, FAQ, services/products, blog. Fetch the top 3-5 most important subpages.

### Phase 2: Analyze Six Dimensions

Score each dimension 0-100 based on the criteria in the methodology reference.

#### 1. Crawler Access (20% weight)

Check robots.txt for directives targeting these AI crawlers:
- **Search/citation crawlers** (blocking these removes you from AI answers): OAI-SearchBot, ChatGPT-User, PerplexityBot, Claude-SearchBot
- **Training crawlers** (blocking is a legitimate choice): GPTBot, ClaudeBot, anthropic-ai, Google-Extended, CCBot, Bytespider, Meta-ExternalAgent, Applebot-Extended

Also check:
- Does llms.txt exist? Is it well-structured with key content pointers?
- Does the site support markdown content negotiation (Accept: text/markdown)?
- Is there a Content-Signal header in HTTP responses?
- Does sitemap.xml exist and reference key pages?
- Are there any overly broad Disallow rules blocking AI crawlers inadvertently?

Score high if search crawlers are allowed, llms.txt exists, and markdown negotiation works. Score critically low if search crawlers are blocked.

#### 2. Content Structure (25% weight)

Analyze the HTML for:
- Single H1 tag with clear page topic
- Logical H2/H3 heading hierarchy (sequential, not skipping levels)
- Semantic HTML elements: `<article>`, `<section>`, `<main>`, `<nav>`, `<header>`, `<footer>`
- Text-to-HTML ratio (higher is better for AI extraction)
- Image `alt` text coverage (% of images with meaningful alt text)
- Meta description present and descriptive
- Canonical URL set

Check subpages for the same patterns. Flag JavaScript-heavy pages where content may not be accessible to crawlers.

#### 3. Content Quality (20% weight)

Analyze page content for:
- Statistics and quantitative data (numbers, percentages, data points)
- External source citations and references ("according to...", linked sources)
- Data tables present
- Author attribution visible
- Content depth (word count — flag if <500 words as thin)
- Expert quotations or original data
- E-E-A-T signals (experience, expertise, authoritativeness, trustworthiness)

#### 4. Structured Data (15% weight)

Extract and analyze `<script type="application/ld+json">` tags:
- Is JSON-LD present at all?
- Schema types found (Organization, LocalBusiness, Product, FAQ, Article, BreadcrumbList, WebSite, WebPage)
- Required properties per type — flag missing critical fields
- Technical validity (valid JSON, proper @context)
- Consistency between structured data and visible page content

#### 5. FAQ / Q&A Content (15% weight)

Check for:
- FAQPage schema markup
- FAQ content patterns on page (heading-based Q&A, definition lists, question-answer pairs)
- Number of FAQ items (8-12 per page is ideal)
- Answer length and quality (50-120 words per answer is optimal)
- Alignment with likely AI query patterns for the business type

#### 6. Content Freshness (5% weight)

Check for:
- `datePublished` and `dateModified` in JSON-LD
- Date-related meta tags (`article:published_time`, `article:modified_time`)
- `Last-Modified` HTTP header
- Visible date indicators on the page
- How recent the dates are (content <6 months old scores highest)

### Phase 3: OG Metadata (bonus check, not weighted in score)

Check for Open Graph and Twitter Card tags:
- `og:title`, `og:description`, `og:image`, `og:url`, `og:type`, `og:site_name`
- `twitter:card`, `twitter:title`, `twitter:description`, `twitter:image`
- Are values present and well-formed?
- Is og:image a valid, appropriately-sized URL?

Flag missing or incomplete OG metadata as an issue for content shareability and AI context.

## Output Format

Present the audit as a structured report:

```
# GEO Readiness Audit: [domain]
Audited: [date]
Pages analyzed: [list URLs checked]

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
- [specific, actionable recommendation]

[repeat for each dimension]

### OG Metadata (Supplemental)
[findings and recommendations]

## Priority Actions

Top 3-5 highest-impact changes ranked by effort vs. impact:

1. [action] — [why it matters] — [estimated effort]
2. ...

## What We Didn't Check

Note these important GEO factors that require analysis beyond a page scan:
- Brand search volume (strongest predictor of AI citation)
- Cross-platform presence (social, directories, third-party mentions)
- Backlink profile (less relevant for AI but still a factor)
- Full-site content audit (this scan covered [N] pages)
```

## Rating Scale

| Rating | Score | Meaning |
|--------|-------|---------|
| Good | 70-100 | Well-optimized for AI search |
| Needs Improvement | 40-69 | Opportunities exist to improve AI visibility |
| Critical | 0-39 | Significant issues likely reducing AI search visibility |

## Scoring Notes

- Be generous with scoring when signals are present but imperfect
- Be strict when critical signals are completely missing (no robots.txt, no structured data at all)
- The overall score is the weighted average of all six dimensions
- OG metadata does not factor into the numeric score but should be reported
