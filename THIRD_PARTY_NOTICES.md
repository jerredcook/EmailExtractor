# Third-party notices

Toolshed's own source is MIT-licensed (see [`LICENSE`](./LICENSE)). Two tools
vendor third-party components with their own licenses, reproduced below. Vendored
copies are byte-for-byte redistributions of the upstream releases named here.

---

## 1. FFmpeg / ffmpeg.wasm — `tools/media-converter/`

The Media Converter runs FFmpeg compiled to WebAssembly. **The compiled core
(`tools/media-converter/vendor/ffmpeg-core.wasm`) is licensed under the GNU
General Public License, version 2 or later (GPL-2.0-or-later).** It is built with
`--enable-gpl` and includes GPL-licensed encoders (notably **libx264** and
**libx265**), which makes the resulting binary as a whole GPL. This is stricter
than the MIT license that covers Toolshed's own code, and it governs that file.

Full build configuration embedded in the binary:

```
--enable-cross-compile --enable-gpl --enable-libass --enable-libfreetype
--enable-libfribidi --enable-libmp3lame --enable-libopus --enable-libtheora
--enable-libvorbis --enable-libvpx --enable-libwebp --enable-libx264
--enable-libx265 --enable-libzimg --enable-zlib
```

Vendored packages and their pinned upstream sources (this is the "corresponding
source" pointer the GPL requires — the complete source is available at these
locations):

| Vendored file(s) | Package | Version | License | Source |
|---|---|---|---|---|
| `ffmpeg-core.js`, `ffmpeg-core.wasm` | `@ffmpeg/core` | 0.12.6 | GPL-2.0-or-later (as built) | https://www.npmjs.com/package/@ffmpeg/core/v/0.12.6 · build scripts: https://github.com/ffmpegwasm/ffmpeg.wasm |
| `ffmpeg.js`, `814.ffmpeg.js` | `@ffmpeg/ffmpeg` | 0.12.10 | MIT | https://www.npmjs.com/package/@ffmpeg/ffmpeg/v/0.12.10 |
| `util.js` | `@ffmpeg/util` | 0.12.1 | MIT | https://www.npmjs.com/package/@ffmpeg/util/v/0.12.1 |

Underlying components: **FFmpeg** — https://ffmpeg.org/download.html (source),
GPL/LGPL per build config above; **x264** — https://www.videolan.org/developers/x264.html
(GPL-2.0-or-later); **x265** — https://bitbucket.org/multicoreware/x265 (GPL-2.0-or-later).
FFmpeg is © the FFmpeg developers. `@ffmpeg/*` JavaScript wrappers are
© 2019 Jerome Wu (MIT).

The full text of the GNU GPL v2 is at https://www.gnu.org/licenses/old-licenses/gpl-2.0.txt
and v3 at https://www.gnu.org/licenses/gpl-3.0.txt. Anyone who receives this
binary may obtain the complete corresponding source from the links above.

> If GPL redistribution is undesirable, swap `@ffmpeg/core` for a non-GPL
> (LGPL) core build — one compiled without `--enable-gpl`/`--enable-libx264`/
> `--enable-libx265` — and drop the H.264/H.265 (MP4/MKV/MOV via libx264)
> presets accordingly.

---

## 2. pdf-lib — `tools/pdf-tools/`

PDF Tools inlines **pdf-lib** (minified) in a single marked `<script>` block.
pdf-lib is MIT-licensed; its notice was lost during minification and is restored
here as MIT requires. pdf-lib bundles **pako** (MIT) and **tslib** (BSD-0/Apache-2.0).

| Package | Version | License | Source |
|---|---|---|---|
| `pdf-lib` | 1.17.1 | MIT | https://github.com/Hopding/pdf-lib |
| `pako` (bundled by pdf-lib) | — | MIT | https://github.com/nodeca/pako |
| `tslib` (bundled by pdf-lib) | — | 0BSD | https://github.com/microsoft/tslib |

```
pdf-lib — MIT License — Copyright (c) 2019 Andrew Dillon

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN
AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION
WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
```

---

## Fonts

Every page loads **Space Mono** and **Syne** from Google Fonts via a CSS
`@import` (both licensed under the SIL Open Font License 1.1). This is the one
external network request the site makes; self-hosting the `woff2` files would
remove it and is tracked as a known privacy inconsistency.
