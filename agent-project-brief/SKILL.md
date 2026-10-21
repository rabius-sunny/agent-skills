---
name: agent-project-brief
description: >-
  Audits a codebase, extracts modular architecture and rule documentation into .agents/docs/
  for progressive disclosure, and creates a concise, high-signal AGENTS.md and CLAUDE.md
  (<150 lines) strictly adhering to the Agent's Project Brief specification. Use whenever
  initializing agent guidelines, refactoring bloated prompt files, or aligning repository
  documentation with agent workflows.
---

# Agent Project Brief & Modular Documentation Architect

A comprehensive methodology and reference for auditing codebases, extracting modular documentation, and creating lean, high-signal `AGENTS.md` and `CLAUDE.md` files.

## Overview & Philosophy

The root instruction file (`AGENTS.md` / `CLAUDE.md`) is the first file loaded into an agent's context on every session turn. Every line consumes context and attention:
- **Instruction Budget**: Keep root briefs under 150 lines. Bloated instructions degrade instruction-following and waste tokens.
- **Progressive Disclosure**: The root file is a **map**, not an encyclopedia. Deep topic guidelines (Auth, Architecture, CRUD, Caching) belong in `.agents/docs/*.md` and are linked from the root file. Agents load them only when relevant.
- **Context vs Enforcement**: The brief tells the agent that enforcement tools (lint, type-check, tests) exist and how to run them. If a rule can be enforced by a compiler or linter, let the tool enforce it.
- **Single Source of Truth**: Never maintain duplicate copies of rules in both `AGENTS.md` and `CLAUDE.md`. Maintain `AGENTS.md` as primary and import it in `CLAUDE.md` via `@AGENTS.md` (or symlink).

---

## When to Use

- Initializing agent configuration in a new or existing repository.
- An existing `AGENTS.md` or `CLAUDE.md` is bloated (>150 lines), outdated, or contains conflicting rules.
- Transitioning monolithic documentation into a progressive disclosure structure (`.agents/docs/`).
- The project architecture underwent major changes (e.g., framework migrations, monolith-to-microservice/split backend) and agent behavior is drifting.

---

## The 4-Phase Workflow

```
1. AUDIT & DISCOVERY ──► 2. PLAN & ALIGN ──► 3. MODULAR DOCS ──► 4. BRIEF & SYNC
   • Inspect codebase       • Gap analysis       • Write .agents/     • Write AGENTS.md
   • Read current docs      • Propose topics       docs/*.md (<100L)    (<150 lines)
   • Map actual architecture• User approval      • Diagrams/contracts • Import in CLAUDE.md
```

### Phase 1: Audit & Codebase Discovery

Never write documentation based on assumptions or outdated files. Investigate the live codebase first:

1. **Stack & Runtimes**: Check root and subfolder `package.json`, `bun.lock`, `Cargo.toml`, `pyproject.toml`, etc. Identify exact major versions and runtime engines (Node, Bun, Deno, Python, Go).
2. **Directory Topology**: Identify if the project is a monorepo, a frontend-backend split (e.g., Next.js frontend + standalone Hono/Express backend), or a unified monolith.
3. **Authentication & Session**: Find auth cookies, JWT signing, middleware guards, and session decryption (`jose`, `bcrypt`, passport, etc.).
4. **Data Layer & Mutations**: Identify ORMs (Drizzle, Prisma, TypeORM, SQLAlchemy), DB driver, mutation lifecycle, transactions, and validation boundaries (Zod, Valibot, Pydantic).
5. **Storage & Assets**: Find how file uploads work (direct-to-S3/R2 presigned URLs vs server proxies).
6. **Existing Documentation Audit**: Read existing `AGENTS.md`, `CLAUDE.md`, `README.md`, and `docs/`. Highlight obsolete rules (e.g., referencing Prisma when the repo migrated to Drizzle).

### Phase 2: Plan & Topic Decomposition

Formulate a structured plan before making file edits:
1. **Identify the Core Topics** to extract into `.agents/docs/`:
   - `AUTH.md`: Dual-cookie/token models, roles, session encryption, timing-safe checks, route guards.
   - `FRONTEND_ARCHITECTURE.md`: Routing structure, network client, data-fetching hooks (SWR/React Query), service objects, UI primitives.
   - `BACKEND_ARCHITECTURE.md`: Server runtime, routing framework, ORM/query builder, response envelope standard (`ok`/`fail`), error handler, background jobs, realtime (SSE/WebSockets).
   - `CRUD_RULES.md`: Mandatory mutation lifecycle (e.g., 7-step sequence), entity ID conventions (integer vs UUID), active filtering flags (`isActive`), asset pointer pairs (`*Url`, `*FileId`).
   - `CACHING.md`: Client caching, server LRU/Redis caching, tag/key invalidation, cross-module invalidation maps.
   - *(Optional project-specific docs: `STORAGE.md`, `BACKGROUND_JOBS.md`, `TESTING.md`).*
2. **Review Instruction Budget**: Verify that moving these details to `.agents/docs/` leaves only essential, universal truths for `AGENTS.md`.
3. **Present Plan to User**: List the files to create/modify and request confirmation if required by project protocol.

### Phase 3: Create Modular Documentation (`.agents/docs/`)

Write concise, high-density markdown documents in `.agents/docs/` (or repository doc directory):
- Target length: 40–100 lines per document.
- Use tables for matrix comparisons (routes vs auth levels, models vs upload scopes).
- Include simple text or mermaid flow diagrams for multi-step lifecycles (e.g., login, file uploads, mutations).
- Provide copy-pasteable minimal code patterns rather than lengthy tutorials.
- Highlight common traps specific to that subsystem.

*(See [templates/MODULAR_DOC.template.md](file:///home/sunny/apps/curlbase/.agents/skills/agent-project-brief/templates/MODULAR_DOC.template.md) for structure).*

### Phase 4: Draft `AGENTS.md` and Synchronize `CLAUDE.md`

Write the root `AGENTS.md` strictly adhering to the 8 required sections from the Notion specification:

#### 1. Project Summary (2–4 lines)
- What the project is, tenant model, and core user roles.
- High-level repo topology (e.g., standalone frontend consuming REST API).

#### 2. Stack
- Explicit frameworks, major versions, language strictness, runtime, database, storage, and auth libraries.

#### 3. Essential Commands
- Provide exact terminal commands (no descriptions, pure commands):
  - Dependencies installation (e.g. `bun install`).
  - Dev server startup (`bun dev`, `cd backend && bun run dev`).
  - Type-checking and linting (`bun check`, `bun run lint`).
  - Database migrations and schema syncing (`bun run db:generate`, `bun run db:migrate`).
  - Test runner commands (`bun test`, single-file execution).
  - Production build command (`bun run build`).

#### 4. Repo Map
- Compact directory tree listing key folders with a 1-line description of purpose.

#### 5. Rules (The Always & Never List)
- Must be project-specific truths an agent cannot infer from generic programming knowledge.
- Format: One rule per bullet. Imperative voice. Testable.
- Sub-categories:
  - *Tooling & Types*: Package manager constraints (e.g., "Always use `bun`, never `npm`"), strict TypeScript guidelines, schema editing permissions.
  - *Architecture & APIs*: Response envelope conventions, client data-fetching patterns, environment variable isolation, input validation boundaries, scoping/filtering requirements.
  - *Mutations & Storage*: Mutation lifecycles, transaction safety, direct-to-bucket storage rules, cache invalidation calls.

#### 6. Workflow
- Concrete execution protocol for the agent:
  1. Check relevant topic doc in `.agents/docs/` before modifying code.
  2. Implement targeted, minimal changes matching existing conventions.
  3. Run typecheck and lint tools (`bun check`).
  4. Fix all errors before reporting completion.

#### 7. Known Traps
- Top 4–8 recurring mistakes agents or developers make in this specific repository (1 line per trap).
- *Examples*: "Do not write Server Actions for CRUD; use `services/api/*`", "Drizzle v1 syntax: use `defineRelations` in `relations.ts`, not v0 per-table relations", "`proxy.ts` runs on Node runtime, do not add Edge runtime".

#### 8. Detailed Documentation Links
- Direct clickable markdown links to `.agents/docs/*.md` and existing domain specs.

#### Tool Interoperability Sync
- If supporting Claude Code, update root `CLAUDE.md`:
  ```markdown
  @AGENTS.md
  ```
  *(Or create a symlink: `ln -s AGENTS.md CLAUDE.md`).* Never maintain two separate copies.

---

## Writing Rules: The Agent Instruction Standard

| Do This | Avoid This | Reason |
| --- | --- | --- |
| "Always use `bun`, never `npm` or `yarn`." | "Please try to use the appropriate package manager." | Vague advice leads to agent guessing. |
| "All entity IDs are integer `number`." | "IDs should preferably be numbers, but strings might be supported." | Ambiguity breeds hallucinated string conversions. |
| "Run `bun check` after every edit." | "Ensure the code is clean and without errors." | Agent needs the exact tool command to run. |
| "Follow 7-step mutation pattern in `.agents/docs/CRUD_RULES.md`." | [Pasting 80 lines of mutation boilerplate in `AGENTS.md`] | Bloats every turn's context window. |
| "Read `process.env` exclusively in `lib/config.ts`." | "Keep configuration organized." | Testable fence preventing scattered env reads. |

---

## Anti-Patterns to Strictly Avoid

1. **The Product Pitch**: Writing paragraphs describing product vision, target market, or marketing copy. Agents execute code; they don't sell products.
2. **Teaching the Stack**: Explaining standard Next.js, React, or TypeScript concepts. The LLM already knows standard framework APIs; only document what is unique or breaking.
3. **Duplicating Linter Errors**: Writing rules like "Always put semicolons" or "Format with 2 spaces" if Prettier/ESLint already enforces them. Mention the tool command instead.
4. **Stale Ghost Instructions**: Keeping rules for libraries or paradigms that have been migrated away (e.g. Prisma rules after migrating to Drizzle).
5. **The Monster Root File**: Allowing `AGENTS.md` to exceed 150 lines. As line count grows, agent instruction compliance degrades exponentially.

---

## Verification & Polish Checklist

- [ ] `AGENTS.md` is strictly under 150 lines (`wc -l AGENTS.md`).
- [ ] All 8 standard sections are present and concise.
- [ ] No conversational fluff, marketing text, or generic programming advice.
- [ ] All commands are copy-pasteable and tested against the live repo.
- [ ] Topic documents in `.agents/docs/` are high-signal, self-contained, and linked.
- [ ] `CLAUDE.md` cleanly imports `@AGENTS.md` without duplicating text.
- [ ] Workspace type-checking (`bun check` / `npm run check`) succeeds with zero errors.
