---
name: api-service-layer
description: Layered HTTP data-fetching architecture for a frontend app - axios client setup, a typed request wrapper, an SWR-based read hook, and centralized service objects for reads and mutations. Use whenever adding a new API integration, wiring up a new backend resource/endpoint, adding a data-fetching hook, or creating a CRUD service - even if the user just says "add an endpoint," "call this API," or "hook this component up to the backend" without mentioning architecture.
user-invocable: false
---

# HTTP Data-Fetching Architecture

A four-layer pattern for talking to a backend API: each layer has exactly one
job, so a change in one (auth scheme, caching strategy, response shape) never
leaks into the others.

```
axios instance  →  request wrapper  →  read hook (SWR)  ↘
     (transport)      (typing)         (caching reads)    service object
                                                          (domain API surface)
```

## 1. Axios instance - transport and cross-cutting concerns

One `axios.create(...)` instance per app, configured once, imported everywhere
else. This is the only place that knows about the base URL, timeouts, and how
auth/errors are handled - so switching auth strategy or adding global retry
logic is a one-file change.

```ts
// services/api/client.ts
import axios from 'axios'
import Cookies from 'js-cookie'

const client = axios.create({
  baseURL: process.env.NEXT_PUBLIC_API_ROOT,
  timeout: 30000,
  headers: { 'Content-Type': 'application/json' }
})

// Request interceptor: attach auth on every outgoing call
client.interceptors.request.use((config) => {
  const token = Cookies.get('authToken')
  if (token) config.headers.Authorization = `Bearer ${token}`
  return config
})

// Response interceptor: centralize error side-effects (toast, logout-on-401, etc.)
client.interceptors.response.use(
  (response) => response,
  (error) => {
    handleApiError(error)
    return Promise.reject(error)
  }
)

export default client
```

Nothing outside this file should import the raw `axios` package or read the
token/base URL directly - that's how the concern stays centralized.

## 2. Request wrapper - typed, response-unwrapped methods

Axios resolves with a full `AxiosResponse` (status, headers, config, `data`).
Call sites almost never want that whole envelope - they want the payload,
typed. Wrap each verb once so every call site gets `Promise<T>` instead of
`Promise<AxiosResponse<T>>`:

```ts
// services/network/http.ts
import type { AxiosRequestConfig, AxiosResponse } from 'axios'
import client from '../api/client'

const unwrap = <T>(response: AxiosResponse<T>) => response.data

const requests = {
  get: <T = any>(url: string, config?: AxiosRequestConfig): Promise<T> =>
    client.get<T>(url, config).then(unwrap),

  post: <T = any>(url: string, body: object, config?: AxiosRequestConfig): Promise<T> =>
    client.post<T>(url, body, config).then(unwrap),

  patch: <T = any>(url: string, body: object, config?: AxiosRequestConfig): Promise<T> =>
    client.patch<T>(url, body, config).then(unwrap),

  put: <T = any>(url: string, body: object, config?: AxiosRequestConfig): Promise<T> =>
    client.put<T>(url, body, config).then(unwrap),

  delete: <T = any>(url: string, config?: AxiosRequestConfig): Promise<T> =>
    client.delete<T>(url, config).then(unwrap)
}

export default requests
```

Every later layer (the read hook, every service method) calls through
`requests`, never through `client`/`axios` directly. This is the seam where
you'd swap HTTP libraries entirely without touching a single service file.

## 3. Read hook - SWR keyed by URL

For **reads** (GET), wrap a caching data-fetching library (e.g. SWR or React
Query) in one hook so every component gets the same loading/error/revalidate
shape and doesn't need to know the caching library's API.

The key trick: the hook's "key" *is* the URL string, and `requests.get` is the
fetcher. This means any endpoint string is automatically cacheable, shareable,
and revalidatable - components don't call `requests.get` themselves.

```ts
'use client'

import requests from '@/services/network/http'
import useSWR, { KeyedMutator } from 'swr'

const fetcher = (url: string) => requests.get(url)

type UseAsyncReturn<T> = {
  data: T | undefined
  error: any
  loading: boolean
  mutate: KeyedMutator<T>
  validating: boolean
}

export default function useAsync<T>(
  // A plain string key always fetches. A function key lets the caller make
  // the fetch conditional/dependent - return null to skip it (e.g. while a
  // required id or auth token isn't available yet).
  url: string | (() => string | null) | null,
  revalidateIfStale = true,
  revalidateOnFocus = false,
  revalidateOnReconnect = true
): UseAsyncReturn<T> {
  const key = typeof url === 'function' ? url() : url

  const { data, error, isLoading, mutate, isValidating } = useSWR<T>(key, fetcher, {
    revalidateIfStale,
    revalidateOnFocus,
    revalidateOnReconnect,
    keepPreviousData: true, // avoids UI flicker to empty state on refetch/pagination
    shouldRetryOnError: false // let error UI + explicit retry own retries, not the cache lib
  })

  return { data: data as T, error, loading: isLoading, mutate, validating: isValidating }
}
```

Use the function-key form for dependent fetches:

```ts
useAsync<Profile>(() => (userId ? `/users/${userId}` : null))
```

## 4. Service object - the domain's API surface

One object per backend resource/domain. This is the *only* file components
should import to talk to that resource - never `requests` or endpoint strings
directly. Splitting **reads** from **mutations** inside the same object keeps
the calling convention obvious:

- **GET methods return an endpoint string** (not a promise). They're key
  builders for `useAsync`, not fetchers - the hook decides when/whether to
  call them, which is what makes caching and revalidation possible.
- **Mutation methods (create/update/delete) call `requests` and return a
  promise directly.** Mutations are one-shot imperative actions triggered by
  user events, not cached reads, so there's nothing to key or cache.

```ts
// services/api/<resource>Service.ts
import requests from '@/services/network/http'
import { buildQueryString } from '@/utils/buildQueryString'

interface ListQuery {
  page?: string
  limit?: string
  search?: string
}

export const resourceService = {
  // ── Reads (endpoint builders, consumed via useAsync) ──────────────────
  getAll: (params?: ListQuery) => `/resources${buildQueryString(params)}`,
  getById: (id: string) => `/resources/${id}`,

  // ── Mutations (promises, called directly from event handlers) ─────────
  create: (data: CreatePayload) => requests.post<CreateResponse>('/resources', data),
  update: (id: string, data: UpdatePayload) =>
    requests.put<UpdateResponse>(`/resources/${id}`, data),
  delete: (id: string) => requests.delete<DeleteResponse>(`/resources/${id}`)
}
```

Wired into a component:

```ts
// Read - pass the endpoint builder's result straight to useAsync
const { data, loading, mutate } = useAsync<Resource[]>(resourceService.getAll({ page: '1' }))

// Mutation - call directly, then revalidate the read
async function handleDelete(id: string) {
  await resourceService.delete(id)
  mutate()
}
```

### Query string helper

Service `getAll`-style methods take a params object and need a consistent way
to turn it into a query string, dropping empty/undefined values so URLs stay
stable (and thus cache keys stay stable):

```ts
export const buildQueryString = (
  params?: Record<string, string | string[] | number | boolean | undefined | null>
) => {
  if (!params) return ''
  const qs = Object.entries(params)
    .filter(([, v]) => v !== undefined && v !== null && v !== '')
    .map(([k, v]) => `${encodeURIComponent(k)}=${encodeURIComponent(String(v))}`)
    .join('&')
  return qs ? `?${qs}` : ''
}
```

## Adding a new resource - checklist

1. Confirm the axios instance / request wrapper / read hook already exist in
   the project (they're app-wide singletons - write them once, not per
   resource).
2. Create `<resource>Service.ts`: GET methods return endpoint strings, define
   the query-param interface if the list endpoint takes filters.
3. Add mutation methods for whatever the backend supports (create/update/
   delete/etc.), each typed with its own request/response shape.
4. In components: call the read hook with a `getAll`/`getById` string for
   data, call mutation methods directly from event handlers, and call the
   hook's `mutate()` afterward to revalidate.

### File/folder layout

The four layers map to fixed, app-wide files; only the service file is
per-resource:

```
src/
├── services/
│   ├── api/
│   │   └── client.ts            # 1. axios instance (singleton)
│   │   └── <resource>Service.ts # 4. one file per backend resource
│   └── network/
│       └── http.ts              # 2. typed request wrapper (singleton)
├── hooks/
│   └── useAsync.ts              # 3. SWR read hook (singleton)
└── utils/
    └── buildQueryString.ts      # query-param helper (singleton)
```

Layers 1-3 and the query-string helper exist once for the whole app - don't
duplicate them per resource. Adding a new resource almost always means adding
exactly one new file: `services/api/<resource>Service.ts`.

## Why this split (and when to deviate)

- **Reads return strings, mutations return promises** - this asymmetry looks
  inconsistent at first glance, but it maps to a real difference: reads are
  declarative ("this component depends on this cache key") while mutations
  are imperative ("run this now"). Don't "fix" the asymmetry by making reads
  return promises too - that would require re-implementing SWR's
  loading/error/cache/revalidate state by hand in every component.
- **One service object per resource, not one per endpoint** - keeps related
  reads and mutations discoverable together and gives components a single
  import for everything about that resource.
- If a project already uses a different data layer (React Query, RTK Query,
  tRPC, a GraphQL client), keep the same layering *concept* - transport
  config, response typing, cached-read hook, domain service object - but
  don't force this exact SWR/axios shape onto it.
