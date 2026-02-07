<!-- [TEMPLATE-META] SETUP INSTRUCTIONS
You are reading a CLAUDE.md template that needs to be filled for this project.
Follow these steps in order:

1. Scan the project: Read package.json (or pyproject.toml, Cargo.toml, go.mod),
   README.md, and the top-level directory structure.
2. Fill all [AUTO-FILL] markers by scanning project files as described in each section.
3. Present all [REQUIRED] markers to the user for decisions. Show sensible defaults.
4. Process all [TEMPLATE-META] sections (they contain guidance for you), then delete
   the TEMPLATE-META markers and their content when done.
5. Remove ALL markers when done. The final file should have NO markers.
6. Target: 200-350 filled lines. If the result exceeds 400 lines, split into a root
   CLAUDE.md and subfolder CLAUDE.md files (see Subfolder Guide section below).
7. After filling, validate: every instruction should be specific, actionable, and
   universally applicable to this project. Remove anything vague or task-specific.
-->

# CRITICAL OVERRIDES

<!-- [REQUIRED] Project-specific rules that override Claude defaults.
Ask the user: "What rules should Claude ALWAYS follow in this project?"
Format: "NEVER do X because Y. INSTEAD: Z."
Keep to 3-7 critical rules. These are the highest-leverage lines in the file.

Example overrides (delete and replace with project-specific rules):
-->

- Use `rg` (ripgrep) and `fd` instead of `find` and `grep`. Use `jq` for JSON. Always limit output to prevent context window overflow.
- NEVER use inline styles. Use Tailwind CSS utility classes. Maintain a coherent design system across the entire application.
- FIRECRAWL MCP: NEVER use `scrapeOptions` with `firecrawl_search` (causes 200K+ token overflow). ALWAYS use two-step: `firecrawl_search(query, limit=5)` then `firecrawl_scrape(url, formats=["markdown"], onlyMainContent=true)`.

---

## Project Overview

<!-- [AUTO-FILL] Scan README.md and package.json (or equivalent manifest).
Fill in: project name, one-line description, purpose, and target users.

Example filled state:
-->

| Field | Value |
|-------|-------|
| Name | my-app |
| Description | A full-stack web application for task management |
| Purpose | Enable teams to track and prioritize work collaboratively |
| Target Users | Small engineering teams (5-20 people) |

## Tech Stack

<!-- [AUTO-FILL] Scan package.json dependencies (or equivalent manifest).
Generate a table with Layer, Technology, and Version columns.
Include: framework, language, database, styling, testing, and key libraries.

Example filled state:
-->

| Layer | Technology | Version |
|-------|-----------|---------|
| Framework | Next.js (App Router) | 15.x |
| Language | TypeScript | 5.x |
| Database | Supabase (PostgreSQL) | - |
| Styling | Tailwind CSS | 4.x |
| UI Components | shadcn/ui | latest |
| Testing | Vitest + Playwright | - |
| Package Manager | pnpm | 9.x |

## Commands

<!-- [AUTO-FILL] Scan package.json "scripts" (or Makefile, justfile, etc.).
List the most common dev commands. Include: dev, build, test, lint, format.

Example filled state:
-->

| Command | Description |
|---------|-------------|
| `pnpm dev` | Start development server |
| `pnpm build` | Production build |
| `pnpm test` | Run unit tests |
| `pnpm test:e2e` | Run end-to-end tests |
| `pnpm lint` | Lint with ESLint |
| `pnpm format` | Format with Prettier |

## Architecture

<!-- [AUTO-FILL] Scan the src/ (or equivalent) directory structure.
Describe each top-level directory's purpose in 1 line. Focus on where things live,
not how they work -- Claude discovers implementation details when reading files.

Example filled state for a Next.js App Router project:
-->

```
src/
  app/           -- Next.js App Router pages and layouts
  components/    -- Shared React components (ui/, forms/, layout/)
  lib/           -- Utilities, helpers, and shared logic
  hooks/         -- Custom React hooks
  types/         -- TypeScript type definitions
  actions/       -- Server actions (mutations)
  queries/       -- Data fetching functions
supabase/
  migrations/    -- SQL migration files
  seed.sql       -- Development seed data
```

## File Structure

<!-- [AUTO-FILL] Generate a top-level directory tree (depth 2).
Use: fd --type d --max-depth 2 | head -30
Or: ls the top-level directories and describe their contents.

Example filled state:
-->

```
.
├── src/
│   ├── app/
│   ├── components/
│   ├── lib/
│   ├── hooks/
│   └── types/
├── supabase/
│   └── migrations/
├── public/
├── tests/
├── package.json
├── tailwind.config.ts
└── tsconfig.json
```

## Code Conventions

<!-- [REQUIRED] Ask the user about their coding conventions.
Topics to cover: naming, imports, error handling, component patterns.
Show these defaults and ask which to keep, modify, or remove:
-->

**Naming:**
- Files: `kebab-case.ts` for utilities, `PascalCase.tsx` for components
- Variables/functions: `camelCase`
- Types/interfaces: `PascalCase`, prefix interfaces with `I` only when needed for disambiguation
- Constants: `UPPER_SNAKE_CASE` for true constants, `camelCase` for derived values

**Imports:**
- Order: React/framework > external packages > internal modules > relative imports > types
- Use path aliases (`@/` maps to `src/`)
- Prefer named exports over default exports

**Error Handling:**
- Use typed error boundaries for React components
- Server actions return `{ success, data, error }` result objects -- never throw
- Log errors with structured context, not bare `console.error`

**Components:**
- Prefer server components; add `"use client"` only when state or interactivity is needed
- Colocate component, types, and tests in the same directory when component-specific

## Environment Setup

<!-- [REQUIRED] Ask the user: "What environment variables does this project need?"
List each variable with its purpose and where to obtain it.

Example filled state:
-->

| Variable | Purpose | Where to Get |
|----------|---------|-------------|
| `NEXT_PUBLIC_SUPABASE_URL` | Supabase project URL | Supabase dashboard > Settings > API |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Supabase anonymous key | Supabase dashboard > Settings > API |
| `SUPABASE_SERVICE_ROLE_KEY` | Server-side admin key | Supabase dashboard > Settings > API |

## Git Workflow

<!-- [REQUIRED] Ask the user: "What branching and commit conventions do you follow?"
Show these defaults:
-->

- **Branching:** Feature branches off `main` (format: `feature/NNN-short-description`)
- **Commits:** Conventional commits (`feat:`, `fix:`, `refactor:`, `docs:`, `test:`, `chore:`)
- **PRs:** Require passing CI checks before merge. Squash-merge to `main`.
- **Do not** force-push to `main` or `develop`. Always create new commits over amending shared history.

## Testing Strategy

<!-- [REQUIRED] Ask the user: "What testing tools and coverage targets do you use?"
Show these defaults:
-->

- **Unit tests:** Vitest -- colocate with source as `*.test.ts(x)`
- **E2E tests:** Playwright -- in `tests/e2e/`
- **Coverage target:** 80% line coverage for business logic
- **Test pattern:** Arrange-Act-Assert. Test behavior, not implementation.
- **TDD encouraged:** Write failing test first, then implement, then refactor.

## MCP Integrations

<!-- [AUTO-FILL] Scan .claude/settings.json or .mcp.json for configured MCP servers.
List each active MCP with a one-line usage rule.

Example filled state:
-->

| MCP Server | Usage Rule |
|-----------|-----------|
| Supabase | Use for ALL database operations. Never use the Supabase CLI. |
| Shadcn | Use for installing UI components. Never manually copy component code. |
| Firecrawl | Two-step only: search then scrape. Never use `scrapeOptions` with search. |
| Ref | Use for looking up framework documentation and API references. |

## Anti-Patterns

<!-- [REQUIRED] Ask the user: "What mistakes should Claude NEVER make in this project?"
Format each as: "NEVER X because Y. INSTEAD: Z."
Include 3-5 critical anti-patterns.

Example anti-patterns (replace with project-specific ones):
-->

- NEVER use the Supabase CLI for migrations or database operations because the MCP provides better integration. INSTEAD: Use the Supabase MCP tools.
- NEVER add `console.log` for debugging and leave it in committed code because it pollutes production logs. INSTEAD: Use a structured logger or remove debug statements before committing.
- NEVER create new files when editing existing ones would work because it causes file bloat. INSTEAD: Prefer editing existing files and only create new ones when truly necessary.
- NEVER import components from external libraries without checking if shadcn/ui has an equivalent because it breaks design consistency. INSTEAD: Check the shadcn registry first.
- NEVER write API routes when a server action would suffice because it adds unnecessary complexity. INSTEAD: Use server actions for mutations, API routes only for webhooks or external consumers.

---

<!-- [TEMPLATE-META] DOCUMENTATION PHILOSOPHY

Teach Claude the 3-layer documentation model, then delete this section:

**Layer 1: CLAUDE.md (conventions) -- auto-loaded, <400 lines**
What goes here: universal rules, tech stack, commands, architecture overview.
This file is loaded into context on EVERY session. Every line has a cost.
Think of it as "how we work here" -- project-wide conventions and constraints.

**Layer 2: memory/ (learnings) -- on-demand**
What goes here: patterns discovered during development, architecture decisions
with rationale, lessons learned from debugging sessions.
Files: memory/patterns.md, memory/decisions.md, memory/learnings.md
Think of it as "what we have learned" -- evolving institutional knowledge.
Claude reads these when relevant context is needed, not on every session.

**Layer 3: specs/ (target state) -- on-demand**
What goes here: feature specifications, acceptance criteria, implementation plans.
Think of it as "what we are building" -- requirements and design documents.
Claude reads these when working on specific features.

Key principle: CLAUDE.md should NEVER contain task-specific instructions, feature
specs, or temporary notes. Those belong in memory/ or specs/. If CLAUDE.md grows
past 400 lines, something that belongs in a deeper layer has leaked into it.
-->

<!-- [TEMPLATE-META] SUBFOLDER CLAUDE.md GUIDE

When to create subfolder CLAUDE.md files:
- Root CLAUDE.md exceeds 400 lines
- A module has conventions that differ from the root (e.g., different naming)
- Team members work in isolated directories and need scoped rules

Where to create them:
- src/components/CLAUDE.md  -- component patterns, UI conventions
- src/api/CLAUDE.md or supabase/CLAUDE.md -- backend/database rules
- tests/CLAUDE.md -- test utilities, fixtures, mocking patterns
- packages/*/CLAUDE.md -- monorepo package-specific conventions

What goes in root vs subfolder:
- Root: project-wide commands, tech stack, architecture overview, critical overrides
- Subfolder: module-specific patterns, local import rules, domain vocabulary

Claude Code loads subfolder CLAUDE.md files on-demand when reading files in that
directory, so they add zero cost when working elsewhere in the project.
-->

<!-- [TEMPLATE-META] README BEST PRACTICES

A good project README should contain:
- Quick start: clone, install, run in under 60 seconds
- Architecture overview: high-level diagram or description
- Development workflow: branch, test, PR, deploy
- Environment setup: what env vars are needed, how to get them
- Deployment guide: how to ship to production
- Contributing guidelines: code style, PR process, review expectations

The README is for humans. CLAUDE.md is for Claude. Do not duplicate content
between them -- CLAUDE.md should reference README sections when relevant:
  "See README.md for deployment instructions."
-->

## Memory Auto-Sync

When you discover patterns, make decisions, or learn from mistakes during development, write them to the appropriate memory file:

| File | Content | Format |
|------|---------|--------|
| `memory/patterns.md` | Recurring code patterns and conventions discovered | `## Pattern Name` with example |
| `memory/decisions.md` | Architecture and technology decisions with rationale | `## Decision` with context, options, rationale |
| `memory/learnings.md` | Lessons from debugging, failed approaches, gotchas | `## Learning` with what happened and what to do instead |

Rules:
- Append new entries; do not overwrite existing ones
- Keep each file under 200 lines; archive old entries if needed
- Only write genuinely reusable insights, not task-specific notes
