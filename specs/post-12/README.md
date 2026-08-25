# Spec Package — post-12: Landing Zones as Code (Part 2 of the FOCUS series)

Spec-driven development package for writing `content/en/blog/post-12.md`.

| File | Purpose | Read when |
|---|---|---|
| [constitution.md](constitution.md) | Non-negotiable writing principles (voice, banned language, Hugo conventions) | Always, before any writing |
| [spec.md](spec.md) | Requirements, front matter schema, acceptance criteria, open questions | Before starting; check acceptance criteria before publish |
| [plan.md](plan.md) | Writing approach, heading tree, risks, review gates | Before drafting |
| [tasks.md](tasks.md) | Phased task checklist with review gates | During execution; keep checkboxes current |
| [research.md](research.md) | Source links, version pins, target queries, open research items | While drafting sections |

## Project Skills (.agents/skills)

Execution of this spec package MUST use the workspace skills:

| Skill | Role in this package | Used in |
|---|---|---|
| [blog-writing-guide](../../.agents/skills/blog-writing-guide/SKILL.md) | Authoritative writing standards: voice, banned language, openings, headings, formatting, SEO, AI-tell avoidance | Constitution §I–V, VII; Draft and Compliance phases |
| [content-research-writer](../../.agents/skills/content-research-writer/SKILL.md) | Collaborative outlining, research + citations, hook improvement, section-by-section feedback, iterative refinement | Research updates; Skeleton, Draft, and review-gate feedback loops |

Before executing any phase, read the relevant SKILL.md file(s) in full.

## Workflow

```
constitution → spec → plan → tasks → execute (post-12.md)
                ↑______ feedback loops at each review gate ______↑

skills: blog-writing-guide (standards)  +  content-research-writer (process)
```

Inputs:
- Structure template: [post-10.md](../../content/en/blog/post-10.md)
- SEO/AEO + FAQ pattern: [post-11.md](../../content/en/blog/post-11.md) and its [spec package](../post-11/spec.md)
- Part 1 promise being fulfilled: post-11 "The bigger architecture" section and task T502

Key differences from the post-11 package:
- SEO/AEO (spec FR-6) is built into the skeleton and draft phases, not a
  post-publication retrofit.
- Code samples (HCL/YAML) must be verified against pinned module/provider
  versions before Review Gate 2.
- The post-11 back-link is a hard requirement (spec FR-2.1/FR-6.8).
