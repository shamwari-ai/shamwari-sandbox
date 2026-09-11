# Shamwari Sandbox

> The execution host for personal-scope artefacts — a Rust process embedding `deno_core`, behind a shared `SandboxProvider` interface.

[![CI](https://github.com/shamwari-ai/shamwari-sandbox/actions/workflows/ci.yml/badge.svg)](https://github.com/shamwari-ai/shamwari-sandbox/actions/workflows/ci.yml)
[![Lint](https://github.com/shamwari-ai/shamwari-sandbox/actions/workflows/lint.yml/badge.svg)](https://github.com/shamwari-ai/shamwari-sandbox/actions/workflows/lint.yml)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://www.apache.org/licenses/LICENSE-2.0)
![Status](https://img.shields.io/badge/status-not%20started-lightgrey?style=flat-square)

**Status:** not started | **Planned address:** `code.shamwari.ai` (does not resolve yet) | **Design:** [`docs/scaling-and-memory.md`](https://github.com/shamwari-ai/shamwari/blob/main/docs/scaling-and-memory.md)

---

## What it is

Nothing is implemented here yet. This repo is a placeholder created alongside
the monorepo split, and the phase it belongs to has not opened. `code.shamwari.ai`
is the intended address; it has no DNS record today, so it is written here as
plain text rather than as a link that would 404.

When it exists, this is the process that runs code Shamwari generates for a
user, for the cases that cannot run anywhere else.

## Why Rust and `deno_core`, and not Cloudflare Containers

Cloudflare Containers is the substrate for the ordinary case. The account
ceiling is 1,500 concurrent vCPU and 6 TiB concurrent memory, so at the `lite`
instance size memory binds first at roughly 24,000 concurrent sandboxes — far
fewer at any size worth running a real interpreter on. A sandbox per session is
affordable only if an idle session holds no container: create on first
execution, not on session open, and reap on idle.

But `personal`-scope artefacts **cannot execute on Containers under rule 1**.
That is the entire reason this component exists separately rather than as
another Worker, and it is why the `SandboxProvider` interface comes before
either backend — build the interface first, or the second one never fits.

`deno_core` here is an **embedded library inside a Rust host**, not a
deployment target. This is the one place Deno appears anywhere in the Shamwari
plans, and it is worth being precise about: Deno Deploy and Cloudflare Workers
are separate, competing edge runtimes, and nothing in this ecosystem deploys
to Deno. Every other Shamwari service runs `workerd` via `wrangler`.

Rust earns its place here specifically because there is CPU-bound work. It is
not the house language — the gateway is TypeScript and Core is Python, both
for good reasons stated in `shamwari`'s `CLAUDE.md`.

## Rule 1

This repo executes `personal`-scope artefacts by design. It is the single most
rule-1-sensitive component in the ecosystem: code that runs here must never be
able to reach a third-party inference provider, and the sandbox boundary is
what enforces that. Any change to the isolation model needs review against
rule 1 even when it looks like a refactor.

## Merge convention

This repository rebase-merges. Every commit on a branch lands on `main`
individually, so commit messages are part of the permanent record. Commits are
signed; rebase preserves signature verification.

## Ecosystem

- [`shamwari`](https://github.com/shamwari-ai/shamwari) — the umbrella:
  architecture, migration log, repo index;
  [`docs/scaling-and-memory.md`](https://github.com/shamwari-ai/shamwari/blob/main/docs/scaling-and-memory.md)
  is the design this repo implements
- [`shamwari-gateway`](https://github.com/shamwari-ai/shamwari-gateway) — will
  route to this host
- [Org standards](https://github.com/shamwari-ai/.github/blob/main/ORG_STANDARDS.md)

## Contributing

See the org's
[CONTRIBUTING.md](https://github.com/shamwari-ai/.github/blob/main/CONTRIBUTING.md),
[SECURITY.md](https://github.com/shamwari-ai/.github/blob/main/SECURITY.md) and
[CODE_OF_CONDUCT.md](https://github.com/shamwari-ai/.github/blob/main/CODE_OF_CONDUCT.md).

## Licence

Licensed under the [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0)
— see `LICENSE` and `NOTICE`.

© Bundu Foundation. Shamwari is Bundu Foundation IP, sold commercially under
Nyuchi Africa.
