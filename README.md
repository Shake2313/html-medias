# HTML Medias

Collection of AI-generated HTML artifacts. Two kinds:

- **Media** — interactive HTML demos (sliders, canvas, mouse/keyboard input).
- **Docs** — HTML documents (notes, write-ups, reports).

Open `index.html` in a browser, straight from disk (`file://`). Toggle between
**Media** and **Docs** views in the header.

## Where it lives

- **Google Drive** — local working copy (Windows).
- **GitHub** — [`Shake2313/html-medias`](https://github.com/Shake2313/html-medias)
  (private). Claude Code cloud sessions clone from here.

Syncing is manual: push local commits before starting a cloud session. Cloud
work lands on a branch → PR → merge into `main`. Run `git pull` locally before
working there again.

## Structure

```
HTML Medias/
├── index.html          # Gallery entry point (Media + Docs views)
├── media/              # Interactive media
│   ├── 001-name.html   # Single-file media
│   └── 002-name/       # Folder when assets needed
│       ├── index.html
│       └── assets/
├── docs/               # HTML documents
│   └── some-doc.html
├── References/         # Source material (notes, images) behind media/docs; not in the gallery
├── .claude/launch.json # Preview server for the Claude desktop app (http://localhost:8123)
├── CLAUDE.md           # Points agents to this file
└── README.md
```

`output/` (screenshots, scratch files) and `.claude/settings.local.json` are
git-ignored.

## Views

**Media view.** Grid of cards on top (newest first), iframe viewer below.
Click card → loads media into viewer. Click again → closes. The viewer grows
to fit the media (see [Height reporting](#height-reporting)).

**Docs view.** Collapsible sidebar list on left, main area on right. Click
item → loads doc into main area. Toggle sidebar button collapses/expands
the list.

## Rules for AI agents adding new content

### Media (`media/`)

1. **Pick next number.** Scan `media/`. Use next 3-digit prefix (e.g. `005-`).
2. **Filename.** `NNN-kebab-case-name.html` for single file, or folder
   `NNN-kebab-case-name/index.html` when assets needed.
3. **Self-contained.** Write your own CSS/JS inline (or inside the media's
   own folder). Third-party libraries and fonts may come from a CDN — see
   [CDN policy](#cdn-policy).
4. **Interactive, not static.** Must use sliders, mouse, keyboard, canvas,
   or similar input. Plain images don't count. Static write-ups go in `docs/`.
5. **Report height.** Wrap the page in `<div class="app">` and post its
   height to the gallery — see [Height reporting](#height-reporting).
6. **Update `index.html`.** Insert the card at the **top** of the
   `<!-- MEDIA LIST -->` block (newest first).
7. **Display title can be Korean.** Filename stays ASCII kebab-case.

Card format:

```html
<div class="card" data-src="media/NNN-name.html">
  <span class="num">NNN</span>
  <span class="title">Display Title (Korean OK)</span>
</div>
```

For folder-based media: `data-src="media/NNN-name/index.html"`.

#### Height reporting

Under `file://` the gallery can't reliably read the iframe's content height,
so each media reports it. The viewer is at least 680px tall and grows to the
reported height.

```js
const app = document.querySelector('.app');
let heightTimer = 0;
function postHeight() {
  clearTimeout(heightTimer);
  heightTimer = setTimeout(() => {
    parent.postMessage({ type: 'interactive-media-height', height: app.scrollHeight }, '*');
  }, 80);
}
new ResizeObserver(postHeight).observe(app);
```

### Docs (`docs/`)

1. **Filename.** `kebab-case-name.html`. No numeric prefix.
2. **Self-contained.** Own CSS/JS inline. Libraries and fonts (MathJax,
   Google Fonts, …) from a CDN are fine — see [CDN policy](#cdn-policy).
3. **Static or lightly interactive.** Reading-first. If the artifact's
   primary purpose is interaction, put it in `media/` instead.
4. **Update `index.html`.** Add the entry inside the `<!-- DOCS LIST -->`
   block, keeping the list alphabetical by display title (`index.html` does
   not sort it for you).
5. **Display title can be Korean.** Filename stays ASCII kebab-case.

Doc entry format:

```html
<div class="doc-item" data-src="docs/some-doc.html">
  <span class="doc-title">Display Title (Korean OK)</span>
</div>
```

## Tech constraints

- **Runs from `file://`.** Everything must work when `index.html` is opened
  straight from disk. Relative `<a>`, `<img>`, `<link>`, and classic
  `<script src>` to local files work. `fetch()`/XHR, `<script type="module">`
  imports, and canvas pixel reads (`getImageData`) of *local* files do not —
  Chromium treats every `file://` page as its own origin. No directory listing
  via JS.
- **Target browser:** modern Chromium / Firefox.
- **Iframe-embedded** — avoid `top.location` or other frame-busting code.
- `.claude/launch.json` serves the folder over `http://` for previews. That
  hides the `file://` limits above, so don't rely on anything that only works
  there.

### CDN policy

Internet access is assumed: the project lives in Google Drive and on GitHub,
so any machine that can open it is online. CDN-hosted libraries and fonts are
allowed — remote URLs load fine from `file://` pages, ES modules included.

- **Pin exact versions** (`mathjax@3.2.2`, `three@0.160.0`). No `latest` or
  unversioned URLs.
- **Well-known CDNs only:** `cdnjs.cloudflare.com`, `cdn.jsdelivr.net`,
  `unpkg.com`, Google Fonts.
- **Cloud sessions:** Claude Code's default cloud network (*Trusted*) reaches
  Google Fonts but **not** cdnjs / jsDelivr / unpkg. A headless-browser check
  in the cloud can render without those libraries — that's the sandbox, not a
  bug in the page, so don't inline the library to "fix" it. To allow them,
  switch the environment's network access to *Custom* (defaults + those
  domains).

## Example ideas

**Media:** slider-driven color, mouse particle field, click-to-spawn canvas,
drag-deform image, keyboard animation.

**Docs:** math write-ups, derivations, lab notes, design memos, lit reviews.
