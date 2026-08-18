# Spec — Blog Post 11: Beyond Separated Billing: Merging Azure Infrastructure and Databricks DBUs with FOCUS

**Deliverable:** `content/en/blog/post-11.md`
**Template reference:** `content/en/blog/post-10.md`
**Source plan:** [itfm-databricks-blog-plan-v4.md](itfm-databricks-blog-plan-v4.md) (moved into this spec package)
**Governing principles:** [constitution.md](constitution.md)
**Governing skills:** [blog-writing-guide](../../.agents/skills/blog-writing-guide/SKILL.md) (standards), [content-research-writer](../../.agents/skills/content-research-writer/SKILL.md) (process)

## 1. Objective

Publish a technical, strategic blog post describing a real-world FinOps MVP that
uses Azure Databricks to merge Azure infrastructure billing and Databricks DBU
usage logs into a single FOCUS-format cost lakehouse.

## 2. Audience

CIOs, CTOs, IT Finance Directors, FinOps Lead Engineers, plus the Data
Engineers and Data Analysts who build and consume the pipeline.

## 3. Functional Requirements

### FR-1: Front matter (exact post-10 schema)

Fields MUST appear in this exact order, matching `post-10.md` byte-for-byte in
style: string values in single quotes, arrays as `[ 'a', 'b' ]` (spaces inside
brackets), booleans and `date` unquoted.

| # | Field | Value / Rule |
|---|---|---|
| 1 | `title` | 'Beyond Separated Billing: Merging Azure Infrastructure and Databricks DBUs with FOCUS' |
| 2 | `url` | `'/blog/2026-08-12-merging-azure-databricks-costs-with-focus'` |
| 3 | `type` | `article` (unquoted) |
| 4 | `omit_header_text` | `false` |
| 5 | `featured_image` | `'/static-blog-images/post-11/cloud-cost-analytics-concept.jpg'` |
| 6 | `summary_image` | `'/blog-images/post-11/unified-cost-lakehouse-short.jpg'` |
| 7 | `sharing_image` | `'/static-blog-images/post-11/unified-cost-lakehouse-share.jpg'` |
| 8 | `twitter_sharing_image` | `'/static-blog-images/post-11/unified-cost-lakehouse-twitter-share.jpg'` |
| 9 | `alt` | 'Banner for article Beyond Separated Billing: Merging Azure Infrastructure and Databricks DBUs with FOCUS' |
| 10 | `keywords` | [ 'FinOps', 'FOCUS', 'Azure Databricks', 'ITFM', 'cost management', 'lakehouse', 'DBU' ] |
| 11 | `date` | 2026-08-12 (unquoted) |
| 12 | `blog_tags` | [ 'FinOps', 'Azure Databricks', 'DevOps' ] |
| 13 | `draft` | `false` (published 2026-08-12) |

Image filenames are kebab-case and descriptive; `summary_image`, `sharing_image`,
and `twitter_sharing_image` share the base name `unified-cost-lakehouse` with the
`-short` / `-share` / `-twitter-share` suffixes, exactly as in post-10.

### FR-2: Required sections (mapped from plan v4)

1. **Introduction** — the fragmented-billing problem, ITFM/TBM framing ("data
   janitors" spending the bulk of their time reconciling, cited to the
   TechDogs definition), the MVP solution, and a definitional
   "What is FOCUS?" passage (SEO requirement).
2. **Ingestion & raw billing formats** — Azure native FOCUS exports to ADLS
   Gen2 vs. Databricks `system.billing.usage` / `system.billing.list_prices`
   system tables and the custom FOCUS transform layer.
3. **Data Engineer's perspective** — Auto Loader Bronze ingestion; join-key
   reconciliation via ClusterId/WorkspaceId tags; timezone/grain alignment;
   serverless vs. classic compute logic; `MERGE INTO` for retroactive billing
   adjustments.
4. **Data Analyst's perspective** — semantic gap (resource IDs → cost centers),
   enrichment joins with HR/ITAM/CMDB, TCO dashboards, AI/BI Genie natural
   language queries, plus a subsection on the native **Databricks Governance
   Hub** cost surface (Beta, list-price DBU-only view) as a complement to the
   custom pipeline.
5. **Architecture deferral tease** — DMLZ/DLZ concepts, explicit deferral to a
   Part 2 with Terraform modules.
6. **ROI & conclusion** — grounded numbers (70% idle All-Purpose cluster
   reduction, $2.1M+ annual savings) and a call to action.

Section headings must be rewritten as concise H2s per constitution §VI (the
list above defines *content*, not literal headings). Informative, keyword-rich
wording moves to H3 subheadings and body text (see §6 Deviations).

### FR-5: Hugo structure contract (post-10 grammar)

The body of `post-11.md` MUST use exactly these constructs, copied verbatim
from `post-10.md`:

1. **H2 headings** — shortcode wrapper, short text (1–4 words):
   ```
   ## {{< color-text text="The problem" >}}
   ```
2. **H3 headings** — plain Markdown, may be longer/descriptive:
   ```
   ### Terraform Configuration
   ```
3. **External links** — `color-link` shortcode on its own line, the sentence
   wrapping around it with a leading space on the continuation line:
   ```
   ...supported in the official community-driven
   {{< color-link link_title="Terraform Provider v5.7.0" path="https://..." target="_blank" >}}
    . It allows you to version your configuration...
   ```
4. **Images** — `img` shortcode on its own line with blank lines before and
   after; alt text is `Picture N. <Sentence-case description>.` with strictly
   sequential numbering starting at 1:
   ```
   {{< img src="/blog-images/post-11/<name>.png" alt="Picture 1. ..." >}}
   ```
5. **Cross-references** — body text refers to images as `(Picture N)` or
   `displayed in Picture N`.
6. **Emphasis** — `**bold**` for key terms/concepts at first introduction;
   `_italics_` for identifiers, group names, and feature values
   (e.g., _test-users_, _impersonate-members_).
7. **Fenced code blocks** — language-tagged (e.g., ```sql) for code samples;
   the published post ships one SQL block (`MERGE INTO` upsert).
8. **No raw Markdown links, no raw `<img>`/`<a>` HTML, no `![]()` images.**

The first body line after the front matter is an H2 `color-text` heading, as
in post-10.

### FR-3: Visual assets

| # | Asset | Location | Status |
|---|---|---|---|
| 1 | Comparative pipeline flow diagram (Picture 1, placed in "What is FOCUS?" section) | `assets/blog-images/post-11/traditional-vs-focus-based.png` (earlier `focus-unified-pipeline-schematic.jpg` iteration kept in `assets/`, origin PNG in `static/static-blog-images/post-11/`; both unused by the published post) | Done |
| 2 | Transformation matrix (Databricks system table fields → FOCUS columns) | In-body Markdown table in "Two billing streams" | Done |
| 3 | Unified TCO dashboard screenshot (Picture 2, in "TCO dashboards that don't double-count") | `assets/blog-images/post-11/tco-dashboard-unified-costs.png` | Done |
| 4 | Governance Hub overview screenshot (Picture 3) | `assets/blog-images/post-11/governance-hub-overview.png` | Done |
| 5 | Governance Hub Cost page screenshot (Picture 4) | `assets/blog-images/post-11/governance-hub-cost-page.png` | Done |
| 6 | Featured + sharing images | `static/static-blog-images/post-11/cloud-cost-analytics-concept.jpg` (+ `-origin`), `unified-cost-lakehouse-{share,twitter-share}.jpg`; summary in `assets/blog-images/post-11/unified-cost-lakehouse-short.jpg` | Done |

Every image uses the `img` shortcode with a numbered `Picture N.` alt caption.
In-body `img` shortcode images MUST live under `assets/` (the shortcode resolves
through Hugo's asset pipeline and crashes on missing resources); front matter
featured/sharing images live under `static/`.

### FR-4: Links

All external references from Section 4 of the plan must appear as
`color-link` shortcodes at first relevant mention. Internal links to
rework-space.com use plain paths.

Published link set (15 `color-link` shortcodes): Databricks DBU pricing,
Apptio ITFM, TechDogs "data janitor" definition, FinOps Foundation FOCUS
portal, Azure scheduled exports tutorial, Azure FOCUS dataset schema,
Databricks system tables docs, system-tables-to-FOCUS-1.3 query,
cloud-infra-costs field solution, "Getting the Full Picture" blog, Databricks
Governance Hub docs, MS Cloud Adoption Framework, PerfectThymeTech
data-management-zone and data-landing-zone Terraform modules, CloudNuro case
study.

### FR-6: SEO & AI search (GEO/AEO) optimization

Optimize the post for classic search engines and for AI answer engines
(ChatGPT, Perplexity, Google AI Overviews, Copilot). Target queries live in
[research.md](research.md) §Target Search Queries.

**Content-level requirements (in `post-11.md`):**

1. **Keyword placement.** The primary keyword cluster (FOCUS, Azure
   Databricks, cost management/lakehouse, DBU) appears in: `title`, `url`
   slug, `keywords` array, at least three H3 headings, the first 100 words of
   body text, and at least one image `alt`.
2. **Definitional section.** A "What is X?" section for the head term
   (shipped: "What is FOCUS?") whose first paragraph is a self-contained,
   quotable definition of 40–60 words that an answer engine can lift verbatim.
3. **Answer-first sections.** Each H2/H3 section opens with a sentence that
   directly answers the implied question of its heading; supporting detail
   follows. No section may open with throat-clearing or a back-reference that
   is meaningless out of context.
4. **FAQ section.** An H2 FAQ block near the end with 3–4 H3s phrased as
   natural-language questions targeting long-tail queries (e.g., "Does Azure
   Databricks export FOCUS data natively?", "How do you join Azure billing
   data with Databricks DBU usage?"). Each answer is 40–80 words,
   self-contained, and answers in the first sentence. Targets featured
   snippets, People Also Ask, and AI answer extraction.
5. **Extractable structures.** Facts that answer comparison or "how" queries
   ship as Markdown tables or lists (shipped: transformation matrix,
   serverless-vs-classic branching rules), because answer engines extract
   structured blocks preferentially.
6. **Entity clarity.** Every acronym expanded at first use (FOCUS, ITFM, DBU,
   TCO, ADLS, DLZ/DMLZ); product names written in full ("Azure Databricks",
   "AI/BI Genie") so entity linking is unambiguous.
7. **Citable claims.** Every statistic carries a number and a `color-link`
   source; AI engines rank citation-dense pages as higher-authority sources.
8. **Internal links.** At least one plain-path internal link to a related
   rework-space.com page or post where a natural anchor exists (builds topic
   authority; the Part 2 post must back-link here when published).

**Site/theme-level requirements (verify against rs-theme, out of content
repo):**

9. **Meta description.** If the theme renders a `description` front matter
   field into `<meta name="description">`, add one: ≤ 155 characters,
   contains the primary keyword, states the payoff. If unsupported, file a
   theme issue.
10. **Structured data.** Verify the theme emits `Article` JSON-LD (headline,
    datePublished, image, author); if the FAQ section ships, request
    `FAQPage` JSON-LD support from the theme.
11. **Discoverability plumbing.** Confirm the post appears in the sitemap and
    RSS feed, canonical URL matches `url`, and Open Graph/Twitter cards
    resolve to the FR-1 sharing images (robots.txt already enabled via
    `enableRobotsTXT`).

## 4. Acceptance Criteria

- [x] Front matter fields appear in the exact FR-1 order with post-10 quoting style; Hugo builds without error.
- [x] Body uses only the FR-5 constructs; a side-by-side structural diff against post-10.md shows no grammar differences (headings, links, images, emphasis).
- [x] All H2 headings are 1–4 words inside `color-text`; H3s are plain Markdown.
- [x] Picture numbering is sequential from 1 with no gaps, and every image is cross-referenced in body text.
- [x] All six content sections present, in order (plus the Governance Hub
      subsection added at final review, see §6 Deviation 3).
- [x] Opening states the problem within the first 3 sentences.
- [x] Zero banned-language occurrences (constitution §II).
- [x] Zero em dashes in the rendered body.
- [x] No AI-writing patterns from the blog-writing-guide skill (staccato fragments, three-beat reveals, parallel-structure ad copy, etc.).
- [x] Every quantitative claim has a number and a linked source (citation workflow per content-research-writer skill).
- [x] All shortcodes match the post-10 syntax exactly.
- [x] All FR-3 in-body assets (Pictures 1–4 and the transformation matrix)
      are present and referenced in text.
- [x] Word count 1,500–2,500 (comparable to post-10 depth).
- [x] FR-6.1: primary keyword cluster present in title, slug, `keywords`,
      ≥ 3 H3s, first 100 words, and ≥ 1 image alt.
- [x] FR-6.2: "What is FOCUS?" opens with a self-contained 40–60-word
      definition.
- [x] FR-6.5–6.7: extractable tables/lists, expanded acronyms, and
      number-plus-source citations shipped.
- [x] FR-6.3: answer-first audit of every H2/H3 opening sentence.
- [x] FR-6.4: FAQ section with 3–4 question-form H3s and self-contained
      answers.
- [x] FR-6.8: ≥ 1 internal rework-space.com link with a natural anchor.
- [ ] FR-6.9–11: theme verification — meta description, Article/FAQPage
      JSON-LD, sitemap/RSS/canonical/OG cards.
- [ ] A Ukrainian counterpart `content/ua/blog/post-11.md` is planned as a
      follow-up task (not blocking English publication).

## 5. Out of Scope

- Deep technical design of DMLZ/DLZ landing zones and Terraform networking
  (explicitly deferred to Part 2).
- Building the actual Databricks pipeline or publishing runnable notebooks.

## 6. Deviations

1. **Short H2 headings.** The blog-writing-guide skill calls for long,
   information-carrying, keyword-rich H2s. The post-10 Hugo template renders
   H2s through the `color-text` shortcode with short labels (1–4 words).
   Template structure wins: H2s stay short; SEO keywords and informative
   wording move into H3 subheadings, the title, `keywords`, and body prose.
2. **Upstream Terraform module links.** The plan referenced the
   `rework-space-com` forks of the data-management-zone and data-landing-zone
   modules; the published post links the `PerfectThymeTech` upstream
   repositories instead (author decision at final review).
3. **Governance Hub subsection.** Not in plan v4. Added at final review as an
   H3 under "From costs to accountability": native Databricks cost visibility
   (Beta, list-price, DBU-only) positioned as a complement to the unified
   FOCUS pipeline, with two screenshots (Pictures 3–4) and one extra
   `color-link` to the Governance Hub docs.
4. **Picture 1 replaced.** The original `focus-unified-pipeline-schematic.jpg`
   diagram was superseded by `traditional-vs-focus-based.png` (traditional
   Medallion pipeline vs. FOCUS-based pipeline comparison); the old files
   remain on disk but are not referenced by the post.
5. **"Data janitors" claim softened.** The plan's "80% of time" figure was
   replaced with "the bulk of their time", cited to the TechDogs dictionary
   entry instead of an uncited statistic.

## 7. Open Questions

- [x] Final publication date → 2026-08-12, confirmed in `date`/`url`, `draft: false`.
- [x] Hero/sharing images → shipped as `cloud-cost-analytics-concept.jpg` and `unified-cost-lakehouse-*` (share/summary artwork reuses post-10 visuals under new names; replace when final artwork is ready).
- [x] Transformation matrix ships as an in-body Markdown table.
- [ ] Does rs-theme render a `description` front matter field into `<meta name="description">`? (FR-6.9; post-10 schema has no such field.)
- [ ] Does rs-theme emit `Article` JSON-LD, and can it support `FAQPage` JSON-LD? (FR-6.10)
- [x] Which existing rework-space.com post is the best internal-link anchor until Part 2 publishes? → post-9, `/blog/2025-12-23-rs-dataplatform-project-concept`, linked from "The bigger architecture" (identity as a tenant-wide DMLZ service). (FR-6.8)
