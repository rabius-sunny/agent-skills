---
name: drizzle-latest-docs
description: Write Drizzle ORM code (schemas, relations, queries, migrations, drizzle-kit config, validators) from the latest official docs at orm.drizzle.team instead of stale training knowledge. Drizzle v1 changed relations, the query API, migrations and imports. Use this skill whenever the task touches Drizzle in any way — drizzle-orm or drizzle-kit imports, pgTable/sqliteTable schemas, db.select/insert/update/delete, db.query relational queries, defineRelations, drizzle.config.ts, migrations, drizzle-zod or validators, seeding, or setting up Drizzle in a new project — even for a small edit or when the user doesn't mention docs.
---

# Drizzle ORM: docs first, memory second

Drizzle v1 rewrote large parts of the API: relations, the relational query builder, the migrations folder, validator imports, casing and drizzle-kit behaviour. Most training data is v0, so code written from memory often compiles against nothing, uses removed APIs, or quietly falls back to old patterns. The official docs are the source of truth. Fetch them before writing Drizzle code, and prefer them over memory whenever the two disagree.

Target: **new projects on Drizzle v1** (PostgreSQL and SQLite/libSQL primarily).

## Workflow

### 1. Check the installed version

Read `package.json` for `drizzle-orm`, `drizzle-kit` and any legacy validator packages (`drizzle-zod`, `drizzle-valibot`, `drizzle-typebox`, `drizzle-arktype`).

- **New project / not installed yet:** fetch `https://orm.drizzle.team/docs/upgrade-v1` first. While v1 is a release candidate, install with the `@rc` tag (`bun add drizzle-orm@rc` / `bun add -D drizzle-kit@rc`, or the npm/pnpm equivalent). If the page says v1 is stable, use the normal latest tag instead.
- **Already on v1:** continue.
- **On 0.x:** this skill targets v1. Tell the user the project is on 0.x and ask before upgrading. Never mix v0 and v1 syntax in one codebase.

### 2. Map the task to doc pages

Use the topic map below to pick the one to three pages that cover what you're about to write. Schema plus relations plus a query is a common trio.

### 3. Fetch each page once per session, before writing that kind of code

- Fetch each relevant page with the web fetch tool (WebFetch, `fetch`, browser, or whatever this agent has). Reuse what you learned for the rest of the session; don't re-fetch the same page for every edit.
- Re-fetch when a type error or runtime error suggests the API differs from what you used, or when the user asks you to.
- Ask the fetch for **exact code snippets, import paths and function signatures**, not a summary. A paraphrased summary loses the details that matter, such as option names and import subpaths. A good prompt: *"Return every code example verbatim with its import lines, plus all option names and their types."*
- Dialects: the default pages show PostgreSQL. For SQLite or libSQL, prefer the `/docs/sqlite/...` variant when one exists, and fetch the connect page for the exact driver (libSQL/Turso, D1, Bun SQLite, etc.).
- If a URL returns 404 or looks moved, fetch `https://orm.drizzle.team/llms.txt` (the official index of all pages) to find the current URL.

### 4. Write the code from the docs

- Follow the fetched docs. If they contradict memory or the snapshot table below, **the docs win**.
- Before finishing, check your code against the stale-pattern table below. Those are the mistakes that keep coming back.
- Write efficient code by default: select only the columns you need, use prepared statements on hot paths, use the batch API where the driver supports it (libSQL, D1, Neon HTTP), use transactions for multi-step writes, and add indexes for the columns you filter and join on. Fetch `/docs/perf-queries` (and `/docs/perf-serverless` for edge or serverless) when performance matters.

### 5. Verify

Run the project's type check (`tsc --noEmit`, `bun run typecheck`, etc.). After schema changes, run `drizzle-kit generate` and `drizzle-kit check` instead of hand-writing migration SQL, unless the user wants a custom migration (see `/docs/kit-custom-migrations`).

### 6. Report sources

End with one line naming the doc pages you used, e.g. `Docs: /docs/relations, /docs/rqb`. That way the user can see the code came from current docs.

### No web access?

Say so plainly. Fall back to the snapshot table below, and flag the code as **not verified against the current docs**. Don't pretend you checked.

## Topic → doc page map

Base URL: `https://orm.drizzle.team`

| Writing… | Fetch |
|---|---|
| New project setup, drivers | `/docs/get-started`, `/docs/get-started-postgresql` or `/docs/sqlite/get-started-sqlite`, plus the provider page (`/docs/connect-neon`, `/docs/connect-supabase`, `/docs/connect-bun-sql`, `/docs/connect-pglite`, …) |
| Upgrading / version questions | `/docs/upgrade-v1`, `/docs/v0-v1-changes`, `/docs/latest-releases` |
| Tables, columns | `/docs/sql-schema-declaration`, `/docs/column-types` (for SQLite, `/docs/sqlite/...` variants) |
| Indexes, constraints, FKs | `/docs/indexes-constraints` |
| Enums, sequences, views, pg schemas | `/docs/column-types`, `/docs/sequences`, `/docs/views`, `/docs/schemas` |
| Relations | `/docs/relations` (and `/docs/relations-v1-v2` when converting old code) |
| Relational queries (`db.query`) | `/docs/rqb` |
| select / insert / update / delete | `/docs/select`, `/docs/insert`, `/docs/update`, `/docs/delete` |
| Filters, operators | `/docs/operators` |
| Joins, aliases, raw SQL | `/docs/joins`, `/docs/aliases`, `/docs/sql` |
| Transactions, batch | `/docs/transactions`, `/docs/batch-api` |
| Dynamic / conditional queries | `/docs/dynamic-query-building` |
| Utilities (`getColumns`, count, etc.) | `/docs/query-utils`, `/docs/goodies` |
| Generated columns | `/docs/generated-columns` |
| RLS (Postgres, Supabase, Neon) | `/docs/rls` |
| Custom types, codecs, JIT mappers | `/docs/custom-types`, `/docs/codecs`, `/docs/jit-mappers` |
| Caching, read replicas | `/docs/cache`, `/docs/read-replicas` |
| `drizzle.config.ts` | `/docs/drizzle-config-file` |
| Migrations workflow | `/docs/migrations`, `/docs/kit-overview`, plus the command page: `/docs/drizzle-kit-generate`, `-migrate`, `-push`, `-pull`, `-check`, `-up`, `-export`, `-studio` |
| Team migrations, custom SQL migrations | `/docs/kit-migrations-for-teams`, `/docs/kit-custom-migrations` |
| Validation schemas | `/docs/zod`, `/docs/valibot`, `/docs/typebox`, `/docs/arktype`, `/docs/effect-schema` |
| Seeding | `/docs/seed-overview`, `/docs/seed-functions` |
| Performance | `/docs/perf-queries`, `/docs/perf-serverless` |
| Something surprising | `/docs/gotchas` |

## Stale patterns to catch (snapshot, checked 2026-10-02)

This is a quick self-check, not a replacement for fetching. If a fetched page says otherwise, follow the page.

| Stale (v0 / memory) | Current (v1) |
|---|---|
| `relations(users, ({ one, many }) => …)` per table | One `defineRelations(schema, (r) => ({ users: { posts: r.many.posts() }, … }))`, imported from `drizzle-orm` |
| `one(users, { fields: [...], references: [...], relationName })` | `r.one.users({ from: r.posts.authorId, to: r.users.id, alias })`. `from`/`to` accept a single column or an array |
| Many-to-many via the junction table in `with` plus manual mapping | `r.many.groups({ from: r.users.id.through(r.usersToGroups.userId), to: r.groups.id.through(r.usersToGroups.groupId) })`, then `with: { groups: true }` |
| `drizzle(url, { schema })`, `mode: 'planetscale'` | `drizzle(url, { relations })`. MySQL `mode` is gone |
| `where: (t, { eq }) => eq(t.id, 1)` in `db.query` | Object filters: `where: { id: 1 }`, `{ age: { between: [25, 35] } }`, `AND` / `OR` / `NOT`, `RAW: (t) => sql\`…\`` |
| `orderBy: (t, { asc }) => [asc(t.id)]` in `db.query` | `orderBy: { id: 'asc' }` |
| `db._query`, `Relations` type, `getOperators`, `createOne` / `createMany` | Removed or legacy. Don't use in new code |
| `drizzle({ casing: 'snake_case' })` | Table-level: `snakeCase.table('users', {...})` / `camelCase.table(...)` from the dialect core (e.g. `drizzle-orm/pg-core`); also `.view`, `.schema` |
| `import { createInsertSchema } from 'drizzle-zod'` | `drizzle-orm/zod` (also `drizzle-orm/valibot`, `/typebox`, `/arktype`, `/effect-schema`). Fetch `/docs/zod` for the exact exports |
| `getTableColumns(table)` | `getColumns(table)` |
| `.array().array()` | `.array('[][]')` |
| `pgTable(...).enableRLS()` | `pgTable.withRLS('name', {...})` |
| `.generatedAlwaysAs('expr')` | `.generatedAlwaysAs(sql\`expr\`)` or `() => sql\`…\`` |
| `meta/_journal.json`, flat migration files | One folder per migration (SQL + snapshot). Run `drizzle-kit up` to convert old folders |
| `drizzle-kit drop` | Removed. Delete the migration folder, then regenerate |
| `drizzle-kit push --strict` | Strict is the default. `--force` skips confirmation, `--explain` previews the SQL |
| `schemaFilter: ['public']` needed | push/pull manage **all** schemas by default. `schemaFilter` supports globs |
| `.prepare('name')` name required | Name is optional |

v1 additions worth using when they fit: `.comment('tag')` SQL comments, `column.as('alias')` in select, `jit: true` mappers, codecs, `drizzle-kit pull --init`, `drizzle-kit check` for branch migration conflicts, predefined `where` filters on relations, and `optional: false` on relations to make related entities non-nullable at the type level.
