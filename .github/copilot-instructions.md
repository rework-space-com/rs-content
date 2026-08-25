# Copilot Instructions

## Skills and agent artifacts

This repository stores reusable Copilot skills and other agent artifacts under
the `.agents/` directory:

- `.agents/skills/` — individual skills, each in its own subdirectory with a
  `SKILL.md` entry point describing when and how to use it.
- `.agents/.skill-lock.json` — lockfile tracking installed skills, their
  source, and which specs/content reference them.

Before starting a task, check `.agents/skills/` for a skill whose
description matches the task (e.g. writing or reviewing blog content). If a
matching skill exists, load and follow its `SKILL.md` instructions rather
than improvising. Do not assume `.agents/` is empty or unused — treat it as
the canonical location for project-specific skills and artifacts.
