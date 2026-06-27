# CLAUDE.md

Guidance for working in this repo. Keep this file short and current — it loads into every Claude Code session.

## What this is

Two small, self-hosted utilities that route content past annoying SaaS friction:

- **`par-avion/`** — a browser app that splits a large file into email-sized parts, turns each into a ready-to-send `.eml` draft, and reassembles them on the receiving end.
- **`loom-grab/`** — a bash wrapper around `yt-dlp` that saves a Loom video the user is authorized to view as a clean MP4.

No backend. No framework. Node 18+ is only needed for the dev scripts and tests, not to use the tools.

## Commands

- `npm test` — run the chunk-core test suite (Node's built-in runner).
- `npm run build` — regenerate the inlined core inside `par-avion/index.html` from its source module.
- `npm run serve` — serve `par-avion/` at http://localhost:5179 for local dev.

After any change to the chunking logic: `npm run build && npm test`.

## Repo map

```
par-avion/
  index.html              self-contained app (open directly or via `npm run serve`)
  lib/chunk-core.mjs      SOURCE OF TRUTH for the pure chunking logic (tested)
  test/chunk-core.test.js round-trip + invariant tests
loom-grab/
  loom-grab.sh            yt-dlp wrapper (POSIX bash, set -euo pipefail)
scripts/
  build.mjs               inlines chunk-core.mjs into index.html
  serve.mjs               static dev server
```

## Invariants — don't break these

1. **`index.html` is a single self-contained file.** It must keep working when opened directly (`file://`) or served. No bundler, no build step required *to use it*, no `import`/module loading at runtime. Vanilla JS only. **Never use `localStorage`/`sessionStorage`** — keep all state in memory.

2. **The core has one source of truth.** Pure, environment-agnostic functions live in `par-avion/lib/chunk-core.mjs`. The copy in `index.html` between the `// ==CHUNK-CORE:START==` and `// ==CHUNK-CORE:END==` markers is **generated** — never hand-edit it. Edit the `.mjs`, then `npm run build`. Only browser-specific code (`sha256Hex` via WebCrypto, `rndHex`, DOM wiring) lives outside the markers in `index.html`.

3. **The email-size bound is the whole point.** `rawChunkSizeForEmailLimit` works backward from the user's limit, accounting for base64's ~37% expansion plus headers and a safety margin, so the *encoded* `.eml` stays under the cap. The round-trip test asserts the largest `.eml` ≤ limit for several sizes — keep it green if you touch the math.

4. **The `.eml` wire format is a contract between split and reassemble.** Parts are `multipart/mixed` with a base64 `application/octet-stream` attachment named `<file>.partNNNofMMM`, plus `X-ChunkMail-Original` and `X-ChunkMail-Part` headers the receiver reads. Changing the format means updating `buildEml` **and** `parseEml` together, then re-running the round-trip test.

5. **`loom-grab.sh` stays portable.** Keep `set -euo pipefail`, the dependency checks, and the password→cookies→manual fallback chain. Avoid bashisms that break on macOS's stock bash 3.2 where there's an easy alternative.

## Scope and boundaries

`loom-grab` is for downloading videos the user is **authorized** to access — e.g. a video shared with them, with the password, or their own content. It works by using that legitimate access: the supplied `--video-password`, or the authenticated session from the user's browser via `--cookies-from-browser`. Do not turn it into something that guesses or brute-forces passwords, or that strips DRM. That boundary is deliberate; keep it.

## Style

Prefer editing existing files over adding new ones. Don't introduce dependencies or a build toolchain without a clear reason — the no-deps, no-bundler property is a feature. Match the existing terse, comment-light-but-not-absent style.
