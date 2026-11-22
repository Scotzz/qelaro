# AGENTS.md

## What this repo is

A **single-page static site** (Qeralo portfolio) plus self-hosted fonts. No build step.

```
index.html                  вся страница: разметка + CSS + JS
assets/fonts.css            только @font-face
assets/fonts/               manrope-cyrillic.woff2, manrope-latin.woff2, OFL.txt
AGENTS.md
.agents/skills/             инструменты агента, не часть сайта
```

- `index.html` holds all CSS in one `<style>` and all JS in one `<script>`. No frameworks, no bundler, no external requests.
- No `package.json`, no lockfile, no test/lint/typecheck tooling, **not a git repo**. Don't invent commands or scaffold tooling.
- Open `index.html` directly in a browser to view it.
- Font paths in `assets/fonts.css` are relative to that file (`fonts/…`), not to `index.html`. Moving the CSS means fixing them.

## Fonts and icons

- **Manrope** variable, `wght` 200–800, two self-hosted subsets. License `assets/fonts/OFL.txt` (OFL — required when self-hosting).
- Cyrillic verified via `cmap`: all 64 Russian letters + Ё/ё. Combined subsets also cover `« » — … • × №`.
- **Missing glyphs: `→` and `₽`.** Don't use them; they'd fall back to a system font. Also no `≈` or `✓` — use words or the Solar check icon.
- **Chillax has zero Cyrillic** (0 glyphs in U+0400–04FF) — unusable for Russian text. It was removed; don't reintroduce it.
- **Solar Linear icons** (480 Design, CC BY 4.0) are inlined as an SVG sprite at the top of `<body>` (`<symbol id="i-…">`), used via `<svg class="ic"><use href="#i-…"/></svg>`. Attribution text is in the footer and in an HTML comment. Keep both: CC BY requires it. To add an icon, fetch its `body` from `https://api.iconify.design/solar.json?icons=<name>-linear` and paste the paths into a new `<symbol>` — never add an external Iconify request.
- Favicon is an inline data-URI SVG — no `favicon.ico` request.

## Site mechanics

- Headings use fixed `font-variation-settings:'wght' 700`; body copy stays `font-weight: 400`. An interactive weight slider was built and deliberately removed at the user's request — don't reintroduce it.
- The scroll-driven 3D assembly is **pure CSS transforms**, no WebGL/three.js. JS only sets `--p` (0→1) on `#deck` from scroll position; all geometry is `calc()` off `--p`.
- `.build` uses `overflow-x: clip` — the rotated deck overflows horizontally by design. **Use `clip`, not `hidden`**: `hidden` would break the `position: sticky` pin inside.
- Default `--p: 1` in CSS means the assembled page shows with JS disabled.
- JS also sets `--scroll` (0→1) on `:root` in the same rAF for the fixed progress hairline.
- `prefers-reduced-motion` collapses the runway, disables 3D, the stamp, the underline draw, the pulse and the bob.
- Contact rows have copy buttons: `navigator.clipboard.writeText` with an `execCommand` fallback needed on `file://`. They swap `<use href>` to `#i-check`, announce via `#copyLive`, and revert after 1.8 s.
- The "Не верьте на слово" panel measures the page in the browser: UTF-8 byte counts via `Blob`, DOM node count, external `http(s)` resource entries, and `document.fonts.ready` time. Values are animated unless reduced motion. HTML is `outerHTML` bytes; CSS/JS count the inline blocks only — this is stated in the panel note.
- The clock shows the **visitor's** local time only; no promises about response time.
- **FAQ arrows need an explicit `transform:rotate(0deg)` initial value.** In Edge, a CSS transition from `none` to `rotate(180deg)` on an inline `<svg>` gets stuck at the identity matrix. Removing that initial value will silently break the rotation.
- **A grid overlay toggled by G was built and deliberately removed** at the user's request. Don't reintroduce it.
- `js-only` elements are hidden by a `<noscript>` style block; the page reads fine without JS.

## Verifying changes

No Chrome installed; Playwright MCP fails with "Chromium distribution 'chrome' is not found". **Edge works** via `playwright-core`:

```js
chromium.launch({ executablePath: 'C:\\Program Files (x86)\\Microsoft\\Edge\\Application\\msedge.exe' })
```

`python`/`python3` are Windows Store stubs and `py` is absent — use **`node`** for scripting. In Node 26, `-e` with both `require` and top-level `await` fails — wrap in an async IIFE.

After layout or asset-path edits check: failed requests / console errors, `document.fonts.check('16px Manrope', 'Привет')`, horizontal overflow at 320/360/390 px, the 3D scene at several scroll positions, copy/audit interactions, reduced motion, and the no-JS screenshot.

## Content still missing

No prices or deadlines in the copy — deliberately not invented. The owner must supply them before a pricing block is added.

## Skills

Project-local in `.agents/skills/`. The global copy of `threejs-react-three-fiber` was deliberately removed; don't reinstall it globally. That skill is currently unused — the site uses CSS 3D, not three.js.
