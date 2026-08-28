# CLAUDE.md

Project context Claude Code should hold in mind every session.

## Stack

- Next.js (App Router, 14+), TypeScript strict (no `any`; use `unknown` + narrowing)
- Tailwind CSS (deliberate choice for this project — default elsewhere is CSS Modules)
- `next-intl` for i18n: locale-prefixed routes (`/en`, `/uk`), English default
- MDX for project case studies (`content/projects/<locale>/`)
- `next-themes` for light/dark (class-based)
- `framer-motion` / CSS transitions for subtle, purposeful animation only
- Jest + React Testing Library (unit), Playwright (e2e, incl. mobile viewports and locale switching)

## Conventions

- Functional components + hooks only; named exports everywhere
- Server Components by default; `'use client'` only where needed (ThemeProvider, ThemeToggle, LocaleSwitcher, contact form, animated bits)
- Prefer `type` over `interface` unless declaration merging is needed
- Comments in English, explain _why_ not _what_
- Mobile-first: base styles for mobile, `md:`/`lg:` Tailwind variants up
- Never touch `.env` files or dotfiles unless explicitly asked
- Conventional Commits — only generate commit messages on request

## Folder structure

```
app/
  [locale]/     # locale-scoped routes: home, about, projects, projects/[slug], api/contact
  middleware.ts # locale detection + redirect, default: en
messages/       # en.json, uk.json — UI copy for next-intl
components/     # shared UI (Header, ThemeToggle, LocaleSwitcher, ProjectCard, ...)
content/        # MDX: projects/en/, projects/uk/ (blog/ deferred, see specs)
lib/            # mdx.ts, projects.ts — typed frontmatter parsing
types/          # content.ts — Project, BlogPost types
specs/          # Spec Kit output per feature — source of truth for "why"
e2e/            # Playwright tests
```

## Workflow

- Every feature goes through `/speckit.specify` → `/speckit.plan` → `/speckit.tasks` → `/speckit.implement`
- Every `/speckit.plan` must include a testing step (unit and/or e2e) and a mobile-layout step before implementation is considered done
- `specs/00X-feature-name/` folders stay committed — they're documentation, not scratch files

## Scope (v1 / MVP)

Home, Projects index, Project case studies (Arclap, Sw!tch, Loop for Success, Dopper, Shell Recharge, ServiceHive, Tallium corporate site), About, Contact.
Blog is **out of scope for v1** — deferred, storage decision (MDX/Firebase/shared NestJS backend) pending flagship project timeline.

## Full context

Detailed page structure, content, and reasoning: see `docs/project-brief.md`.
