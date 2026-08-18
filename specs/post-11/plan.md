# Plan — Writing post-11.md

**Spec:** [spec.md](spec.md) | **Constitution:** [constitution.md](constitution.md)

## Skills Used

| Pass | Skill | How it is applied |
|---|---|---|
| Skeleton | [content-research-writer](../../.agents/skills/content-research-writer/SKILL.md) | Collaborative outlining workflow: heading tree with key points and `[Research needed: ...]` markers per section |
| Draft | both | content-research-writer for hook iteration and section-by-section feedback; blog-writing-guide for voice, openings, and structure rules |
| Asset | — | No skill; follows post-10 shortcode conventions |
| Compliance | [blog-writing-guide](../../.agents/skills/blog-writing-guide/SKILL.md) | Banned-language, AI-tell, heading, and SEO audits straight from the skill checklists |

Read the SKILL.md files before starting the corresponding pass.

## Approach

Write the post in ordered passes rather than section-by-section polish:

1. **Skeleton pass** — front matter + H2/H3 heading tree with one-line intent
   notes and `[Research needed: ...]` markers per section (per
   content-research-writer outlining format). Validate against FR-2 before
   writing prose.
2. **Draft pass** — full prose per section, engineering sections first
   (Sections 3–4 carry the technical weight), then intro/conclusion. After
   each section, run a content-research-writer style section review before
   moving on. Iterate the hook separately per that skill's workflow.
3. **Asset pass** — produce/place diagrams and the transformation matrix,
   wire up `img` shortcodes.
4. **Compliance pass** — banned-language sweep, em-dash sweep, AI-tell sweep,
   link/shortcode audit, acceptance-criteria checklist, all per
   blog-writing-guide.
5. **SEO/AEO pass (post-publication)** — spec FR-6: answer-first audit, FAQ
   section, internal link, and rs-theme verification (meta description,
   JSON-LD, sitemap/OG). Tasks T601–T607.

## Heading Tree (working draft)

H2s are short `color-text` labels per spec FR-5; descriptive, keyword-rich
wording lives in the H3s.

- H2: "The problem" *(intro: fragmented billing + ITFM framing)*
- H2: "What is FOCUS?" *(definitional/SEO)*
- H2: "Two billing streams"
  - H3: Azure infrastructure costs: native FOCUS exports to ADLS Gen2
  - H3: Databricks DBUs: system tables that don't speak FOCUS
- H2: "Building the pipeline" *(Data Engineer's perspective)*
  - H3: Streaming ingestion with Auto Loader
  - H3: The join-key problem: finding ClusterId in Azure tags
  - H3: Timezone and grain misalignment
  - H3: Serverless vs. classic compute: avoiding double-counting
  - H3: Retroactive billing adjustments and MERGE INTO
- H2: "From costs to accountability" *(Data Analyst's perspective)*
  - H3: Translating cloud-speak into cost centers
  - H3: TCO dashboards that don't double-count
  - H3: Natural-language cost queries with AI/BI Genie
  - H3: Native cost visibility within Databricks Governance Hub *(added at final review; see spec §6 Deviation 3)*
- H2: "The bigger architecture" *(DMLZ/DLZ tease + Part 2 promise)*
- H2: "Conclusion" *(ROI numbers + CTA)*
- H2: "FAQ" *(shipped, spec FR-6.4: 4 question-form H3s from research.md §Target Search Queries)*

Headings are drafts; H3 wording tuned during the compliance pass for
keyword coverage (FinOps, FOCUS, Azure Databricks, DBU, ITFM).

## Content Sourcing

- All factual claims trace to the link references in
  [itfm-databricks-blog-plan-v4.md](itfm-databricks-blog-plan-v4.md) §4
  (plan file now lives in this spec package).
- ROI numbers (70% idle cluster reduction, $2.1M savings) cite the CloudNuro
  case study and Databricks field solution links.
- The transformation matrix derives from the databricks-solutions
  `cloud-infra-costs` FOCUS 1.3 SQL query repo.

## File/Asset Layout

```
content/en/blog/post-11.md              # deliverable
assets/blog-images/post-11/             # in-body diagrams
static/static-blog-images/post-11/      # hero + sharing images
specs/post-11/                          # this spec package
```

## Risks & Mitigations

| Risk | Mitigation |
|---|---|
| Plan's bracketed source numbers ([5], [91]...) leak into prose | Strip all bracket citations; replace with color-link shortcodes |
| Section 5 tease reads as filler | Keep to ~2 short paragraphs, end with concrete Part 2 promise |
| Dual-perspective structure (DE/DA) fragments the narrative | Bridge with one transitional paragraph: pipeline output = analyst input |
| Heading shortcodes typed incorrectly | Copy shortcode syntax verbatim from post-10.md |
| Front matter drifts from post-10 (order, quoting) | T404a structural diff against post-10.md before Review Gate 3 |
| BWG skill pushes long H2s that break the color-text design | Deviation 1 recorded in spec.md §6: H2s short, keywords in H3s |

## Review Gates

1. Skeleton approved against FR-2 → proceed to draft.
2. Draft passes acceptance criteria self-check → proceed to assets.
3. Final human review → flip `draft: false`.
4. SEO/AEO pass (FR-6) compliance re-run after the FAQ lands → close T607.
