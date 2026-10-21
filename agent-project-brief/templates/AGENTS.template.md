# [Project Name] — Agent Project Brief

## 1. Project Summary
[1-2 sentences: what the application does, tenancy model, and core user roles].
[1-2 sentences: repo architecture, e.g. monorepo split into frontend app and standalone REST/gRPC backend].

## 2. Stack
- **Frontend**: [Framework, Version, Language, State/Data fetching, UI/Styling].
- **Backend**: [Runtime, Framework, Version, ORM/DB Client, Database, Storage, Email].
- **Auth & Security**: [Authentication mechanism, session model, hashing, token lifetime].

## 3. Essential Commands

### Frontend (Root)
- `[install command]`: Install dependencies.
- `[dev command]`: Start frontend dev server on `http://localhost:[port]`.
- `[check/typecheck command]`: Type-check frontend.
- `[build command]`: Build production bundle.
- `[lint command]`: Run linter.

### Backend (`cd backend`)
- `[install command]`: Install backend dependencies.
- `[dev command]`: Start backend server on `http://localhost:[port]`.
- `[check/typecheck command]`: Type-check backend.
- `[db generate command]`: Generate schema migrations.
- `[db migrate command]`: Apply migrations against database.
- `[db seed command]`: Seed initial admin/test data.

## 4. Repo Map
```
├── [dir1]/               # [1-line description of purpose]
├── [dir2]/               # [1-line description of purpose]
├── [dir3]/               # [1-line description of purpose]
├── .agents/docs/         # Modular architecture and domain deep-dives
└── backend/              # Standalone backend service
    ├── [backend-dir1]/   # [1-line description of purpose]
    └── [backend-dir2]/   # [1-line description of purpose]
```

## 5. Rules

### Tooling & Types
- [Rule 1: Exact package manager constraint, e.g., "Always use bun, never npm or yarn"].
- [Rule 2: TypeScript strictness, e.g., "No any, no @ts-ignore, no unannotated assertions"].
- [Rule 3: Schema modification boundary, e.g., "Never modify DB/Zod schemas without prior user approval"].
- [Rule 4: Typecheck command trigger, e.g., "Run bun check after every edit and ensure 0 errors"].

### Architecture & APIs
- [Rule 5: Primary key / entity ID standard, e.g., "All entity IDs are integer number; never use UUID/string"].
- [Rule 6: Data fetching boundary, e.g., "Frontend queries data only via services/api/*Service.ts"].
- [Rule 7: API envelope standard, e.g., "Backend returns standard { data: T } or { error: string }"].
- [Rule 8: Config isolation, e.g., "Read process.env exclusively in config.ts"].
- [Rule 9: Input validation, e.g., "Validate all route inputs at boundary with Zod"].
- [Rule 10: Visibility / Scoping, e.g., "Queries must explicitly filter by isActive: true unless auditing deactivated"].

### Mutations & Storage
- [Rule 11: Mutation lifecycle pattern, e.g., "Follow the 7-step mutation pattern in CRUD_RULES.md"].
- [Rule 12: Transaction boundary, e.g., "Never call notifications or external email inside DB transactions"].
- [Rule 13: File upload flow, e.g., "Upload directly to S3/R2 presigned URLs; store pointer pairs (*Url, *FileId)"].
- [Rule 14: Cache invalidation trigger, e.g., "Call invalidateModule(ns) after every DB mutation"].

## 6. Workflow
1. Check the relevant topic doc in `.agents/docs/` before making changes.
2. Implement targeted, minimal changes matching established file patterns.
3. Run `[typecheck command]` (and backend check if backend code was modified).
4. Fix all type and lint errors before reporting completion.

## 7. Known Traps
- **[Trap 1 Name]**: [1-line clear directive on what NOT to do and what to do instead].
- **[Trap 2 Name]**: [1-line clear directive on what NOT to do and what to do instead].
- **[Trap 3 Name]**: [1-line clear directive on what NOT to do and what to do instead].
- **[Trap 4 Name]**: [1-line clear directive on what NOT to do and what to do instead].

## 8. Detailed Documentation Links
- [Authentication & Roles](file:///.agents/docs/AUTH.md)
- [Frontend Architecture & Services](file:///.agents/docs/FRONTEND_ARCHITECTURE.md)
- [Backend Architecture & Data](file:///.agents/docs/BACKEND_ARCHITECTURE.md)
- [CRUD & Mutation Lifecycle](file:///.agents/docs/CRUD_RULES.md)
- [Caching & Invalidation](file:///.agents/docs/CACHING.md)
- [Existing Spec / Guide](file:///docs/[spec-name].md)
