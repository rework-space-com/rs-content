# Research Notes — post-11

Reference material backing the spec and plan. Source of truth for links:
[itfm-databricks-blog-plan-v4.md](itfm-databricks-blog-plan-v4.md) §4
(plan file now lives in this spec package).

Research, citation formatting, and closing the open items below follow the
[content-research-writer skill](../../.agents/skills/content-research-writer/SKILL.md)
(research assistance + citation management workflows).

## Key Sources by Section

### FOCUS / ITFM (intro, definitional)
- FinOps Foundation FOCUS portal: https://focus.finops.org/
- FOCUS 1.4 data model spreadsheet
- Apptio ITFM framework guide: https://www.apptio.com/topics/it-financial-management/
- Databricks "Beyond the Spreadsheet" CFO blog

### Azure billing side
- Azure Cost Management FOCUS exports tutorial: https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/tutorial-improved-exports
- FOCUS dataset schema: https://learn.microsoft.com/en-us/azure/cost-management-billing/dataset-schema/cost-usage-details-focus

### Databricks billing side
- System tables docs: https://learn.microsoft.com/en-us/azure/databricks/admin/system-tables/
- Field solution repo: https://github.com/databricks-solutions/cloud-infra-costs
- System tables → FOCUS 1.3 SQL converter (same repo, /focus)
- Databricks blog "Getting the Full Picture"
- DBU pricing page (linked at first DBU mention): https://www.databricks.com/product/pricing
- Governance Hub docs (added at final review): https://docs.databricks.com/aws/en/admin/governance-hub/

### ITFM framing
- TechDogs "data janitor" definition (replaces the uncited "80% of time" figure): https://www.techdogs.com/td-dictionary/word/data-janitor

### Architecture tease
- MS Cloud Adoption Framework unified data platform: https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/data/architecture-azure-landing-zones-unify-data-platform
- Terraform modules (published post links the PerfectThymeTech upstream, not the rework-space-com forks):
  - https://github.com/PerfectThymeTech/terraform-azurerm-data-management-zone
  - https://github.com/PerfectThymeTech/terraform-azurerm-data-landing-zone

### ROI claims
- CloudNuro case study: https://www.cloudnuro.ai/blog/databricks-finops-cost-reduction-case-study
  - 70% reduction in idle All-Purpose compute clusters
  - $2.1M+ annual savings from re-architecting high-frequency jobs

## Verified Technical Facts (to be re-verified during draft)

- Azure Cost Management exports FOCUS-format datasets natively to ADLS Gen2.
- Databricks `system.billing.usage` and `system.billing.list_prices` do NOT
  natively conform to FOCUS; a scheduled SQL transform is required.
- Azure Databricks injects `ClusterId` / `WorkspaceId` tags into the VMs it
  provisions; these are the join keys against Azure billing rows.
- Serverless: VM compute bundled into DBU price (storage still separate).
  Classic: VM compute billed directly by Azure. Pipeline must branch on this.
- Azure billing data is refreshed retroactively during the billing period →
  Delta `MERGE INTO` upserts required for idempotency.

## Target Search Queries (spec FR-6)

Primary keyword cluster: *FOCUS FinOps*, *Azure Databricks cost management*,
*DBU billing*, *unified cost lakehouse*, *ITFM*.

Head terms (definitional/informational):
- "what is FOCUS FinOps" / "FinOps Open Cost and Usage Specification"
- "Azure Databricks cost management"
- "Databricks DBU cost"

Long-tail queries (FAQ candidates, phrase as question-form H3s):
- "Does Azure Databricks export FOCUS data natively?" *(A: Azure side yes via
  Cost Management exports; Databricks system tables need a SQL transform.)*
- "How do you join Azure billing data with Databricks DBU usage?" *(A:
  ClusterId/WorkspaceId tags on the VMs as join keys.)*
- "Why do serverless Databricks costs get double-counted?" *(A: VM compute is
  bundled into the serverless DBU price; adding matched infra rows counts
  hardware twice.)*
- "How do you handle retroactive Azure billing restatements in Delta Lake?"
  *(A: idempotent `MERGE INTO` upserts keyed on ChargePeriodStart + ResourceId
  + SkuId.)*

AI answer engines (ChatGPT, Perplexity, AI Overviews) favor: answer-first
paragraphs, self-contained 40–80-word blocks, tables/lists, expanded
acronyms, and citation-dense pages. All codified in spec FR-6.

## Open Research Items

- [x] Confirm current FOCUS spec version to cite → published post cites the FOCUS 1.3 converter query; no explicit spec-version claim in prose beyond timestamp precision.
- [x] Post length → published at the longer deep-dive depth within the 1,500–2,500 target.
- [x] "Sovereign Data Project Concept" internal link → dropped; the published post contains no internal links.
