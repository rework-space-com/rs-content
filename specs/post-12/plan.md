# Plan — Writing post-12.md

**Spec:** [spec.md](spec.md) | **Constitution:** [constitution.md](constitution.md)

## Skills Used

| Pass | Skill | How it is applied |
|---|---|---|
| Skeleton | [content-research-writer](../../.agents/skills/content-research-writer/SKILL.md) | Collaborative outlining workflow: heading tree with key points and `[Research needed: ...]` markers per section |
| Draft | both | content-research-writer for hook iteration and section-by-section feedback; blog-writing-guide for voice, openings, and structure rules |
| Asset | — | No skill; follows post-10/post-11 shortcode conventions |
| Compliance | [blog-writing-guide](../../.agents/skills/blog-writing-guide/SKILL.md) | Banned-language, AI-tell, heading, and SEO audits straight from the skill checklists |

Read the SKILL.md files before starting the corresponding pass.

## Approach

Write the post in ordered passes. Unlike post-11, the SEO/AEO requirements
(spec FR-6) are built into the skeleton and draft passes, not retrofitted.

1. **Skeleton pass** — front matter + H2/H3 heading tree with one-line intent
   notes and `[Research needed: ...]` markers. FAQ block and answer-first
   openers planned from the start. Validate against FR-2 and FR-6 before prose.
2. **Draft pass** — full prose per section, engineering sections first
   (Sections 3–5 carry the technical weight: landing zones, Terraform
   practices, DABs), then trade-offs, then intro/conclusion/FAQ. Verify every
   HCL/YAML sample against real module and provider versions; pin versions.
   Section review after each section per content-research-writer.
3. **Asset pass** — architecture diagram (Picture 1), second visual
   (Picture 2), hero/sharing images; wire `img` shortcodes.
4. **Compliance pass** — banned-language sweep, em-dash sweep, AI-tell sweep,
   link/shortcode audit, FR-6 keyword/answer-first/FAQ checks,
   acceptance-criteria checklist, Hugo build.

## Heading Tree (working draft)

H2s are short `color-text` labels per spec FR-5; descriptive, keyword-rich
wording lives in the H3s.

- H2: "The problem" *(portal-built environments can't be reviewed or reproduced; back-link to post-11)*
- H2: "What is a Data Landing Zone?" *(definitional/SEO: DLZ within the Azure Platform Landing Zone model)*
- H2: "Landing zones in Terraform"
  - H3: The platform and data landing zone topology we deploy
  - H3: Azure organization: a platform landing zone governs workloads
  - H3: Networking: hub-spoke and private endpoints as module inputs
  - H3: Unity Catalog spans workspaces at account scope
- H2: "Who owns what" *(spec FR-2.4: scopes + team contracts)*
  - H3: Databricks account scope vs. workspace scope in Terraform providers
  - H3: The Platform team's contract: inputs and Azure Policy guardrails
  - H3: The data engineering team's contract: what they receive and what they own
- H2: "Terraform discipline" *(best practices applied, not listed)*
  - H3: Remote state isolation per zone and environment
  - H3: Plan/apply CI/CD with approval gates
  - H3: Tags enforced at provision time: closing Part 1's unmatched bucket
- H2: "Pipelines as code"
  - H3: Databricks Asset Bundles: structure and environment targets
  - H3: The FOCUS transform job as a bundle-managed workflow
  - H3: Unity Catalog objects via the Terraform Databricks provider
- H2: "Trade-offs"
  - H3: DABs vs. Terraform: who owns the workspace objects
  - H3: When the upstream modules are enough, and when to fork
- H2: "Conclusion" *(numbers where citable, limitations, CTA)*
- H2: "FAQ" *(spec FR-6.4: 3–4 question-form H3s from research.md §Target Search Queries)*

Headings are drafts; H3 wording tuned during the compliance pass for keyword
coverage (Terraform, Azure Databricks, data landing zone, Databricks Asset
Bundles, IaC).

## Content Sourcing

- Architecture claims trace to the Microsoft Cloud Adoption Framework and the
  PerfectThymeTech module READMEs/source ([research.md](research.md)).
- DAB claims trace to Databricks Asset Bundles documentation; provider claims
  to the Terraform Registry Databricks provider docs.
- Any savings/ROI numbers must come from citable sources or real project data
  (spec §7 open question); no uncited statistics.
- Part 1 facts (tag join keys, FOCUS transform, unmatched bucket) reference
  post-11 via the internal back-link, not re-derivation.

## File/Asset Layout

```
content/en/blog/post-12.md              # deliverable
assets/blog-images/post-12/             # in-body diagrams
static/static-blog-images/post-12/      # hero + sharing images
specs/post-12/                          # this spec package
```

## Risks & Mitigations

| Risk | Mitigation |
|---|---|
| Post reads as a Terraform tutorial instead of an architecture deep-dive | Anchor every practice to the post-11 story (governance, cost attribution); trade-offs section mandatory |
| Responsibility section becomes an org-chart essay | Ship it as contracts: explicit inputs tables and policy lists, no RACI prose |
| Module APIs drift between draft and publish | Pin versions in prose and re-verify samples at Review Gate 2 |
| DABs vs. Terraform section turns into vendor-doc paraphrase | State an opinionated split (who owns what) with rationale from real use |
| Scope creep into multi-cloud or fork internals | Spec §5 out-of-scope list; cut at skeleton gate |
| Heading shortcodes typed incorrectly | Copy shortcode syntax verbatim from post-11.md |
| Front matter drifts from post-10 (order, quoting) | Structural diff against post-10/post-11 before Review Gate 3 |
| SEO retrofit pain (post-11 lesson) | FR-6 items included in skeleton checklist and Review Gate 1 |

## Review Gates

1. Skeleton approved against FR-2 and FR-6 → proceed to draft.
2. Draft passes acceptance criteria self-check; code samples verified against pinned versions → proceed to assets.
3. Final human review → flip `draft: false`.
