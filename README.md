# shamwari-sandbox

The execution host behind **code.shamwari.ai**: Rust, embedding
`deno_core`, behind a shared `SandboxProvider` interface.

> **Status: not started.** This repo is a placeholder created alongside the
> monorepo split. The phase it belongs to has not opened yet. Nothing here
> is implemented.

## Why Rust and `deno_core`, and not Cloudflare Containers

`personal`-scope artefacts **cannot execute on Cloudflare Containers under
rule 1** — that is the entire reason this component exists separately
rather than as another Worker.

`deno_core` here is an **embedded library inside a Rust host**, not a
deployment target. This is the one place Deno appears anywhere in the
Shamwari plans, and it is worth being precise about: Deno Deploy and
Cloudflare Workers are separate, competing edge runtimes, and nothing in
this ecosystem deploys to Deno. Every other Shamwari service runs `workerd`
via `wrangler`.

## Rule 1

This repo executes `personal`-scope artefacts by design. It is the single
most rule-1-sensitive component in the ecosystem: code that runs here must
never be able to reach a third-party inference provider, and the sandbox
boundary is what enforces that. Any change to the isolation model needs
review against rule 1 even when it looks like a refactor.

## Related

- [`shamwari`](https://github.com/shamwari-ai/shamwari) — umbrella: architecture, migration log, repo index
- [`shamwari-gateway`](https://github.com/shamwari-ai/shamwari-gateway) — routes to this host
- Org standards: [ORG_STANDARDS.md](https://github.com/shamwari-ai/.github/blob/main/ORG_STANDARDS.md)

## Merge convention

This repository rebase-merges. Every commit on a branch lands on `main`
individually, so commit messages are part of the permanent record.
