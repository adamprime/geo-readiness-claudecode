# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/), and this project adheres to [Semantic Versioning](https://semver.org/).

## [0.2.0] - 2026-03-07

### Changed
- **Local project mode is now the primary use case.** Running `/geo` with no URL analyzes source files on disk using Glob/Grep/Read instead of fetching a live site. Live site mode is still available by passing a URL.
- Skill detects project framework (Next.js, Nuxt, SvelteKit, Astro, Hugo, Jekyll, plain HTML) and adapts checks accordingly.
- Local mode reports specific file paths alongside findings so you can fix issues immediately.

### Added
- Framework-specific guidance in methodology reference (where to find pages, layouts, meta config, structured data per framework).
- **Markdown for agents: self-hosted approach.** Checks for `.md` versions of pages, `<link rel="alternate" type="text/markdown">` tags, and hosting config headers (netlify.toml, vercel.json). Works with any hosting provider, not just Cloudflare.
- Hosting config analysis for Content-Type and Content-Signal headers.

## [0.1.0] - 2026-03-07

### Added
- Initial release with six scored dimensions: Content Structure (25%), Content Quality (20%), Crawler Access (20%), Structured Data (15%), FAQ/Q&A Content (15%), Content Freshness (5%).
- Supplemental OG metadata check (not scored).
- Detailed methodology reference with research citations from Princeton GEO Paper, Presence AI, Hashmeta, AirOps, DiscoveredLabs, and others.
- Support for both Claude Code (`.claude/commands/`) and Factory Droid (`.factory/skills/`).
- Auto-discovery of key subpages in live site mode.
- AI crawler classification: distinguishes search/citation crawlers from training crawlers in robots.txt analysis.
- llms.txt, sitemap.xml, and Cloudflare Markdown for Agents detection.
