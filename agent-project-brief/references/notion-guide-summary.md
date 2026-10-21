# Summary: AGENTS.md & CLAUDE.md — Writing the Agent's Project Brief

> Source Reference: *AGENTS.md & CLAUDE.md: Writing the Agent’s Project Brief* (Notion)

## Core Principles

1. **Instruction Budget**: The root instruction file loads into context on every single turn. Aim for <150 lines. Every line that doesn't actively change agent behavior must be removed.
2. **Context vs. Enforcement**: The brief is context; it is not enforcement. Enforce rules with automated tools (lint, typecheck, tests, CI). Use the brief to tell the agent that the tool exists.
3. **The File is a Map**: Point to detailed docs (`.agents/docs/`, `/docs/`); do not embed them in the root brief.
4. **Single Source of Truth**:
   - `AGENTS.md` is the universal open standard.
   - `CLAUDE.md` should import it with `@AGENTS.md` on its own line (or via symlink `ln -s AGENTS.md CLAUDE.md`). Never duplicate text between both files.

## Required Sections

1. **Project summary (2–4 lines)**: What the project is and main parts of the repo. No marketing fluff.
2. **Stack**: Languages, frameworks, runtime, database, and major versions (especially when breaking changes exist).
3. **Commands**: Exact copy-pasteable commands for install, dev server, tests, lint, typecheck, migrations, and build.
4. **Repo map**: Short tree of key folders with 1 line each.
5. **Rules (the "always" and "never" list)**: Project-specific constraints that an agent cannot infer from the code. One rule per line, imperative voice, testable.
6. **Workflow**: Step-by-step protocol: read spec/docs → minimal edits → run checks → verify before declaring done.
7. **Known traps**: Specific mistakes agents make repeatedly in this repo. One line per trap.
8. **Links**: Clickable links to architecture docs, specs, and ADRs.

## Items to Exclude

- General programming advice ("write clean code").
- Rules that a tool already enforces with clear compiler/linter error messages.
- Long historical explanations or rationale (put in ADRs).
- Product pitches, roadmaps, or sales text.
- Secrets, tokens, internal credentials.
- Volatile information (sprint status, active tickets).
- Full code implementations (point to an existing repo file instead).
