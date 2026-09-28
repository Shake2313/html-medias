# HTML Medias

Local collection of AI-generated HTML artifacts. Two kinds:

- **Media** — interactive HTML demos (sliders, canvas, mouse/keyboard input).
- **Docs** — HTML documents (notes, write-ups, reports).

Open `index.html` in browser. Toggle between **Media** and **Docs** views in
the header.

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
└── README.md
```

## Views

**Media view.** Grid of cards on top, iframe viewer below. Click card → loads
media into viewer. Click again → closes.

**Docs view.** Collapsible sidebar list on left, main area on right. Click
item → loads doc into main area. Toggle sidebar button collapses/expands
the list.

## Rules for AI agents adding new content

### Media (`media/`)

1. **Pick next number.** Scan `media/`. Use next 3-digit prefix (e.g. `004-`).
2. **Filename.** `NNN-kebab-case-name.html` for single file, or folder
   `NNN-kebab-case-name/index.html` when assets needed.
3. **Self-contained.** Inline all CSS/JS. No CDN — must work offline via
   `file://` protocol.
4. **Interactive, not static.** Must use sliders, mouse, keyboard, canvas,
   or similar input. Plain images don't count. Static write-ups go in `docs/`.
5. **Update `index.html`.** Append card inside `<!-- MEDIA LIST -->` block.
6. **Display title can be Korean.** Filename stays ASCII kebab-case.

Card format:

```html
<div class="card" data-src="media/NNN-name.html">
  <span class="num">NNN</span>
  <span class="title">Display Title (Korean OK)</span>
</div>
```

For folder-based media: `data-src="media/NNN-name/index.html"`.

### Docs (`docs/`)

1. **Filename.** `kebab-case-name.html`. No numeric prefix required —
   docs are listed alphabetically by title, not by index.
2. **Self-contained.** Inline all CSS/JS. No CDN.
3. **Static or lightly interactive.** Reading-first. If the artifact's
   primary purpose is interaction, put it in `media/` instead.
4. **Update `index.html`.** Append entry inside `<!-- DOCS LIST -->` block.
5. **Display title can be Korean.** Filename stays ASCII kebab-case.

Doc entry format:

```html
<div class="doc-item" data-src="docs/some-doc.html">
  <span class="doc-title">Display Title (Korean OK)</span>
</div>
```

## Tech constraints

- Runs from `file://` protocol. No `fetch()` to local files. No directory
  listing via JS.
- Target browser: modern Chromium / Firefox.
- No external CDN. Inline everything.
- Iframe-embedded — avoid `top.location` or other frame-busting code.

## Example ideas

**Media:** slider-driven color, mouse particle field, click-to-spawn canvas,
drag-deform image, keyboard animation.

**Docs:** math write-ups, derivations, lab notes, design memos, lit reviews.
