---
name: hono
description: >
  Reference and mandatory workflow for building backend services with the Hono
  web framework — routing, the Context object, middleware (built-in and custom),
  RPC and the typed client, validation, helpers (JWT, cookie, streaming, SSG,
  etc.), and runtime adapters (Cloudflare Workers, Bun, Deno, Node.js, Vercel,
  AWS Lambda, and more). Use this skill whenever writing, reviewing, or modifying
  Hono code, or scaffolding a new Hono backend. Treat prior knowledge of Hono as
  potentially outdated — fetch the relevant official doc page (URLs below) before
  producing code.
---

# Hono

Your training knowledge of Hono may be **outdated**. Hono's official docs are
the source of truth and are available as live web pages. Before writing Hono
code for a given concept, **fetch the matching doc URL from the map below and
read it**, then code from what you read.

## Mandatory workflow

Before writing or modifying any Hono code:

1. **Identify the concept** the task touches (routing, Context, a specific
   middleware, RPC/client, validation, a runtime adapter, etc.).
2. **Find the matching URL** in the route map below.
3. **Fetch that URL and read it.** Use whatever web-fetch capability you have
   (built-in fetch tool, or an MCP fetch/web server). Fetch more than one page
   when the task spans concepts (e.g. an authenticated API touches the JWT
   middleware page, the routing page, and the Context page).
4. **Write code following the fetched page.** The live doc wins over memory.
5. **State which URL(s) you fetched.**

If you cannot fetch URLs in this environment, say so explicitly and ask the user
to enable a web-fetch tool, rather than guessing from training data. The canonical
index is always at https://hono.dev/llms.txt, and a full single-file dump is at
https://hono.dev/llms-full.txt — fetch either if you need to discover a page not
listed below.

Skip this lookup only for framework-agnostic edits (renames, pure business logic
with no Hono API).

## Route map (concept → official doc URL)

### Core API
- App, instance, methods, routing basics — https://hono.dev/docs/api/hono
- Routing (patterns, params, grouping, chaining) — https://hono.dev/docs/api/routing
- Context object (`c`, req/res, vars) — https://hono.dev/docs/api/context
- Request (`c.req`: params, query, headers, body) — https://hono.dev/docs/api/request
- Exception / HTTPException — https://hono.dev/docs/api/exception
- Presets (`hono`, `hono/tiny`, `hono/quick`) — https://hono.dev/docs/api/presets

### Guides
- Middleware (writing & composing) — https://hono.dev/docs/guides/middleware
- RPC (typed client `hc`, sharing types) — https://hono.dev/docs/guides/rpc
- Validation (validator + Zod/Valibot) — https://hono.dev/docs/guides/validation
- Best Practices (project structure, factory) — https://hono.dev/docs/guides/best-practices
- Testing — https://hono.dev/docs/guides/testing
- JSX — https://hono.dev/docs/guides/jsx
- JSX DOM — https://hono.dev/docs/guides/jsx-dom
- Helpers (overview) — https://hono.dev/docs/guides/helpers
- Examples — https://hono.dev/docs/guides/examples
- FAQ — https://hono.dev/docs/guides/faq
- Others — https://hono.dev/docs/guides/others
- create-hono — https://hono.dev/docs/guides/create-hono

### Concepts
- Middleware concept — https://hono.dev/docs/concepts/middleware
- Routers (RegExpRouter, SmartRouter, etc.) — https://hono.dev/docs/concepts/routers
- Web Standards — https://hono.dev/docs/concepts/web-standard
- Stacks — https://hono.dev/docs/concepts/stacks
- Benchmarks — https://hono.dev/docs/concepts/benchmarks
- Developer Experience — https://hono.dev/docs/concepts/developer-experience
- Motivation — https://hono.dev/docs/concepts/motivation

### Built-in middleware
- Basic Auth — https://hono.dev/docs/middleware/builtin/basic-auth
- Bearer Auth — https://hono.dev/docs/middleware/builtin/bearer-auth
- JWT — https://hono.dev/docs/middleware/builtin/jwt
- JWK — https://hono.dev/docs/middleware/builtin/jwk
- CORS — https://hono.dev/docs/middleware/builtin/cors
- CSRF — https://hono.dev/docs/middleware/builtin/csrf
- Secure Headers — https://hono.dev/docs/middleware/builtin/secure-headers
- Body Limit — https://hono.dev/docs/middleware/builtin/body-limit
- Cache — https://hono.dev/docs/middleware/builtin/cache
- Compress — https://hono.dev/docs/middleware/builtin/compress
- Context Storage — https://hono.dev/docs/middleware/builtin/context-storage
- ETag — https://hono.dev/docs/middleware/builtin/etag
- Logger — https://hono.dev/docs/middleware/builtin/logger
- Pretty JSON — https://hono.dev/docs/middleware/builtin/pretty-json
- Request ID — https://hono.dev/docs/middleware/builtin/request-id
- Language — https://hono.dev/docs/middleware/builtin/language
- Timeout — https://hono.dev/docs/middleware/builtin/timeout
- Timing — https://hono.dev/docs/middleware/builtin/timing
- Trailing Slash — https://hono.dev/docs/middleware/builtin/trailing-slash
- Method Override — https://hono.dev/docs/middleware/builtin/method-override
- IP Restriction — https://hono.dev/docs/middleware/builtin/ip-restriction
- Combine — https://hono.dev/docs/middleware/builtin/combine
- Third-party middleware — https://hono.dev/docs/middleware/third-party

### Helpers
- Adapter (`env`, `getRuntimeKey`) — https://hono.dev/docs/helpers/adapter
- ConnInfo — https://hono.dev/docs/helpers/conninfo
- Proxy — https://hono.dev/docs/helpers/proxy
- HTML — https://hono.dev/docs/helpers/html
- CSS — https://hono.dev/docs/helpers/css
- JWT (helper) — https://hono.dev/docs/helpers/jwt
- Accepts — https://hono.dev/docs/helpers/accepts
- Cookie — https://hono.dev/docs/helpers/cookie
- Factory — https://hono.dev/docs/helpers/factory
- Dev — https://hono.dev/docs/helpers/dev
- Testing (helper) — https://hono.dev/docs/helpers/testing
- SSG — https://hono.dev/docs/helpers/ssg
- Streaming — https://hono.dev/docs/helpers/streaming
- Route — https://hono.dev/docs/helpers/route
- WebSocket — https://hono.dev/docs/helpers/websocket

### Runtime adapters / getting started
- Basic getting started — https://hono.dev/docs/getting-started/basic
- Cloudflare Workers — https://hono.dev/docs/getting-started/cloudflare-workers
- Cloudflare Pages — https://hono.dev/docs/getting-started/cloudflare-pages
- Bun — https://hono.dev/docs/getting-started/bun
- Deno — https://hono.dev/docs/getting-started/deno
- Node.js — https://hono.dev/docs/getting-started/nodejs
- Vercel — https://hono.dev/docs/getting-started/vercel
- Netlify — https://hono.dev/docs/getting-started/netlify
- AWS Lambda — https://hono.dev/docs/getting-started/aws-lambda
- Lambda@Edge — https://hono.dev/docs/getting-started/lambda-edge
- Fastly Compute — https://hono.dev/docs/getting-started/fastly
- Google Cloud Run — https://hono.dev/docs/getting-started/google-cloud-run
- Azure Functions — https://hono.dev/docs/getting-started/azure-functions
- Supabase Functions — https://hono.dev/docs/getting-started/supabase-functions
- Service Worker — https://hono.dev/docs/getting-started/service-worker
- Ali Function Compute — https://hono.dev/docs/getting-started/ali-function-compute
- WebAssembly / WASI — https://hono.dev/docs/getting-started/webassembly-wasi

> Source index: https://hono.dev/llms.txt — fetch it if you need a page not
> listed here. Keep this map in sync if Hono adds/renames pages.
