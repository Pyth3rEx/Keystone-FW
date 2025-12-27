# Contributing to Keystone Framework

Thanks for your interest in contributing to **Keystone Framework**. This document defines the rules for:
- how we write code
- how we structure modules/resources
- how we commit + open PRs
- how Keystone stays secure while accepting third-party code

> Keystone is a “don’t trust the user / don’t trust third-party devs” framework. The core must assume modules can be sloppy, outdated, or malicious. The framework enforces safety and consistency.

---

## Table of contents

- [Principles](#principles)
- [Repo structure](#repo-structure)
- [Tech stack](#tech-stack)
- [Coding standards](#coding-standards)
- [Module design rules](#module-design-rules)
- [Database and migrations](#database-and-migrations)
- [Configuration rules](#configuration-rules)
- [API and contracts](#api-and-contracts)
- [Security requirements](#security-requirements)
- [Testing requirements](#testing-requirements)
- [Logging](#logging)
- [Git workflow](#git-workflow)
- [Commit messages](#commit-messages)
- [Pull request checklist](#pull-request-checklist)

---

## Principles

1. **Core owns truth. Modules declare intent.**
   - Modules declare what they *need* (db schema, config keys, permissions, runtime needs).
   - Keystone decides what is installed/enabled, and validates everything.

2. **Determinism over cleverness.**
   - Same input config → same output behavior.
   - Avoid hidden side effects and “magic” runtime mutation.

3. **Strict boundaries.**
   - Modules can’t patch core internals.
   - Modules interact through stable public APIs/contracts only.

4. **Secure by default.**
   - Everything goes through Keystone. The framework is responsible for the validation and sanitation of everything.
   - Modules can easely be **disabled on the framework level**.
   - The module cannot self-enable or auto-load itself.

5. **Good DX, strong enforcement.**
   - We prefer automation (lint/test/validation) to docs-only rules.

---

## Repo structure

```text
Keystone-FW/
│
├─ .github/
│   ├─ workflows/
│   │   ├─ ci.yml                 # build, lint, typecheck
│   │   ├─ release.yml            # build + package release bundle
│   │   └─ updater.yml            # optional auto-release metadata
│   └─ ISSUE_TEMPLATE/
│
├─ packages/
│   ├─ shared/                     # STABLE CONTRACTS (SACRED)
│   │   ├─ events/
│   │   ├─ rpc/
│   │   ├─ permissions.ts
│   │   ├─ config/
│   │   ├─ schemas/
│   │   └─ errors.ts
│   │
│   └─ tooling/
│       ├─ updater/
│       │   ├─ update.ts           # download, verify, swap, rollback
│       │   └─ checksum.ts
│       ├─ migrate/
│       ├─ seed/
│       └─ cli.ts                  # pnpm keystone <cmd>
│
├─ docs/
│   ├─ architecture.md
│   ├─ module-spec.md
│   ├─ lifecycle.md
│   ├─ config.md
│   ├─ updater.md
│   └─ philosophy.md
│
├─ resources/
│   └─ [keystone]/
│     └─ keystone/          # THE ONLY FiveM RESOURCE
│         ├─ fxmanifest.lua
│         │
│         ├─ bootstrap/
│         │   ├─ server.ts       # FiveM entrypoint (server)
│         │   └─ client.ts       # FiveM entrypoint (client)
│         │
│         ├─ config/                        # OPERATOR-OWNED (never overwritten)
│         │   ├─ general/                   # global framework config
│         │   │   └─ keystone.yaml
│         │   ├─ modules/                   # all module configs
│         │   │   ├─ identity.yaml
│         │   │   ├─ accounts.yaml
│         │   │   └─ inventory.yaml
│         │   ├─ secrets.env                # ignored by git
│         │   └─ keystone.lock.json         # pinned versions (written by updater)
│         │
│         ├─ core/
│         │   ├─ container.ts    # DI-lite
│         │   ├─ events.ts       # typed event bus
│         │   ├─ rpc.ts          # RPC layer
│         │   ├─ permissions.ts
│         │   ├─ commands.ts
│         │   ├─ notifications.ts
│         │   ├─ logging.ts
│         │   ├─ config.ts       # schema validation + resolution
│         │   └─ clock.ts
│         │
│         ├─ db/
│         │   ├─ db.ts
│         │   ├─ tx.ts
│         │   ├─ repository.ts
│         │   └─ migrations/
│         │
│         ├─ modules/            # Keystone modules (plugins)
│         │   └─ keystone-example/     # DOCUMENTATION EXAMPLE
│         │       ├─ module.json
│         │       ├─ server.ts
│         │       ├─ client.ts
│         │       └─ README.md
│         │
│         ├─ runtime/
│         │   ├─ moduleLoader.ts # discovery, dependency graph, lifecycle
│         │   ├─ dependency.ts   # topo sort, cycle detection
│         │   ├─ registry.ts     # loaded modules, states
│         │   └─ disposables.ts  # cleanup enforcement
│         │
│         ├─ ui/                 # core UI shell (React/NUI)
│         │   ├─ index.html
│         │   ├─ src/
│         │   └─ dist/
│         │
│         └─ dist/
│             ├─ server.js       # compiled bundle
│             ├─ client.js
│             └─ modules/        # compiled plugin bundles
│
├─ scripts/
│   ├─ build.ts
│   ├─ dev.ts
│   └─ package-release.ts
│
├─ .gitignore
├─ package.json
├─ pnpm-lock.yaml
├─ tsconfig.base.json
├─ tsconfig.server.json
├─ tsconfig.client.json
└─ README.md

````

---

## Tech stack

- **TypeScript** everywhere
- **Node.js** LTS
- **PostgreSQL** for persistence
- Tooling:
  - ESLint
  - Prettier
  - TypeScript strict mode
  - (optional but recommended) Vitest for unit tests

---

## Coding standards

### TypeScript
- `strict: true` (no exceptions)
- No `any` unless:
  - you are interfacing with an external unknown payload, and
  - you validate it (zod/valibot/io-ts) and then narrow the type
- Prefer **explicit return types** for exported functions
- Prefer **named exports** in libraries; default exports only for React/components (if any)

### Style
- Prettier formatting is non-negotiable
- Keep functions small; avoid deep nesting
- Prefer early returns over multiple `else` branches
- No clever metaprogramming in core (decorators, runtime patching)

### Errors
- Throw typed errors (custom error classes) for domain failures
- Do not throw raw strings

---

## Module design rules

### What a module is
A module is a self-contained feature package with:
- a `module.json` manifest
- optional runtime code (server/client)
- declared configs and database needs

### Modules must be declarative
A module must **never**:
- write into Keystone config directly
- create tables “by itself” at runtime
- patch/monkeypatch core
- spawn network calls on install without explicit permission and config

A module must **always**:
- send inputs through the Keystone core for validation before use
- make requests for BD changes to Keystone core

### Third-party modules are treated as untrusted
Keystone will:
- validate manifests strictly
- refuse unknown/unsafe config keys
- run migrations through the framework only
- gate runtime startup based on Keystone’s toggle list

---

## Database and migrations

Modules must declare DB needs so Keystone can:
- check if schema exists
- create/update it on first launch
- run upgrades safely
- refuse incompatible migrations

### Rules
- **All schema changes are migrations.** No “auto create table in code.”
- Migrations must be:
  - ordered
  - reversible when feasible
  - idempotent where possible
- A module must define:
  - schema name (or table prefix)
  - list of migration files
  - minimal compatible version (if needed)

### Conventions
- Use one schema per module when possible (`keystone_<module>`), or a strict prefix
- Every table must have:
  - `id` (UUID preferred)
  - `created_at`, `updated_at`
  - indexed foreign keys

---

## Configuration rules

### Keystone owns enablement
- Keystone maintains a **toggle list** file (framework-managed), listing installed modules and whether they’re enabled.
- If a module folder is removed, Keystone removes its entry.
- A module manifest must not contain `enabledByDefault`, `autoload`, or similar “trust me” flags.

### Config declaration
Modules must declare their configuration surface:
- key name
- type
- default values
- required/optional
- validation constraints
- secret/non-secret classification

Keystone will:
- validate config at boot
- refuse boot if required config is missing for enabled modules
- keep secrets out of logs

---

## API and contracts

### Backwards compatibility
- Public contracts must be versioned.
- Breaking changes require:
  - a version bump
  - migration notes
  - deprecation window where feasible

### No direct imports across boundaries
- Modules should import from:
  - `@keystone/core`
  - `@keystone/contracts`
  - `@keystone/sdk`
- Modules should not import internal paths like:
  - `@keystone/core/src/...`

---

## Security requirements

Minimum bar for any module PR:

1. **No dynamic code loading**
   - No `eval`, `Function()`, runtime TS compilation, etc.

2. **No silent networking**
   - Any outbound network use must be explicit, documented, and off by default

3. **Input validation**
   - Validate external payloads (HTTP, events, user input) via Keystone before use

4. **Least privilege**
   - Modules request permissions; Keystone enforces them

5. **No secrets in repo**
   - Never commit tokens, private keys, service accounts
   - Use `.env.example` and documentation instead

---

## Testing requirements

- Any new feature or bugfix should include tests.
- Minimum expected:
  - unit tests for core logic
  - contract tests for public APIs/events (when relevant)

Tests must be:
- deterministic
- not depend on live services
- not depend on wall-clock time (use fakes)

---

## Logging

- Use Keystone’s logger
- Log levels:
  - `debug` for noisy dev details
  - `info` for main lifecycle events
  - `warn` for recoverable issues
  - `error` for failures
- Never log secrets, credentials, or raw PII unless explicitly required and redacted

---

## Git workflow

### Branching
- `main` is protected
- Feature branches:
  - `feat/<topic>`
  - `fix/<topic>`
  - `chore/<topic>`
  - `docs/<topic>`

### PR size
- Keep PRs small.
- If your PR touches:
  - manifest format changes
  - contracts changes
  - database migrations
  Then expect stricter review.

---

## Commit messages

Use Conventional Commits:

- `feat: ...`
- `fix: ...`
- `docs: ...`
- `refactor: ...`
- `test: ...`
- `chore: ...`

Examples:
- `feat: add module toggle list management`
- `fix: validate module config types before boot`
- `docs: clarify migration requirements`

---

## Pull request checklist

Before opening a PR, ensure:

- [ ] Code builds locally
- [ ] Lint passes
- [ ] Tests pass
- [ ] No secrets added
- [ ] Module manifests validate
- [ ] Database changes include migrations + rollback notes (if any)
- [ ] Public contracts versioned (if changed)
- [ ] Docs updated if behavior changed
- [ ] Third-party module changes do not enable/auto-load themselves

---

## Where to start

- `docs/architecture.md` (core design)
- `docs/modules.md` (module lifecycle + manifest schema)
- `third_party/acme/garage/` (example third-party module skeleton)

If those files don’t exist yet, add them first as docs-only PRs before implementing code.
