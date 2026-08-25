# Constitution — Rework-Space Blog Writing Principles

Non-negotiable principles governing how `content/en/blog/post-12.md` is written.
All spec, plan, and task decisions must comply with this document.

## 0. Authoritative Skills

Sections I–V and VII summarize the
[blog-writing-guide skill](../../.agents/skills/blog-writing-guide/SKILL.md).
That SKILL.md is the authoritative source: if this constitution and the skill
disagree, the skill wins, and this file must be updated.

The writing *process* (outlining, research, citations, hook iteration,
section feedback) follows the
[content-research-writer skill](../../.agents/skills/content-research-writer/SKILL.md).

Agents executing any task in this package must read both SKILL.md files
before writing prose.

## I. Voice & Tone

- Sound like a senior engineer explaining something they are genuinely excited about: smart, specific, direct, deeply knowledgeable.
- Never sound like a press release, sales deck, or AI-generated summary.
- Use "we" (Rework-Space) and "you" (the reader). This is a conversation, not a paper.
- Highly technical, authoritative, and strategic: speak to both *engineering friction* and *business outcomes*.

## II. Banned Language (automatic rejection)

- "We're excited/thrilled to announce"
- "Best-in-class" / "industry-leading" / "cutting-edge"
- "Seamless" / "seamlessly"
- "Empower" / "leverage" / "unlock" (as marketing verbs)
- "Robust" (describe what makes it robust instead)
- "Streamline"
- Filler transitions: "That being said," "It's worth noting that," "At the end of the day," "Without further ado"
- "In this blog post, we will explore..."

## III. Openings & Structure

- The opening (first 2–3 sentences) must **state the problem** or **state the conclusion**. Never background, history, or hype.
- Structure follows the reader's questions:
  1. What problem does this solve? (1–2 paragraphs max)
  2. How does it actually work? (bulk of the post, be specific)
  3. What were the trade-offs or alternatives?
  4. How do I use/try/implement this?
- Deep-dives must also cover: what didn't work, and known limitations.

## IV. Formatting

- Short paragraphs; break at contrast points ("but", "however").
- One idea per paragraph. One-sentence paragraphs are fine for emphasis.
- **No em dashes.** Use commas, periods, or line breaks.
- H3 subheadings must convey information ("DABs vs. Terraform: who owns the workspace objects"), never generic labels ("Background", "Results"). H2s are exempt: they follow the short `color-text` template style (§VI).
- Avoid AI-writing tells: staccato fragments, bumper-sticker aphorisms, three-beat reveals, smug simplicity, parallel-structure ad copy, personality-only-in-bookends.

## V. Technical Quality

- Numbers over adjectives: every performance/savings claim carries a number.
- Code, HCL, and YAML snippets must be plausible and complete enough to adapt (providers, versions, context); pin module/provider versions in prose.
- Diagrams for any system with more than two interacting components; label with real service names.
- Honesty over hype: acknowledge limitations, betas, and competitor strengths.

## VI. Hugo/Site Conventions (from the post-10/post-11 template)

`post-10.md` (with `post-11.md` extensions) is the binding structural template.
The full grammar is codified in spec.md FR-1 (front matter) and FR-5 (body
constructs); summary:

- Front matter: exact field order, single-quoted strings, spaced arrays, unquoted booleans/date.
- H2 headings: `## {{</* color-text text="..." */>}}` with short text (1–4 words, like "The problem", "FAQ").
- H3 headings: plain Markdown (`### ...`), may be descriptive.
- External links: `{{</* color-link link_title="..." path="..." target="_blank" */>}}` on its own line, sentence wrapping around it. Internal links use `/blog/...` paths.
- Images: `{{</* img src="/blog-images/post-12/..." alt="Picture N. ..." */>}}`, sequential numbering, cross-referenced as "(Picture N)" in prose.
- Fenced, language-tagged code blocks (```hcl, ```yaml, ```sql) for code samples.
- Emphasis: `**bold**` for key terms at first mention; `_italics_` for identifiers and group/feature names.
- No raw Markdown links/images or HTML tags in the body.
- In-body images live in `assets/blog-images/post-12/`; social/featured images in `static/static-blog-images/post-12/` with `-short`/`-share`/`-twitter-share` filename suffixes.
- URL slug pattern: `/blog/YYYY-MM-DD-descriptive-slug`.

## VII. SEO & AI Search

- Keywords in H3s, title, `keywords` front matter, and body prose (H2s are short template labels, see §VI).
- Include a definitional section for the head term ("What is a Data Landing Zone?").
- Lead generic (educational), close specific (Rework-Space/implementation).
- Answer-first section openings, a question-form FAQ block, extractable tables/lists, expanded acronyms, and citation-dense claims (spec FR-6, pattern proven on post-11).

## Governance

Any deviation from this constitution requires an explicit note in `spec.md`
under "Deviations" with a one-line justification.
