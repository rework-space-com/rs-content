# Research Notes — post-12

Reference material backing the spec and plan. Research, citation formatting,
and closing the open items below follow the
[content-research-writer skill](../../.agents/skills/content-research-writer/SKILL.md)
(research assistance + citation management workflows).

## Key Sources by Section

### Landing zone architecture (definitional + Terraform sections)
- MS Cloud Adoption Framework, unified data platform / landing zones: https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/data/architecture-azure-landing-zones-unify-data-platform
- MS Cloud Adoption Framework, Azure landing zone architecture: https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/?tabs=conceptual#azure-landing-zone-architecture
- Azure Landing Zones Accelerator platform landing zone template: https://github.com/Azure/alz-terraform-accelerator/tree/main/templates/platform_landing_zone
- PerfectThymeTech data landing zone module: https://github.com/PerfectThymeTech/terraform-azurerm-data-landing-zone
- Azure landing zone design areas (networking, identity): https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/
- Azure management groups (policy inheritance scope): https://learn.microsoft.com/en-us/azure/governance/management-groups/overview

### Scopes and responsibility model (spec FR-2.4)
- Databricks account console / account admin docs: https://learn.microsoft.com/en-us/azure/databricks/admin/
- Databricks identity federation (account-level users/groups/SPs): https://learn.microsoft.com/en-us/azure/databricks/admin/users-groups/
- Unity Catalog automatic enablement and metastore assignment: https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog/enable-workspaces
- Azure Policy overview: https://learn.microsoft.com/en-us/azure/governance/policy/
- Azure Policy built-ins for tags (require/deny) and allowed locations: https://learn.microsoft.com/en-us/azure/governance/policy/samples/built-in-policies
- Databricks Terraform provider authentication at account vs. workspace level: https://registry.terraform.io/providers/databricks/databricks/latest/docs#authentication

### Terraform discipline
- Terraform backends / remote state: https://developer.hashicorp.com/terraform/language/backend
- Terraform module versioning / registry conventions: https://developer.hashicorp.com/terraform/language/modules
- Azure Policy as code: https://learn.microsoft.com/en-us/azure/governance/policy/
- [Research needed: pick one opinionated CI/CD reference for plan/apply approval gates]

### Databricks pipelines as code
- Databricks Asset Bundles docs: https://learn.microsoft.com/en-us/azure/databricks/dev-tools/bundles/
- Terraform Databricks provider: https://registry.terraform.io/providers/databricks/databricks/latest/docs
- Unity Catalog docs (objects, grants): https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog/
- Part 1 (internal back-link, mandatory): /blog/2026-08-12-merging-azure-databricks-costs-with-focus

## Version Pins (fill at T004)

- [x] Azure Landing Zones Accelerator platform landing zone template: v17.4.0 (implemented configuration, verified 2026-08-19)
- [x] terraform-azurerm-data-landing-zone: v0.8.0 (latest release, verified 2026-08-18)
- [x] databricks/databricks Terraform provider: 1.127.0 (latest, published 2026-08-17, verified 2026-08-18)
- [ ] Databricks CLI (bundles): vX.Y.Z (confirm before Review Gate 3)

## Verified Technical Facts (re-verify during draft)

- Azure Databricks tag propagation (`ClusterId`/`WorkspaceId`) and post-11's
  unmatched-bucket problem are the motivation for provision-time tag
  enforcement (see post-11 research.md; do not re-derive).
- DABs deploy jobs, DLT pipelines, and workspace files per target environment;
  Unity Catalog securables are generally better managed by the Terraform
  provider. [Verify current guidance at draft time; the DAB/Terraform boundary
  moves between releases.]
- The Platform Landing Zone provisions the management-group hierarchy and
  central management and connectivity foundation. The DLZ module provisions
  the data workload resources and integrates them with that foundation.
  [Verify exact resource coverage against the pinned versions.]
- Unity Catalog is enabled automatically for new Azure Databricks workspaces.
  A metastore is an account-level, regional container that can serve multiple
  workspaces.

## Target Search Queries (spec FR-6)

Primary keyword cluster: *Terraform Azure Databricks*, *data landing zone*,
*Databricks Asset Bundles*, *IaC best practices*, *Unity Catalog Terraform*.

Head terms (definitional/informational):
- "what is a data landing zone"
- "Azure Databricks Terraform"
- "Databricks Asset Bundles"

Long-tail queries (FAQ candidates, phrase as question-form H3s):
- "Should I use Databricks Asset Bundles or Terraform?" *(A: split by
  ownership — infrastructure and UC securables in Terraform, workload
  artifacts in bundles; state the boundary and why.)*
- "How do you deploy a data landing zone with Terraform?" *(A: start with the
  Platform Landing Zone, then deploy the DLZ as an application landing zone;
  pin versions and isolate state per layer.)*
- "How do you enforce cost-attribution tags on Azure Databricks clusters?"
  *(A: provision-time tag policies/defaults in Terraform; closes the unmatched
  bucket from Part 1.)*
- "Can Databricks Asset Bundles manage Unity Catalog objects?" *(A: limited;
  catalogs/schemas/grants belong in the Terraform provider — verify current
  DAB capabilities at draft time.)*
- "Who should own the Databricks account, the platform team or the data
  team?" *(A: platform team; account scope spans workspaces,
  while data teams own workspace-level workloads. FAQ candidate for the T211
  section; not shipped — the published FAQ carries four other questions.)*

AI answer engines favor: answer-first paragraphs, self-contained 40–80-word
blocks, tables/lists, expanded acronyms, and citation-dense pages. All
codified in spec FR-6.

## Open Research Items

- [x] Confirm current PerfectThymeTech module release versions and resource coverage (T004). → data landing zone module pinned at v0.8.0; the platform foundation uses the Azure Landing Zones Accelerator `platform_landing_zone` template (v17.4.0). The data management zone (DMLZ) module is **not** deployed in this post: the DMLZ is framed as planned research only.
- [ ] Confirm the current DAB vs. Terraform provider boundary for UC objects (draft asserts the boundary with a re-verify caveat).
- [x] Find citable deployment/ROI numbers, or scope claims to external sources only (spec §7). → Scoped: draft uses no uncited statistics.
- [x] Choose the CI/CD reference implementation for the plan/apply section. → Described generically (PR-gated plan, post-merge apply, Platform Landing Zone approval gate) without linking a single vendor implementation.
