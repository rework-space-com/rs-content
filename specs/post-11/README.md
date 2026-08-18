# Spec Package — post-11: Merging Azure Infrastructure and Databricks DBUs with FOCUS

Spec-driven development package for writing `content/en/blog/post-11.md`.

| File | Purpose | Read when |
|---|---|---|
| [constitution.md](constitution.md) | Non-negotiable writing principles (voice, banned language, Hugo conventions) | Always, before any writing |
| [spec.md](spec.md) | Requirements, front matter schema, acceptance criteria, open questions | Before starting; check acceptance criteria before publish |
| [plan.md](plan.md) | Writing approach, heading tree, risks, review gates | Before drafting |
| [tasks.md](tasks.md) | Phased task checklist with review gates | During execution; keep checkboxes current |
| [research.md](research.md) | Source links, verified facts, open research items | While drafting sections |

## Project Skills (.agents/skills)

Execution of this spec package MUST use the workspace skills:

| Skill | Role in this package | Used in |
|---|---|---|
| [blog-writing-guide](../../.agents/skills/blog-writing-guide/SKILL.md) | Authoritative writing standards: voice, banned language, openings, headings, formatting, SEO, AI-tell avoidance | Constitution §I–V, VII; Draft and Compliance phases |
| [content-research-writer](../../.agents/skills/content-research-writer/SKILL.md) | Collaborative outlining, research + citations, hook improvement, section-by-section feedback, iterative refinement | Research updates; Skeleton, Draft, and review-gate feedback loops |

Before executing any phase, read the relevant SKILL.md file(s) in full.

## Workflow

```
constitution → spec → plan → tasks → execute (post-11.md)
                ↑______ feedback loops at each review gate ______↑

skills: blog-writing-guide (standards)  +  content-research-writer (process)
```

Inputs:
- Template: [post-10.md](../../content/en/blog/post-10.md)
- Content plan:  Gemini notebook [Blog Post Plan: Beyond Separated Billing: Merging Azure Infrastructure and Databricks DBUs with FOCUS](https://notebook.google.com/notebook/7b9a8205-90bd-4aa2-8359-ba7e262763e9)

