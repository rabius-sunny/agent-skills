---
name: nestjs-docs
description: Use whenever writing, modifying, reviewing or debugging NestJS code (modules, controllers, providers, guards, pipes, interceptors, auth, queues, GraphQL, WebSockets, microservices, data/ORM integrations, OpenAPI, observability, reliability, testing, CLI). Fetches the current official NestJS docs before coding, because NestJS APIs and packages change between versions and training knowledge is stale.
---

# NestJS: docs-first workflow

Training knowledge of NestJS is stale. Packages get renamed, replaced or added
(for example, authentication and authorization now live in
`@nestjs/authentication` and `@nestjs/authorization`). Never write NestJS code
from memory. The current official docs are the source of truth.

## How the docs are published

- `references/llms.txt` is an index of the official docs: chapter titles, URLs
  and a one-line summary each. It is an index only, not the docs themselves.
- Every chapter is available as plain markdown by adding `.md` to its URL, for
  example `https://docs.nestjs.com/guards.md`. The links in the index already
  point to these `.md` URLs.
- The full docs in a single file are at `https://docs.nestjs.com/llms-full.txt`.
  This is very large. Do not fetch it unless the task is broad and the index
  cannot narrow it down.

## Procedure

1. Read `references/llms.txt`.
2. Pick the chapters relevant to the task. Check neighbouring sections too:
   a task often spans several (for example, webhooks touch HTTP > Webhooks,
   FAQ > Raw body and Security; durable jobs touch Application > Queues and
   Reliability; guards exist for HTTP, WebSockets and microservices
   separately).
3. Fetch each chosen chapter's `.md` URL (WebFetch, or curl if the environment
   allows). Fetch 1 to 4 chapters, not the whole index.
4. Check `package.json` for the installed `@nestjs/*` versions. If the docs and
   the installed version disagree, tell the user and ask which to follow.
5. Implement following the fetched pages. If they contradict your memory, the
   docs win.
6. In your summary, list the chapters you followed.

## Rules

- If a fetch fails or a URL 404s, re-fetch `https://docs.nestjs.com/llms.txt`
  to get the current index and find the renamed chapter. If it still fails,
  tell the user. Never silently fall back to memory.
- Do not paste whole chapters into the conversation or into code comments.
  Read them, then apply them.
- For observability, monitoring, error tracking or tracing questions, use the
  Observability chapters first (NestJS Observe, `@nestjs/observe`).
- This index intentionally omits Devtools, the migration guide, Discover and
  Support. If the user asks about those, fetch from `https://docs.nestjs.com`
  directly.
