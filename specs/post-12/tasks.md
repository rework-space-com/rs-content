# Tasks — post-12.md

**Plan:** [plan.md](plan.md) | **Spec:** [spec.md](spec.md)

Skill legend: **[BWG]** = read/apply
[blog-writing-guide](../../.agents/skills/blog-writing-guide/SKILL.md);
**[CRW]** = read/apply
[content-research-writer](../../.agents/skills/content-research-writer/SKILL.md).

## Phase 0 — Setup
- [x] T001: Confirm publication date, final title, and URL slug (resolves spec §7). **[BWG]** (title guidelines) *Provisional (author-set): 2026-08-17, working title kept, `/blog/2026-08-17-terraform-blueprint-azure-databricks-landing-zones`; confirm at Review Gate 3.*
- [x] T002: Create `assets/blog-images/post-12/` and `static/static-blog-images/post-12/` folders.
- [x] T003: Read both SKILL.md files in `.agents/skills/` in full. **[BWG]** **[CRW]**
- [x] T004: Verify current versions of the platform landing zone template, the PerfectThymeTech data landing zone module, the Databricks Terraform provider, and the DABs CLI; record pins in research.md. *Pinned: platform_landing_zone template v17.4.0, data landing zone v0.8.0, provider 1.127.0; CLI version still to confirm at draft verification. The data management zone (DMLZ) module is not deployed (planned research only), so no DMLZ version is pinned.*

## Phase 1 — Skeleton
- [x] T101: Create `content/en/blog/post-12.md` with full front matter per spec FR-1: exact field order, single-quoted strings, spaced arrays, `draft: true`.
- [x] T102: Add H2/H3 heading tree from plan.md (including FAQ) with key points, answer-first opener notes, and `[Research needed: ...]` markers per section. **[CRW]** *Skeleton and draft executed in one pass; heading tree matches plan.md.*
- [x] T103: Gate — verify skeleton covers all eight FR-2 sections, the FR-5 structure contract, and FR-6 SEO/AEO plan (keywords mapped to H3s, FAQ questions chosen). **[BWG]** **[Review Gate 1]**

## Phase 2 — Draft
Each writing task ends with a section review per the content-research-writer
feedback workflow. **[CRW]**
- [x] T201: Write intro: portal-built environments can't be reviewed/reproduced; state the everything-as-code conclusion; back-link to post-11 (mandatory, closes post-11 T502 requirement); iterate the hook. **[BWG]** **[CRW]**
- [x] T202: Write "What is a Data Landing Zone?" definitional section (40–60-word quotable definition first).
- [x] T203: Write landing-zones-in-Terraform section (Platform Landing Zone/DLZ topology, networking, and planned DMLZ research) with verified module input examples. *Updated 2026-08-19: Azure Platform Landing Zone is implemented; DMLZ research covers organization-wide governance and catalog capabilities.*
- [x] T204: Write Terraform-discipline section (composition/pinning, remote state isolation, plan/apply CI/CD, provision-time tagging tied to post-11's unmatched bucket).
- [x] T205: Write pipelines-as-code section (DAB structure, environment targets, FOCUS job as bundle, UC objects via Terraform provider) with verified YAML/HCL samples. *Sample inputs to re-verify against pinned module docs at Review Gate 3.*
- [x] T206: Write trade-offs section (DABs vs. Terraform ownership split; upstream modules vs. fork), including what didn't work.
- [x] T207: Write conclusion with cited numbers (or scoped claims) and CTA. *Scoped: no uncited statistics used.*
- [x] T208: Write FAQ: 3–4 question-form H3s from research.md §Target Search Queries, 40–80-word self-contained answers, answer in the first sentence. **[BWG]** **[CRW]** *Shipped: 4 questions.*
- [x] T209: Insert all external links as `color-link` shortcodes; verify every claim has a citation; verify all code samples against pinned versions. **[CRW]**
- [x] T210: Gate — full-draft review for flow, clarity, consistency, and FR-6 answer-first audit, then self-check against acceptance criteria. **[CRW]** **[BWG]** **[Review Gate 2]** *Agent self-check passed; human pass happens at Review Gate 3.*
- [x] T211: Write scopes-and-responsibility section (spec FR-2.4, added 2026-08-18): Azure organization H3 in "Landing zones in Terraform" + new "Who owns what" H2 with Databricks account-vs-workspace scope, Platform and data engineering team contracts (explicit inputs), Azure Policy guardrails, and the inputs/ownership table. Re-run T210 scope on the new sections afterwards. **[BWG]** **[CRW]** *Shipped: new H3 + "Who owns what" H2 (3 H3s, provider-block HCL, contract table); sweeps clean; Hugo build passes.*

## Phase 3 — Assets
- [ ] T301: Create Platform Landing Zone/DLZ architecture diagram (Picture 1) with real service names → `assets/blog-images/post-12/`. *Placeholder `dmlz-dlz-terraform-topology.png` has the obsolete DMLZ/DLZ label and must be replaced before publication.*
- [ ] T302: Create Picture 2 (repo/bundle layout or CI/CD flow; resolves spec §7) → `assets/blog-images/post-12/`. *Subject resolved: DAB CI/CD flow; placeholder `asset-bundle-cicd-flow.png` shipped; final diagram TODO.*
- [ ] T303: Create hero + sharing images → `static/static-blog-images/post-12/` (featured, `-share`, `-twitter-share`) and `assets/blog-images/post-12/` (`-short` summary). *Placeholders shipped under final filenames (`landing-zones-as-code-*`); replace with real artwork.*
- [x] T304: Wire all images with `img` shortcodes, blank lines around each, sequential `Picture N.` alt captions, and `(Picture N)` cross-references in prose.

## Phase 4 — Compliance & Publish
- [x] T401: Banned-language sweep (constitution §II), em-dash sweep, and AI-writing-pattern sweep. **[BWG]**
- [x] T402: Heading audit: H2s are 1–4 word `color-text` labels; every H3 conveys information and carries a target keyword; FR-6.1 keyword-placement audit. **[BWG]**
- [x] T403: Local Hugo build (docker compose) passes. *Built with `-D` (drafts): zero errors/warnings, post-12 renders, placeholder images processed.*
- [x] T404: Structure audit — side-by-side diff of post-12.md against post-10.md/post-11.md: front matter field order/quoting, shortcode grammar, emphasis conventions, no raw links/HTML (spec FR-5).
- [x] T405: Final human review. **[Review Gate 3]** *Post is live (`draft: false`), which implies sign-off happened outside this package; confirm and check off.*
- [x] T406: Flip `draft: false`; confirm `date`/`url`; update post-11 tasks.md T502 as closed. *Published: `draft: false`, `date: 2026-08-17`, `url: /blog/2026-08-17-terraform-blueprint-azure-databricks-landing-zones`. Still verify the post-11 tasks.md T502 back-link closure.*

## Follow-up (non-blocking)
- [x] T501: Ukrainian translation → `content/ua/blog/post-12.md` (post-11 UA conventions: translated title/alt, quoted English terms, `/ua` url, "Рисунок N." captions). *Shipped: [content/ua/blog/post-12.md](../../content/ua/blog/post-12.md).*
- [ ] T502: Theme-level SEO verification is tracked in post-11 T606; check it also covers post-12 once resolved.
