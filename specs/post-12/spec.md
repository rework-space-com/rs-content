# Spec — Blog Post 12 (Part 2): Landing Zones as Code — a Terraform Blueprint for Azure Databricks and Pipelines-as-Code

**Deliverable:** `content/en/blog/post-12.md`
**Template reference:** `content/en/blog/post-10.md` (structure grammar), `content/en/blog/post-11.md` (SEO/AEO pattern, FAQ, code blocks)
**Predecessor:** [post-11 spec package](../post-11/spec.md) — this post fulfils post-11's Part 2 promise and task T502
**Governing principles:** [constitution.md](constitution.md)
**Governing skills:** [blog-writing-guide](../../.agents/skills/blog-writing-guide/SKILL.md) (standards), [content-research-writer](../../.agents/skills/content-research-writer/SKILL.md) (process)

## 1. Objective

Publish an engineering deep-dive showing how to deploy the entire environment
from post-11 as code: the Azure Platform Landing Zone that governs the
organization, the DLZ workload layer, and the Databricks pipelines inside it.
The DMLZ is planned research, not an implemented component. Everything
reviewable in a PR, nothing clicked in a portal.

## 2. Audience

Platform Engineers, DevOps/Cloud Engineers, Data Platform Architects, plus the
CTOs and Lead Engineers who approve landing-zone designs.

## 3. Functional Requirements

### FR-1: Front matter (exact post-10 schema)

Fields MUST appear in this exact order, matching `post-10.md` byte-for-byte in
style: string values in single quotes, arrays as `[ 'a', 'b' ]` (spaces inside
brackets), booleans and `date` unquoted.

| # | Field | Value / Rule |
|---|---|---|
| 1 | `title` | Published: 'Landing Zones as Code: A Terraform Blueprint for Azure Databricks and Pipelines-as-Code' |
| 2 | `url` | `'/blog/2026-08-17-terraform-blueprint-azure-databricks-landing-zones'` |
| 3 | `type` | `article` (unquoted) |
| 4 | `omit_header_text` | `false` |
| 5 | `featured_image` | `'/static-blog-images/post-12/landing-zones-as-code-concept.jpg'` |
| 6 | `summary_image` | `'/blog-images/post-12/<base>-short.jpg'` |
| 7 | `sharing_image` | `'/static-blog-images/post-12/<base>-share.jpg'` |
| 8 | `twitter_sharing_image` | `'/static-blog-images/post-12/<base>-twitter-share.jpg'` |
| 9 | `alt` | 'Banner for article <final title>' |
| 10 | `keywords` | [ 'Terraform', 'Azure Databricks', 'IaC', 'data landing zone', 'Databricks Asset Bundles', 'Unity Catalog', 'DevOps' ] (tune at final review) |
| 11 | `date` | `2026-08-17` (unquoted) |
| 12 | `blog_tags` | [ 'Terraform', 'Azure', 'IaC', 'Databricks', 'DevOps' ] |
| 13 | `draft` | `false` (published) |

### FR-2: Required sections

1. **Introduction** — the problem: post-11's pipeline works, but a portal-built
   environment cannot be reviewed, reproduced, or governed; state the
   everything-as-code conclusion up front. Back-link to post-11 in the first
   section (also satisfies FR-6.8 and post-11 FR-6.8/T502).
2. **Definitional section** — "What is a Data Landing Zone?" (DLZ/DMLZ per
   Microsoft's Cloud Adoption Framework); self-contained, quotable 40–60-word
   definition in the first paragraph.
3. **Landing zone architecture as code** — Azure Platform Landing Zone and DLZ
   topology, implemented with the Azure Landing Zones Accelerator
   `platform_landing_zone` template and the PerfectThymeTech
   `terraform-azurerm-data-landing-zone` module: management groups, central
   management and connectivity subscriptions, hub-spoke networking, and
   private endpoints. Architecture diagram required (Picture 1). Must
   situate the zones inside the **Azure organization**: tenant →
   management-group hierarchy → platform and workload subscriptions, with
   Azure Policy assignments attached at the management-group scope so every
   workload subscription inherits the guardrails. State that the DMLZ is
   planned research for organization-wide data governance and catalog
   capabilities.
4. **Scopes and responsibility model** — two scope ladders and two teams:
   - **Databricks account scope vs. workspace scope.** The Databricks
   account (account console, account ID, metastore, account-level users,
   groups, and service principals via identity federation) sits above all
   workspaces; workspace-level objects (clusters, jobs, warehouses) map to
   DLZ layers. State explicitly which Terraform
     provider block (account-level vs. workspace-level `host`) manages which
     objects.
   - **Platform team contract.** Owns: management groups, subscriptions,
   networking, Databricks workspaces, cluster policies, Azure
     Policy assignments. **Inputs:** management-group/subscription IDs, CIDR
     allocations, Entra ID group object IDs, the naming-and-tagging standard,
     the Databricks account ID, and the Azure Policy set. The Platform Landing
     Zone **emits** the hub VNet/NSG/route-table IDs that the DLZ consumes to
     deploy its network-connected resources. **Azure Policy is the restriction
     mechanism for created entities**: allowed regions, allowed VM SKUs for
     cluster nodes, required tags (deny without `cost_center`), deny public
     network access on storage, enforced diagnostic settings. Policies
     constrain what any later `terraform apply` or portal action can create,
     including entities the platform team did not create itself.
   - **Data engineering team contract.** Owns: jobs, Lakeflow pipelines,
     notebooks, bundle configs, SQL. **Inputs (received from the
     platform team as Terraform outputs):** workspace URLs, Unity
     Catalog catalog/schema names and external locations, cluster policy IDs,
     SQL warehouse IDs, service principal IDs, and storage paths. The team
     works inside policy-constrained boundaries and cannot create untagged or
     out-of-region resources.
   - An inputs/ownership table (extractable per FR-6.5) summarizing both
     contracts.
5. **Terraform best practices applied, not listed** — module composition and
   versioning, remote state with per-zone/per-environment isolation, plan/apply
   CI/CD with approval gates, policy-as-code guardrails, and a
   naming-and-tagging standard shown as the mechanism that makes post-11's
   FinOps attribution possible (tags enforced at provision time close the
   "unmatched bucket").
6. **Databricks pipelines as code** — the FOCUS transformation and merge
   pipeline from post-11 deployed via **Databricks Asset Bundles (DABs)**:
   bundle structure, environment targets (dev/prod), jobs and DLT pipeline
   YAML, Unity Catalog objects (catalogs, schemas, grants) via the Terraform
   Databricks provider, CI/CD promotion of bundles.
7. **Trade-offs** — DABs vs. pure Terraform for workspace objects; when the
   upstream modules are enough vs. when to fork. Per constitution §III, cover
   what did not work and known limitations.
8. **Conclusion** — concrete numbers where available, honest limitations, CTA.
9. **FAQ** — per FR-6.4.

Section headings are rewritten as concise H2s per constitution §VI; the list
above defines *content*, not literal headings.

### FR-3: Visual assets

| # | Asset | Location | Status |
|---|---|---|---|
| 1 | Platform Landing Zone and DLZ architecture diagram (Picture 1), real service names, showing the management-group hierarchy, Azure Policy assignment scope, platform management/connectivity subscriptions, and the Databricks account boundary above the workspaces | `assets/blog-images/post-12/dmlz-dlz-terraform-topology.png` | Placeholder has an outdated DMLZ/DLZ label; replace before publication. |
| 2 | DAB CI/CD promotion flow (Picture 2) | `assets/blog-images/post-12/asset-bundle-cicd-flow.png` | Placeholder shipped; final diagram TODO |
| 3 | Featured + sharing images, base name `landing-zones-as-code` | `static/static-blog-images/post-12/` (`-concept`, `-share`, `-twitter-share`) + `assets/blog-images/post-12/` (`-short`) | Placeholders shipped; final artwork TODO |

In-body `img` shortcode images MUST live under `assets/` (the shortcode
resolves through Hugo's asset pipeline and crashes on missing resources);
front matter featured/sharing images live under `static/`.

### FR-4: Links

All external references appear as `color-link` shortcodes at first relevant
mention. Internal links to rework-space.com use plain `/blog/...` paths.

Planned link set (extend in research.md): PerfectThymeTech
data-management-zone and data-landing-zone modules, MS Cloud Adoption
Framework unified data platform, Azure management groups docs, Azure Policy
docs (built-in policies for tags/locations), Databricks account console /
identity federation docs, Databricks Asset Bundles docs, Terraform
Databricks provider registry page, Unity Catalog docs, Terraform remote
state/backends docs, **post-11 internal back-link (mandatory)**.

### FR-5: Hugo structure contract (post-10/post-11 grammar)

Identical to post-11 spec FR-5:

1. H2 headings via `color-text` shortcode, 1–4 words.
2. H3 headings plain Markdown, descriptive.
3. External links via `color-link` on its own line, sentence wrapping around it.
4. Images via `img` shortcode, blank lines around, `Picture N.` alt captions,
   sequential from 1, cross-referenced as `(Picture N)`.
5. `**bold**` for key terms at first mention; `_italics_` for identifiers.
6. Fenced, language-tagged code blocks (```hcl, ```yaml, ```sql).
7. No raw Markdown links, no raw HTML, no `![]()` images.

First body line after front matter is an H2 `color-text` heading.

### FR-6: SEO & AI search (GEO/AEO) optimization

Built in from the first draft (pattern proven on post-11), not retrofitted.
Target queries live in [research.md](research.md) §Target Search Queries.

**Content-level:**

1. **Keyword placement.** Primary cluster (Terraform Azure Databricks, data
   landing zone, Databricks Asset Bundles, IaC best practices) in: `title`,
   `url` slug, `keywords`, ≥ 3 H3s, first 100 words, ≥ 1 image alt.
2. **Definitional section.** "What is a Data Landing Zone?" opens with a
   self-contained, quotable 40–60-word definition.
3. **Answer-first sections.** Each H2/H3 opens with a sentence that directly
   answers the heading's implied question.
4. **FAQ section.** H2 FAQ block with 3–4 question-form H3s (candidates in
   research.md); each answer 40–80 self-contained words, answer in the first
   sentence.
5. **Extractable structures.** Comparison facts (DABs vs. Terraform, module
   responsibilities) ship as Markdown tables or lists.
6. **Entity clarity.** Every acronym expanded at first use (DLZ, DMLZ, DAB,
   IaC, UC, CAF); product names written in full.
7. **Citable claims.** Every statistic carries a number and a `color-link`
   source.
8. **Internal links.** Mandatory back-link to post-11; add others where
   natural anchors exist.

**Site/theme-level:** inherit post-11 open items FR-6.9–11 (meta description,
`Article`/`FAQPage` JSON-LD, sitemap/canonical/OG verification) — tracked in
the post-11 package (T606), do not duplicate work here.

## 4. Acceptance Criteria

- [x] Front matter fields in exact FR-1 order with post-10 quoting style; Hugo builds without error.
- [x] Body uses only FR-5 constructs; structural diff against post-10/post-11 shows no grammar differences.
- [x] All H2s are 1–4 words inside `color-text`; H3s plain Markdown and informative.
- [x] Picture numbering sequential from 1; every image cross-referenced in prose.
- [x] All nine FR-2 content sections present, in order. *Section 4 (scopes
      and responsibility model) shipped via T211 on 2026-08-18.*
- [x] Opening states the problem or conclusion within the first 3 sentences.
- [x] Back-link to post-11 present in the introduction (closes post-11 T502 requirement).
- [x] Zero banned-language occurrences (constitution §II); zero em dashes.
- [x] No AI-writing patterns from the blog-writing-guide skill.
- [x] Every quantitative claim has a number and a linked source.
- [x] All HCL/YAML samples reference pinned, real module/provider versions. *Platform Landing Zone template pinned at v17.4.0; data landing zone module v0.8.0; Databricks provider 1.127.0.*
- [x] Trade-offs section covers alternatives not chosen and why (constitution §V).
- [x] FR-2.4 inputs/ownership table present: platform vs. data
      engineering contracts with explicit team inputs and Azure Policy
      restrictions (shipped via T211).
- [x] FR-6.1–6.8 satisfied (keyword placement, definition, answer-first, FAQ, tables, acronyms, citations, internal links).
- [x] Word count 1,500–2,500.
- [x] A Ukrainian counterpart `content/ua/blog/post-12.md` planned as follow-up (not blocking English publication). *Shipped: [content/ua/blog/post-12.md](../../content/ua/blog/post-12.md).*

## 5. Out of Scope

- Multi-cloud landing zones; AWS/GCP equivalents.
- Deep FinOps analysis (covered by post-11).
- Rework-Space fork specifics of the upstream Terraform modules beyond the
  "when to fork" trade-off discussion.

## 6. Deviations

1. **Short H2 headings.** Same as post-11 Deviation 1: template `color-text`
   H2s stay short; SEO keywords move to H3s, title, `keywords`, and body.

## 7. Open Questions

- [x] Final publication date and URL slug. *Published: 2026-08-17, `/blog/2026-08-17-terraform-blueprint-azure-databricks-landing-zones`.*
- [x] Final title (iterate per BWG: specific claim or payoff, not a vague announcement). *Published with the working title: 'Landing Zones as Code: A Terraform Blueprint for Azure Databricks and Pipelines-as-Code'.*
- [ ] Hero/sharing artwork subject and base filename. *Base name fixed: `landing-zones-as-code`; placeholders shipped, real artwork TODO.*
- [x] Picture 2 subject: repo/bundle layout vs. CI/CD promotion flow. → CI/CD promotion flow (`asset-bundle-cicd-flow.png`).
- [x] Do we have real deployment numbers to cite? → No; claims scoped so no uncited statistics appear in the draft.
- [x] Which module versions to pin. → Platform Landing Zone template v17.4.0, DLZ v0.8.0, Databricks provider 1.127.0 (research.md §Version Pins).
