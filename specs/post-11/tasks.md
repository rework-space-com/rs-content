# Tasks — post-11.md

**Plan:** [plan.md](plan.md) | **Spec:** [spec.md](spec.md)

Skill legend: **[BWG]** = read/apply
[blog-writing-guide](../../.agents/skills/blog-writing-guide/SKILL.md);
**[CRW]** = read/apply
[content-research-writer](../../.agents/skills/content-research-writer/SKILL.md).

## Phase 0 — Setup
- [x] T001: Confirm publication date and final URL slug (resolves spec §7). *Provisional: 2026-08-12, `/blog/2026-08-12-merging-azure-databricks-costs-with-focus`; confirm before publish.*
- [x] T002: Create `assets/blog-images/post-11/` and `static/static-blog-images/post-11/` folders.
- [x] T003: Read both SKILL.md files in `.agents/skills/` in full. **[BWG]** **[CRW]**

## Phase 1 — Skeleton
- [x] T101: Fill `content/en/blog/post-11.md` (file exists, currently empty) with full front matter per spec FR-1: exact field order, single-quoted strings, spaced arrays, `draft: true`.
- [x] T102: Add H2/H3 heading tree from plan.md: short H2s in `color-text` shortcodes, descriptive H3s, with key points and `[Research needed: ...]` markers per section. **[CRW]**
- [x] T103: Gate — verify skeleton covers all six FR-2 sections and conforms to the FR-5 structure contract. **[BWG]** **[Review Gate 1]**

## Phase 2 — Draft
Each writing task (T201–T207) ends with a section review per the
content-research-writer feedback workflow. **[CRW]**
- [x] T201: Write intro: fragmented-billing problem + ITFM framing (problem stated in first 3 sentences); iterate the hook until it stops a scrolling developer. **[BWG]** **[CRW]**
- [x] T202: Write "What is FOCUS?" definitional section.
- [x] T203: Write ingestion/raw-formats section (Azure FOCUS exports vs. Databricks system tables).
- [x] T204: Write Data Engineer section (Auto Loader, join keys, grain alignment, serverless/classic, MERGE INTO).
- [x] T205: Write Data Analyst section (semantic gap, enrichment, dashboards, AI/BI Genie).
- [x] T206: Write architecture-deferral tease (DMLZ/DLZ, Part 2 promise, ≤2 paragraphs).
- [x] T207: Write ROI + conclusion with cited numbers and CTA.
- [x] T208: Insert all external links as `color-link` shortcodes; strip plan's bracket citations; verify every claim has a citation. **[CRW]**
- [x] T209: Gate — full-draft review for flow, clarity, and consistency, then self-check against acceptance criteria. **[CRW]** **[BWG]** **[Review Gate 2]**

## Phase 3 — Assets
- [x] T301: Create comparative pipeline flow diagram → shipped as `assets/blog-images/post-11/traditional-vs-focus-based.png` (Picture 1). *Earlier `focus-unified-pipeline-schematic.jpg` iteration and its origin PNG remain on disk but are unused.*
- [x] T302: Create transformation matrix (shipped as in-body Markdown table; resolves spec §7 decision).
- [x] T303: Hero + sharing images shipped: `cloud-cost-analytics-concept.jpg` (featured, + `-origin`), `unified-cost-lakehouse-{short,share,twitter-share}.jpg`. *Share/summary artwork reuses post-10 visuals under spec names; replace when final artwork is ready.*
- [x] T304: Wire all images with `img` shortcodes, blank lines around each, sequential `Picture N.` alt captions, and `(Picture N)` cross-references in prose.
- [x] T305: Add TCO dashboard screenshot → `assets/blog-images/post-11/tco-dashboard-unified-costs.png` (Picture 2).
- [x] T306: Add Governance Hub screenshots → `assets/blog-images/post-11/governance-hub-overview.png` (Picture 3) and `governance-hub-cost-page.png` (Picture 4), supporting the new H3 subsection (spec §6 Deviation 3).

## Phase 4 — Compliance & Publish
- [x] T401: Banned-language sweep (constitution §II), em-dash sweep, and AI-writing-pattern sweep. **[BWG]**
- [x] T402: Heading audit: H2s are 1–4 word `color-text` labels; every H3 conveys information and carries a target keyword. **[BWG]**
- [x] T403: Local Hugo build (docker compose) passes after moving the in-body image to `assets/blog-images/post-11/`.
- [x] T404: Structure audit — side-by-side diff of post-11.md against post-10.md: front matter field order/quoting, shortcode grammar (color-text, color-link, img), emphasis conventions, no raw Markdown links/HTML (spec FR-5).
- [x] T405: Final human review (author edits: Terraform links switched to PerfectThymeTech upstream, image renames, Picture 1 replaced with `traditional-vs-focus-based.png`, Governance Hub subsection added, "data janitors" claim softened and cited — spec §6 Deviations 2–5). **[Review Gate 3]**
- [x] T406: `draft: false` set; `date`/`url` confirmed as 2026-08-12.

## Phase 5 — SEO & AI search optimization (spec FR-6, post-publication)
- [x] T601: Keyword-placement audit: cluster present in title, slug, `keywords`, ≥ 3 H3s, first 100 words, ≥ 1 image alt (FR-6.1). **[BWG]**
- [x] T602: Verify "What is FOCUS?" opens with a self-contained 40–60-word quotable definition (FR-6.2).
- [x] T603: Answer-first audit: rewrite any H2/H3 section whose opening sentence does not directly answer the heading's implied question (FR-6.3). **[BWG]** *Reworked openers: join-key section ("Tags are the join keys.") and serverless section ("Mixing up how serverless and classic compute are billed..."); all other sections already opened answer-first.*
- [x] T604: Add FAQ section: H2 `color-text` label (e.g., "FAQ") + 3–4 question-form H3s from research.md §Target Search Queries, each answered in 40–80 self-contained words, answer in the first sentence (FR-6.4). **[BWG]** **[CRW]** *Shipped: 4 question H3s after Conclusion.*
- [x] T605: Add ≥ 1 internal rework-space.com link with a natural anchor; record the chosen target in spec §7 (FR-6.8). *Shipped: `color-link` to post-9 (`/blog/2025-12-23-rs-dataplatform-project-concept`) in "The bigger architecture".*
- [ ] T606: Theme verification in rs-theme repo: `description` meta field, `Article`/`FAQPage` JSON-LD, sitemap/RSS inclusion, canonical URL, OG/Twitter card rendering (FR-6.9–11). File theme issues for gaps.
- [x] T607: Post-FAQ compliance re-run: banned-language, em-dash, AI-tell sweeps and Hugo build (repeat T401/T403 scope on changed sections). **[BWG]** **[Review Gate 4]** *Sweeps clean; docker compose Hugo build passed with zero warnings (EN 110 pages, UA 104).*

## Follow-up (non-blocking)
- [x] T501: Ukrainian translation → `content/ua/blog/post-11.md`. *Shipped: full UA body incl. FAQ; title/alt translated per author request (unlike post-10 UA, which keeps the English title), keywords kept English, retained English terms wrapped in quotes, `/ua`-prefixed url and internal link, "Рисунок N." captions; Hugo build clean.*
- [ ] T502: Open plan for Part 2 (DMLZ/DLZ architecture + Terraform modules). *Part 2 must back-link to this post (FR-6.8).*
