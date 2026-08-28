# Portfolio Site — Project Brief

Reference document for developing the portfolio site with an AI coding agent (Claude Code + GitHub Spec Kit). Use this as the source for `CLAUDE.md` and the initial `/speckit.constitution`.

---

## 1. Goal

Personal portfolio site for Yurii Shchebetun, Frontend Developer (React/Next.js/TypeScript), currently job-searching. Must read as a **product**, not a static landing page — case-study driven, with a working small backend touchpoint (contact form API route) as a first step toward a separate fullstack flagship project (planned next, out of scope for this brief).

Content covers **only real Tallium projects** (no placeholder/fake projects): Arclap, Sw!tch, Loop for Success, Dopper, Shell Recharge, ServiceHive, Tallium corporate site.

**v1 (MVP) scope**: Home, Projects index, Project case studies, About, Contact. Blog is explicitly **out of scope for v1** — see Section 12 (Post-MVP).

---

## 2. Tech Stack

- **Framework**: Next.js (App Router, 14+), TypeScript strict mode (no `any`; use `unknown` + narrowing)
- **Styling**: Tailwind CSS (deliberate choice for this project, distinct from CSS Modules used elsewhere — see note below)
- **Content**: MDX for project case studies (no headless CMS for v1). Blog content/storage deferred — see Section 12
- **i18n**: `next-intl`, locale-prefixed routing (`/en`, `/uk`), English as default locale
- **Theme**: `next-themes` (light/dark, class-based, no flash of wrong theme)
- **Animation**: `framer-motion` (or native CSS transitions where sufficient) — subtle, purposeful only
- **Testing**: Jest + React Testing Library (unit/component), Playwright (e2e)
- **CI/CD**: GitHub Actions (lint → test → build), deploy on Vercel
- **AI workflow**: GitHub Spec Kit (spec-driven development) with Claude Code

### Note on Tailwind vs. CSS Modules

Default preference elsewhere is CSS Modules. Tailwind is used here intentionally to demonstrate range for recruiters/reviewers. State this explicitly in the README so it doesn't read as inconsistent.

---

## 3. Code Conventions (constitution source)

- TypeScript strict; never `any`; prefer `unknown` with narrowing
- Prefer `type` over `interface` unless declaration merging is needed
- Functional components + hooks only
- Server Components by default; `'use client'` only when necessary (theme provider, toggle, interactive/animated elements, contact form)
- Named exports for all components and utilities
- Comments in English, explain _why_ not _what_
- Conventional Commits format (commit messages generated only on request)
- Never touch `.env` files or hidden/dotfiles unless explicitly asked

---

## 4. Development Workflow (Spec Kit + Claude Code)

### Setup

```bash
npx create-next-app@latest portfolio --typescript --tailwind --app
cd portfolio
uv tool install specify-cli
specify init --here --ai claude
```

### Per-feature cycle

```
/speckit.constitution   → once, at project start (encodes section 3 above)
/speckit.specify        → what and why, no implementation details
/speckit.plan           → technical plan (components, routes, data shape)
/speckit.tasks          → task breakdown
/speckit.implement      → Claude writes the code
```

Each cycle produces a `specs/00X-feature-name/` folder — keep these committed. They double as material for a future blog case-study post on spec-driven development.

**Recommendation**: run the first 1–2 features (layout + home page) through the full manual cycle before deciding whether the constitution needs adjusting.

### `CLAUDE.md` (root file, read automatically each session)

Should summarize: stack + commands (`npm run dev/test/lint`), folder structure (section 6), pointer to `specs/` for active plans, rule against touching `.env` / committing without review.

### Non-functional rule for `/speckit.plan`

Every feature plan must include a testing step (unit and/or e2e as relevant) before `/speckit.implement` is considered complete.

---

## 5. Testing

| Level          | Tool                         | Covers                                                                                                          |
| -------------- | ---------------------------- | --------------------------------------------------------------------------------------------------------------- |
| Unit/component | Jest + React Testing Library | `lib/mdx.ts` parsing logic, `ProjectCard`, `Header`, theme toggle logic                                         |
| E2E            | Playwright                   | Home → project navigation, contact form submit, theme switch persistence, locale switch (URL + content changes) |

Decide once and document in `CLAUDE.md`: co-located `*.test.ts` next to source files, or a top-level `__tests__/` — do not mix both.

CI: GitHub Actions — `lint → test → build` on every PR.

---

## 6. Folder Structure

```
portfolio/
├── app/
│   ├── [locale]/
│   │   ├── layout.tsx              # locale-aware root layout: fonts, metadata, ThemeProvider, next-intl provider
│   │   ├── page.tsx                # home: hero + intro + featured projects
│   │   ├── about/
│   │   │   └── page.tsx
│   │   ├── projects/
│   │   │   ├── page.tsx                # all projects grid
│   │   │   └── [slug]/
│   │   │       └── page.tsx            # case-study page (MDX-driven)
│   │   │                                # (blog/ route deferred — see Section 12)
│   │   └── api/
│   │       └── contact/
│   │           └── route.ts            # contact form handler
│   ├── globals.css                 # Tailwind directives + CSS variables (light/dark theme tokens)
│   └── middleware.ts                # locale detection + redirect (default: en)
│
├── messages/
│   ├── en.json                      # UI strings: nav, buttons, headings
│   └── uk.json
│
├── components/
│   ├── Header/
│   ├── ThemeToggle/                 # client component
│   ├── LocaleSwitcher/              # client component
│   ├── ProjectCard/
│   └── ...
│
├── content/
│   └── projects/                    # en/arclap.mdx, uk/arclap.mdx, etc. — per-locale case studies
│                                     # (blog/ deferred — see Section 12)
│
├── lib/                             # mdx.ts, projects.ts (typed frontmatter parsing)
├── types/
│   └── content.ts                   # Project, BlogPost types
├── specs/                           # Spec Kit output per feature
├── e2e/
├── public/images/
├── jest.config.ts
├── playwright.config.ts
├── tailwind.config.ts
└── next.config.ts
```

---

## 7. Theming

- `next-themes` with `class` strategy, `darkMode: 'class'` in Tailwind config
- CSS custom properties in `globals.css`: `--color-bg`, `--color-text`, `--color-accent`, etc., defined for `:root` and `.dark`
- `ThemeProvider` and toggle button are the only client components required for theming; rest of the tree stays server-rendered

---

## 8. Design & Animation Direction

- Load the frontend-design skill before implementing layout/visual decisions — avoid default/templated look (typography, palette, spacing choices should be intentional)
- Animation: restrained, purposeful only —
  - fade-in on scroll for project cards
  - smooth theme transition (no color flash)
  - subtle hover micro-interactions on cards/buttons
- No heavy/decorative motion — audience is B2B/technical reviewers; restraint reads better than density of effects

### Responsive / mobile requirement

- **Mobile-first**, not "desktop + adapted later" — build breakpoints up (base styles for mobile, `md:`/`lg:` Tailwind variants for larger screens)
- Treat this as a hard requirement in every `/speckit.plan`, not a follow-up pass: each feature's plan and tasks must include a mobile-layout step (e.g., hero, project grid, case-study page, contact form)
- Key checks per page/component:
  - Hero and nav collapse cleanly (hamburger/menu pattern on mobile, no horizontal scroll)
  - Project grid reflows to single column on small screens
  - Case-study MDX content (images, code blocks if any) doesn't overflow viewport
  - Contact form fields and touch targets sized for tap, not just click
  - Theme toggle remains reachable/visible on mobile nav
- Test on real breakpoints, not just resizing the desktop browser: at minimum ~375px (small phone), ~768px (tablet), ~1024px+ (desktop)
- Add mobile viewport checks to the Playwright e2e suite (viewport sizing per test or a dedicated mobile project in `playwright.config.ts`)

---

## 9. Localization (EN / UK)

- English is the default locale (primary audience: international recruiters); Ukrainian is secondary
- Locale lives in the URL path (`/en/projects`, `/uk/projects`), handled by `next-intl` middleware — no cookie-only or browser-detection-only switching, so links are shareable and indexable per language
- UI copy (nav, buttons, section headings) lives in `messages/en.json` / `messages/uk.json`
- Case-study content is **not auto-translated** — each MDX file is authored per locale (`content/projects/en/*.mdx`, `content/projects/uk/*.mdx`); a case study can ship in one locale first if the translation isn't ready yet, rather than blocking the page
- A visible locale switcher (client component) belongs in the header/nav, reachable on mobile as well (see Section 8 responsive requirements)
- Metadata (`<title>`, `<meta description>`) must also be localized per route for SEO

## 10. Page Structure & Content

### `/` — Home

- Hero: name, role, one-line positioning, CTA to projects + contact
- Featured projects (2–3 cards): Arclap, ServiceHive, Dopper/Shell Recharge
- Skills overview grouped by category (Frontend / Integration & APIs / Tooling & AI-assisted workflow), not a flat list
- Short About teaser (2–3 sentences on the prosecutor → developer transition) linking to full `/about`

### `/projects` — index

- All Tallium case studies as a grid/list
- Optional filter by tech/domain (can be deferred past v1)

### `/projects/[slug]` — case study (MDX)

Each project file follows this shape:

1. Context — client, domain, team size
2. Role — specific individual contribution, not team-level description
3. Technical decisions — stack + 1–2 notable architectural details (e.g., multi-tier auth on Arclap, BFF aggregation on Dopper)
4. Challenge → solution — one concrete problem-solving story
5. Outcome — qualitative result (no invented metrics)
6. Screenshots/GIFs where available (nothing NDA-sensitive)

Projects to cover: Arclap, Sw!tch, Loop for Success, Dopper, Shell Recharge, ServiceHive, Tallium corporate site.

### `/about`

- Full career story: prosecutor (2014–2019) → ITVDN retraining → WebJumpUA (junior) → Tallium
- Why the switch to tech; transferable strengths (analytical thinking, deadline pressure)
- Short section on AI-assisted workflow (Claude daily use, BMAD method, Spec Kit) — current differentiator, worth calling out explicitly
- Languages, education

### Contact

- Either a Home section or a dedicated `/contact` page: name/email/message form → `app/api/contact/route.ts`
- Direct links: email, LinkedIn (GitHub if made public)

---

## 11. Deployment

- Vercel for the Next.js app (zero-config, matches existing experience)
- GitHub Actions for CI (lint/test/build) ahead of merge

---

## 12. Post-MVP: Blog

Deferred until after v1 ships. Purpose (unchanged from earlier discussion): technical case-study posts, e.g. "Spec-driven development on a real project", "Designing multi-tier auth" — reinforces the AI-assisted workflow narrative from `/about`.

**Open decision — where posts live and how they're authored:**

| Option                                                                                                           | Fit                                                                                                                                                                                                         |
| ---------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **MDX in-repo** (same pattern as project case studies, `content/blog/`)                                          | Simplest, zero extra infra, but content changes require a redeploy — fine if posting is infrequent                                                                                                          |
| **Firebase** (Firestore for post data + Storage for images)                                                      | Fast to set up, no backend to maintain, lets you publish without a redeploy — reasonable if the flagship project won't have a reusable backend, or if this needs to ship before the flagship project exists |
| **Dedicated NestJS backend** (same one planned for the flagship project, with a `blog` module + Prisma/Postgres) | Best if the flagship backend is being built anyway — avoids running two separate backend services, and gives another fullstack talking point ("blog CMS built on my own API") for interviews                |

**Recommendation**: decide this once the flagship project's backend timeline is clearer. If the NestJS backend will exist soon, hold off and reuse it. If the portfolio blog is needed sooner than that, MDX-in-repo is the lowest-effort bridge and can be migrated later without changing the site's frontend.

This decision and its implementation should go through its own `/speckit.specify` → `/speckit.plan` cycle when picked up — it's a separate feature from the v1 MVP scope above.
