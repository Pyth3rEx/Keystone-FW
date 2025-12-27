# Keystone Framework

<p align="center">
  <strong>A clean, modular, TypeScript-first FiveM framework with a PostgreSQL backbone.</strong><br/>
  Portfolio-grade architecture: typed contracts, plugin system, migrations, and audit-ready design.
</p>

<div align="center">
<a href="https://github.com/Pyth3rEx/Keystone-FW/blob/main/LICENSE">
  <img alt="License"
    src="https://img.shields.io/github/license/Pyth3rEx/Keystone-FW?style=for-the-badge&label=License" />
</a>
<a href="https://github.com/Pyth3rEx/Keystone-FW/releases">
  <img alt="Release"
    src="https://img.shields.io/github/v/release/Pyth3rEx/Keystone-FW?include_prereleases&style=for-the-badge&label=Release" />
</a>
<br/>
<a href="https://github.com/Pyth3rEx/Keystone-FW/issues">
  <img alt="Issues"
    src="https://img.shields.io/github/issues/Pyth3rEx/Keystone-FW?style=for-the-badge&label=Issues" />
</a>
<a href="https://github.com/Pyth3rEx/Keystone-FW/stargazers">
  <img alt="Stars"
    src="https://img.shields.io/github/stars/Pyth3rEx/Keystone-FW?style=for-the-badge&label=Stars" />
</a>
<a href="https://github.com/Pyth3rEx/Keystone-FW/network/members">
  <img alt="Forks"
    src="https://img.shields.io/github/forks/Pyth3rEx/Keystone-FW?style=for-the-badge&label=Forks" />
</a>

<p align="center">
  <img alt="Keystone Framework" src="https://github.com/Pyth3rEx/Keystone-FW/blob/main/docs/assets/keystone-banner.png?raw=true" width="100%" />
</p>

</div>
<div align="center">
<a href="https://github.com/Pyth3rEx/Keystone-FW/commits/main">
  <img alt="Last Commit"
    src="https://img.shields.io/github/last-commit/Pyth3rEx/Keystone-FW?style=for-the-badge&label=Last%20Commit" />
</a>
<a href="https://github.com/Pyth3rEx/Keystone-FW/actions">
  <img alt="CI"
    src="https://img.shields.io/github/actions/workflow/status/Pyth3rEx/Keystone-FW/ci.yml?branch=main&style=for-the-badge&label=CI&logo=githubactions&logoColor=white" />
</a>
<a href="https://github.com/Pyth3rEx/Keystone-FW/pulls">
  <img alt="Pull Requests"
    src="https://img.shields.io/github/issues-pr/Pyth3rEx/Keystone-FW?style=for-the-badge&label=PRs" />
</a>
</div>

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

See `docs/philosphy.md`.

---

## License

GNU v3. See `LICENSE`.

---

## Credits

* FiveM / Cfx.re ecosystem for the runtime and tooling
* Community patterns around exports/events and resource design