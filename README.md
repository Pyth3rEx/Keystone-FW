# Keystone Framework

<p align="center">
  <img alt="Keystone Framework" src="https://raw.githubusercontent.com/Pyth3rEx/Keystone-FW/main/docs/assets/keystone-banner.png" width="100%" />
</p>

<p align="center">
  <strong>A clean, modular, TypeScript-first FiveM framework with a PostgreSQL backbone.</strong>
  <br />
  Built as a portfolio-grade codebase: typed contracts, plugin architecture, migrations, and audit-friendly design.
</p>

<p align="center">
  <a href="https://github.com/Pyth3rEx/Keystone-FW/actions/workflows/ci.yml">
    <img alt="CI" src="https://img.shields.io/github/actions/workflow/status/Pyth3rEx/Keystone-FW/ci.yml?branch=main&label=CI&logo=githubactions&logoColor=white" />
  </a>
  <a href="https://github.com/Pyth3rEx/Keystone-FW/blob/main/LICENSE">
    <img alt="License" src="https://img.shields.io/github/license/Pyth3rEx/keystone?label=License" />
  </a>
  <a href="https://github.com/Pyth3rEx/Keystone-FW/releases">
    <img alt="Release" src="https://img.shields.io/github/v/release/Pyth3rEx/keystone?include_prereleases&label=Release" />
  </a>
  <a href="https://github.com/Pyth3rEx/Keystone-FW/commits/main">
    <img alt="Last Commit" src="https://img.shields.io/github/last-commit/Pyth3rEx/keystone?label=Last%20commit" />
  </a>
  <a href="https://github.com/Pyth3rEx/Keystone-FW/issues">
    <img alt="Issues" src="https://img.shields.io/github/issues/Pyth3rEx/keystone?label=Issues" />
  </a>
  <a href="https://github.com/Pyth3rEx/Keystone-FW/pulls">
    <img alt="PRs" src="https://img.shields.io/github/issues-pr/Pyth3rEx/keystone?label=PRs" />
  </a>
  <a href="https://github.com/Pyth3rEx/Keystone-FW/stargazers">
    <img alt="Stars" src="https://img.shields.io/github/stars/Pyth3rEx/keystone?label=Stars" />
  </a>
</p>

---

## What is Keystone?

Keystone is a **framework core** plus a set of **system modules** (resources) that implement the essentials of an RP server without turning into a monolithic script dump.

Keystone is intentionally opinionated:
- **TypeScript-first** (typed events, typed exports, typed schemas)
- **Plugin architecture** (core is stable; features are modules)
- **PostgreSQL** persistence (migrations + repositories)
- **Audit-friendly** (structured logs, transaction ledger patterns)
- **Developer experience** (fast iteration, clear errors, clean APIs)

---

## Goals

- **Framework, not a pack**: stable contracts and composable modules.
- **Consistency**: one standard for notifications, commands, permissions, logging.
- **Maintainability**: no shared global state spaghetti; clear boundaries.
- **Portfolio-grade**: readable architecture, docs, CI, and a runnable demo.

---

## Features (planned / in progress)

### Core
- [ ] Typed event bus + lifecycle (`keystone:core:ready`)
- [ ] Service container (DI-lite) and module registry
- [ ] Config loader with env overrides
- [ ] Structured logging (JSON-friendly)

### Systems
- [ ] Identity / characters (create, select, persist)
- [ ] Accounts + transaction ledger (cash/bank)
- [ ] Inventory (minimal slots/weight, item registry)
- [ ] Jobs (roles, grades, duty state, pay)
- [ ] Admin (essential commands + audit logs)

### Tooling
- [ ] PostgreSQL migrations + seed tooling
- [ ] Local dev via Docker Compose
- [ ] CI (lint/build) on PR

---

## Repository layout

```text
.
├── resources/
│   ├── [keystone]/
│   │   ├── keystone-core/           # Core runtime (events, DI, config, logging)
│   │   ├── keystone-example/        # Minimal example module (proves plugin model)
│   │   └── ...                      # Future system modules (identity, inventory, etc.)
├── packages/
│   ├── shared/                      # Shared TS types, schemas, utilities
│   └── tooling/                     # Migration runner, generators, seed scripts
├── docs/
│   ├── architecture.md
│   ├── api.md
│   └── assets/                      # README images (banner, diagrams)
└── .github/
    └── workflows/
        └── ci.yml
````

---

## Quick start (dev)

### Prerequisites

* **FXServer** (FiveM server artifacts)
* **Node.js** (LTS recommended)
* **pnpm** (recommended) or npm
* **Docker** (for local PostgreSQL)

### 1) Clone

```bash
git clone https://github.com/Pyth3rEx/keystone.git
cd keystone
```

### 2) Install

```bash
pnpm install
```

### 3) Start PostgreSQL (Docker)

```bash
docker compose up -d
```

### 4) Run migrations

```bash
pnpm db:migrate
```

### 5) Build

```bash
pnpm build
```

### 6) Add resources to your server

In your `server.cfg`:

```cfg
ensure keystone-core
ensure keystone-example
```

Then start FXServer.

---

## Configuration

Keystone supports config files + environment overrides.

Example:

```env
KEYSTONE_DB_HOST=127.0.0.1
KEYSTONE_DB_PORT=5432
KEYSTONE_DB_NAME=keystone
KEYSTONE_DB_USER=keystone
KEYSTONE_DB_PASSWORD=keystone
KEYSTONE_LOG_LEVEL=info
```

---

## API philosophy

Keystone exposes functionality through:

* **exports** (stable API surface)
* **namespaced events** (typed payloads in TS)
* **services** resolved through the container (`resolve("db")`, `resolve("logger")`, etc.)

Guidelines:

* No feature module depends on another feature module directly; it depends on **keystone-core** contracts.
* All events are namespaced as `keystone:<domain>:<action>`.

---

## Roadmap

* `v0.1.0` Core boot + ready event + module registry + logger + example module
* `v0.2.0` PostgreSQL layer + migrations + repository base
* `v0.3.0` Identity (characters) + session state
* `v0.4.0` Accounts + ledger
* `v0.5.0` Inventory
* `v0.6.0` Jobs + duty

---

## Contributing

This repo is built as a portfolio project. PRs are welcome, but the main goal is architectural clarity and consistent standards.

* Use conventional commits if possible
* Keep modules small and composable
* Avoid adding features that force Keystone into a monolith

---

## License

MIT. See `LICENSE`.

---

## Credits

* FiveM / Cfx.re ecosystem for the runtime and tooling
* Community patterns around exports/events and resource design

> Note: Replace placeholders like `Pyth3rEx` and add `docs/assets/keystone-banner.png` when ready.