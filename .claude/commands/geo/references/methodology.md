# GEO Readiness Audit — Detailed Methodology

## Background: SEO vs GEO

Traditional SEO optimizes for ten blue links. Generative Engine Optimization (GEO) addresses a different question: when an AI constructs a narrative answer, does your business appear?

Key research finding: Only 12% of AI citations overlap with Google's top 10 search results (DiscoveredLabs, 2025). Ranking well on Google does not guarantee visibility in AI search.

---

## Dimension 1: Content Structure (25% weight)

### What to check

- **H1 tag**: Exactly one per page, clearly describing the page topic
- **Heading hierarchy**: H2s under H1, H3s under H2s — no skipped levels
- **Semantic HTML**: `<article>`, `<section>`, `<main>`, `<nav>`, `<header>`, `<footer>` presence
- **Text-to-HTML ratio**: Extract all visible text vs total HTML size. Ratio above 25% is good, below 10% is concerning
- **Image alt text**: Count images with and without `alt` attributes. Calculate coverage percentage
- **Meta description**: Present in `<meta name="description">`, 120-160 characters ideal
- **Canonical URL**: `<link rel="canonical">` present and pointing to correct URL

### Scoring guidance

| Score Range | Criteria |
|-------------|----------|
| 80-100 | Single H1, logical heading hierarchy, semantic HTML throughout, >90% alt text coverage, meta description present |
| 60-79 | Minor heading issues (occasional skip), some semantic HTML, >70% alt text, meta description present |
| 40-59 | Multiple H1s or poor hierarchy, minimal semantic HTML, <70% alt text |
| 0-39 | No heading structure, no semantic HTML, most images lack alt text, no meta description |

### Research basis

- Pages with clear H2/H3 hierarchy achieve citation rates 3.2x higher (Presence AI, 1,200+ pages, Feb 2026)
- Sequential heading structure increases citation odds by 2.8x (AirOps, 12,000+ URLs, Jul 2025)
- Clearly organized content sections account for ~50% of all AI citations (AirOps, 2025)

---

## Dimension 2: Content Quality (20% weight)

### What to check

- **Statistics density**: Count numeric data points, percentages, dollar amounts. Look for patterns like "X%", "$X", "X million", data in tables
- **Source citations**: External links, reference patterns ("according to...", "research shows...", "[Source]")
- **Data tables**: `<table>` elements with meaningful data (not layout tables)
- **Author attribution**: Bylines, author bio sections, `<meta name="author">`
- **Content depth**: Word count of visible text content. Flag <500 words as thin
- **Expert quotations**: `<blockquote>` elements, quotation patterns with attribution
- **E-E-A-T signals**: First-person experience, credentials mentioned, original research/data

### Scoring guidance

| Score Range | Criteria |
|-------------|----------|
| 80-100 | Multiple statistics, cited sources, data tables, clear authorship, >1500 words |
| 60-79 | Some statistics or citations, decent depth, some authorship signals |
| 40-59 | Minimal data, few citations, thin content (500-1000 words) |
| 0-39 | No statistics, no citations, very thin content (<500 words), no authorship |

### Research basis

- Princeton GEO study: citing sources, adding statistics, and adding quotations improved AI visibility by up to 40% (Aggarwal et al., ACM SIGKDD 2024)
- Comprehensive guides with data tables achieve 67% citation rates (Presence AI, 2026)
- Content with visual data elements increases citation likelihood by 89% (Presence AI, 2026)
- 1,500+ word content cited 4.7x more (Hashmeta, 100K responses, 2025)
- First-person narratives get 67% more citations (Hashmeta, 2025)

---

## Dimension 3: Crawler Access (20% weight)

### What to check

#### robots.txt analysis

Parse for User-agent rules targeting these crawlers:

**Search/citation crawlers** (blocking = invisible in AI answers):
- `OAI-SearchBot` — OpenAI's search crawler
- `ChatGPT-User` — ChatGPT browsing feature
- `PerplexityBot` — Perplexity's crawler
- `Claude-SearchBot` — Anthropic's search crawler

**Training crawlers** (blocking is a legitimate business decision):
- `GPTBot` — OpenAI's training crawler
- `ClaudeBot` / `anthropic-ai` — Anthropic's training crawler
- `Google-Extended` — Google's AI training crawler
- `CCBot` — Common Crawl
- `Bytespider` — TikTok/ByteDance
- `Meta-ExternalAgent` — Meta's AI crawler
- `Applebot-Extended` — Apple's AI crawler

**Key distinctions to report:**
- Blocking search crawlers is CRITICAL (removes site from AI answers entirely)
- Blocking training crawlers is a CHOICE (may reduce long-term AI knowledge but doesn't affect search citation)
- Overly broad `Disallow: /` rules that inadvertently block everything

#### llms.txt

- Check `<domain>/llms.txt` and `<domain>/.well-known/llms.txt`
- If present, evaluate structure: does it contain title, description, and links to key content?
- The llms.txt spec (Jeremy Howard, 2024) proposes a curated summary of a site's most important content for AI consumption

#### Markdown content negotiation

- Send request with `Accept: text/markdown` header
- If response returns `content-type: text/markdown`, the site supports markdown for agents
- Check for `x-markdown-tokens` header (token count estimate)
- This is supported by Cloudflare (Pro+ plans), Vercel, and Laravel

#### Content-Signal header

- Check HTTP response headers for `Content-Signal`
- Expected values: `ai-train=yes/no`, `search=yes/no`, `ai-input=yes/no`
- From contentsignals.org, emerging standard for AI usage preferences

#### Sitemap

- Check `<domain>/sitemap.xml`
- Also check robots.txt for `Sitemap:` directive
- Evaluate: does it exist, does it reference key pages, is it valid XML

### Scoring guidance

| Score Range | Criteria |
|-------------|----------|
| 80-100 | Search crawlers allowed, llms.txt present, markdown negotiation works, sitemap exists |
| 60-79 | Search crawlers allowed (or not mentioned = default allow), sitemap exists, missing llms.txt or markdown |
| 40-59 | Some crawlers blocked indiscriminately, no llms.txt, no markdown support |
| 0-39 | Search crawlers explicitly blocked, no sitemap, broad Disallow rules |

### Research basis

- Blocking OAI-SearchBot prevents site from appearing in ChatGPT search results entirely (OpenAI docs)
- Allowing GPTBot "can significantly enhance your site's visibility in AI search platforms" (RathoreSEO, Nov 2025)
- Markdown content negotiation is an emerging standard adopted by Cloudflare, Vercel, and Laravel (Feb 2026)
- llms.txt proposed standard gaining industry adoption (Jeremy Howard, 2024)

---

## Dimension 4: Structured Data (15% weight)

### What to check

- Extract all `<script type="application/ld+json">` blocks
- Parse as JSON and validate structure
- Identify schema types present

**Priority schema types and required properties:**

| Schema Type | Required Properties | Nice to Have |
|-------------|-------------------|--------------|
| Organization | name, url, logo | sameAs, contactPoint, description |
| LocalBusiness | name, address, telephone | openingHours, geo, priceRange, image |
| Product | name, description | offers, image, brand, aggregateRating |
| FAQPage | mainEntity (Question + acceptedAnswer) | — |
| Article | headline, author, datePublished | dateModified, image, publisher |
| BreadcrumbList | itemListElement | — |
| WebSite | name, url | potentialAction (SearchAction) |
| WebPage | name, description | — |

**Check for common errors:**
- Missing `@context` ("https://schema.org")
- Invalid `@type` values
- Empty or null required properties
- Multiple conflicting Organization schemas
- Inconsistency between structured data and visible content

### Scoring guidance

| Score Range | Criteria |
|-------------|----------|
| 80-100 | Multiple relevant schema types, all required properties present, technically valid |
| 60-79 | At least Organization/LocalBusiness schema, mostly complete, valid JSON |
| 40-59 | Minimal schema (only WebSite or basic), missing key properties |
| 0-39 | No JSON-LD at all, or invalid/broken structured data |

### Research basis

- Pages with rich schema are 13% more likely to receive AI citations (AirOps, 2025)
- Schema markup increases citation frequency by 2.3x (Hashmeta, 2025)
- However: "Higher schema coverage does NOT correlate with increased citation frequency" (SearchAtlas, 2025)
- Schema makes content "parseable but not recommendable" — it helps AI understand you but doesn't alone trigger citation (Typescape, Jan 2026)

---

## Dimension 5: FAQ / Q&A Content (15% weight)

### What to check

- **FAQPage schema**: Look for FAQPage type in JSON-LD with Question/Answer pairs
- **On-page FAQ patterns**: Headings containing "FAQ", "Questions", "Q:", definition lists (`<dl>/<dt>/<dd>`), question-answer pair patterns
- **FAQ quantity**: 8-12 high-intent FAQs per page is ideal
- **Answer quality**: 50-120 words per answer is optimal for AI extraction
- **Query alignment**: Do the questions match how users would ask AI about this business/topic?

### Scoring guidance

| Score Range | Criteria |
|-------------|----------|
| 80-100 | FAQPage schema present with 8+ Q&A pairs, answers are 50-120 words, questions align with AI queries |
| 60-79 | Some FAQ content (schema or on-page), 4+ questions, reasonable answers |
| 40-59 | Minimal FAQ content, <4 questions, or answers too short/too long |
| 0-39 | No FAQ content at all, neither in schema nor on-page |

### Research basis

- FAQ-heavy content achieves 58% citation rates across AI platforms (Presence AI, 2026)
- Google AI Overviews value FAQ schema at 71% citation rate — highest of any format (Presence AI, 2026)
- Recommended: 8-12 high-intent FAQs per page, 50-120 word answers, FAQPage JSON-LD markup (Daniel Martin, Feb 2026)
- Listicles and how-to guides (Q&A formats) make up 72% of total AI citations (Hashmeta, 2025)

---

## Dimension 6: Content Freshness (5% weight)

### What to check

- `datePublished` and `dateModified` in JSON-LD structured data
- `<meta property="article:published_time">` and `article:modified_time`
- `Last-Modified` HTTP response header
- Visible dates on the page (byline dates, "Updated on...", copyright year)
- How recent: <6 months is great, 6-12 months is okay, >12 months is stale

### Scoring guidance

| Score Range | Criteria |
|-------------|----------|
| 80-100 | Multiple date signals present, content updated within 6 months |
| 60-79 | Some date signals, content within 12 months |
| 40-59 | Only copyright year or minimal date signals |
| 0-39 | No date information detectable anywhere |

### Research basis

- Articles published within 6 months receive 3.2x more AI citations (Hashmeta, 2025)
- The presence of date metadata is itself a trust signal for AI systems

### Note on homepages

Homepages often lack publish dates — this is normal. Score leniently for homepages. The freshness check becomes more meaningful on blog posts and content pages found during subpage discovery.

---

## OG Metadata (Supplemental — not scored)

### What to check

**Open Graph tags:**
- `og:title` — present, matches page title
- `og:description` — present, 60-200 characters
- `og:image` — present, valid URL, recommended 1200x630px
- `og:url` — present, matches canonical
- `og:type` — present (website, article, etc.)
- `og:site_name` — present

**Twitter Card tags:**
- `twitter:card` — present (summary_large_image preferred)
- `twitter:title`, `twitter:description`, `twitter:image`

### Why it matters

OG metadata doesn't directly influence AI citation, but it affects how content appears when shared across platforms. Cross-platform presence is a significant factor in AI citation (Digital Bloom, 2025), and poor OG metadata reduces shareability and visibility.

---

## Subpage Discovery Strategy

When discovering subpages to analyze:

1. Parse homepage HTML for internal links
2. Prioritize these page types (in order):
   - About / About Us
   - Services / Products / What We Do
   - FAQ / Questions
   - Contact
   - Blog (most recent post)
3. Fetch 3-5 subpages maximum to keep the audit manageable
4. Apply the same dimension checks to each subpage
5. Report per-page findings but roll up into a single overall score

---

## Platform-Specific Context

Different AI platforms have different citation preferences. Note these when making recommendations:

| Platform | Content Preference | Citation Style |
|----------|-------------------|----------------|
| Perplexity | Data-driven content (64% citation rate) | Includes source URLs (most measurable) |
| ChatGPT | Structured comparisons (63%) | Entity matching, doesn't always cite URLs |
| Claude | Comprehensive guides (69%) | Conservative, emphasizes technical precision |
| Google AI Overviews | FAQ schema (71% citation rate) | Source links from SERP data |

---

## Factors Outside Scan Scope

Always note these in the report — they are critical GEO factors that cannot be determined from a page scan:

1. **Brand search volume** — Strongest predictor of LLM citation (0.334 correlation, Digital Bloom, 2025)
2. **Cross-platform presence** — Sites active on multiple platforms are significantly more likely to be cited
3. **Third-party mentions** — 91% of AI answers cite third-party sources rather than brand-owned content (LinkSurge, 2026)
4. **Backlink profile** — Less relevant for AI than traditional SEO, but still a factor
5. **Content on other pages** — A full-site audit would analyze more pages than this scan covers

---

## Sources

1. Aggarwal, P. et al. "GEO: Generative Engine Optimization." ACM SIGKDD 2024. Princeton University, IIT Delhi.
2. Presence AI. "AI Citation Rates Research." February 2026.
3. Hashmeta. "We Analyzed 100,000 ChatGPT Responses to Find What Gets Cited." January 2025.
4. AirOps. "Why Ranking on Page One Isn't Enough." July 2025.
5. SearchAtlas. "The Limits of Schema Markup for AI Search." 2025.
6. The Digital Bloom. "2025 AI Citation & LLM Visibility Report." December 2025.
7. DiscoveredLabs. "AI Citation Patterns." December 2025.
8. LinkSurge. "91% of AI Answers Come from Sites That Aren't Yours." February 2026.
9. Typescape. "Schema for AI Search: What's Worth Implementing in 2026." January 2026.
10. Martin, D. "FAQ schema still drives AI citations." February 2026.
11. Cloudflare. "Markdown for Agents." February 2026.
12. Howard, J. "llms.txt specification." 2024.
