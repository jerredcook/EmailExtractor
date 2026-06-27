# relay-tools

Two small, self-hosted utilities for moving content past everyday SaaS friction — no accounts, no uploads, no subscriptions.

- **Par Avion** — mail a file that's too big to mail. Splits a large file into email-sized parts, makes ready-to-send drafts, and reassembles them on the other end.
- **loom-grab** — save a Loom video you're authorized to view as a clean MP4, without creating a Loom account.

Everything runs locally. Par Avion needs only a browser; loom-grab needs `yt-dlp` and `ffmpeg`. Node.js 18+ is used only for the dev scripts and tests.

## Quick start

```bash
git clone <your-remote> relay-tools   # or just unzip this folder
cd relay-tools
npm test            # runs the Par Avion round-trip tests (no install needed)
npm run serve       # opens Par Avion at http://localhost:5179
```

## Open it in VS Code with Claude Code

```bash
cd relay-tools
code .              # open the folder in VS Code
```

Then, in VS Code's integrated terminal, start Claude Code:

```bash
claude              # first run opens a browser to authenticate
```

If you don't have Claude Code yet, install it with the native installer (`curl -fsSL https://claude.ai/install.sh | bash`) or via npm (`npm install -g @anthropic-ai/claude-code`, which needs Node 18+). Verify with `claude --version` and `claude doctor`. Claude Code reads [`CLAUDE.md`](./CLAUDE.md) at startup, which already describes the layout, commands, and the invariants worth respecting — so it can pick up the project without re-explaining. Running `/init` inside Claude Code can extend that file further.

## The tools

### Par Avion — `par-avion/`

Open `par-avion/index.html` (double-click, or `npm run serve` for a localhost secure context). Two modes:

- **Split & draft:** drop in your oversized file, set your attachment limit (default 10 MB), and it produces one `.eml` draft per part plus a `manifest.json` of per-part SHA-256 checksums. Double-click a draft to open it in Apple Mail / Outlook / Thunderbird and send.
- **Reassemble:** drop the saved attachments (or the `.eml` files) plus the manifest. It confirms every part is present, verifies each checksum, joins them in order, and downloads the original file — bit-for-bit identical.

The non-obvious part it handles for you: your size cap applies to the *encoded* email, and base64 inflates binary by ~37%, so a naive 10 MB chunk yields a ~13 MB email that bounces. Par Avion sizes chunks backward from your limit (with headroom), so a 10 MB cap produces ~6.7 MB parts and the largest email lands around 9.2 MB. See [`par-avion/README.md`](./par-avion/README.md) for the internals.

### loom-grab — `loom-grab/`

A wrapper around `yt-dlp` for saving a Loom video you have access to:

```bash
./loom-grab/loom-grab.sh "https://www.loom.com/share/<id>" "the-password"
```

Leave the password off to be prompted privately. Requires `yt-dlp` and `ffmpeg` (`brew install yt-dlp ffmpeg`). It tries your password directly, falls back to reusing your authenticated browser session, and prints a manual route if both miss. Use it only for content you're authorized to access. See [`loom-grab/README.md`](./loom-grab/README.md).

## Dev scripts

| Command | What it does |
| --- | --- |
| `npm test` | Run the chunk-core test suite. |
| `npm run build` | Re-inline `par-avion/lib/chunk-core.mjs` into `index.html`. |
| `npm run serve` | Serve `par-avion/` on localhost (set `PORT` to change). |

## License

MIT — see [`LICENSE`](./LICENSE).
