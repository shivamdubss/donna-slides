# Donna Slides — Project Guide

## Architecture

Multi-deck HTML/CSS slide system. Slides are authored at 1920×1080px and scaled to fit the browser with `transform: scale()`. PDF export uses `window.print()` with `@page { size: 1920px 1080px; margin: 0; }`.

**Fonts:** Satoshi (display/headlines), Plus Jakarta Sans (body), Geist Mono (eyebrows, mono labels, slide numbers).

**Structure:**
```
donna-slides/
├── styles.css              # shared design tokens + slide scaling + print CSS
├── index.html              # deck picker (home page)
└── decks/
    ├── _template/          # starting point for new decks — copy this folder
    ├── product/            # Docketing product demo (10 slides)
    └── lunch-and-learn/    # The Billable Hour Is Bleeding Out (12 slides)
```

Each deck is `decks/{name}/index.html` + `decks/{name}/deck.css`. Both link to `../../styles.css`.

**New decks:** Copy `decks/_template/`, rename the folder, update the title and content. Register the new deck in the root `index.html` deck picker.

---

## Design Rules

These rules come from a full design audit of the product deck. Enforce them during creation — don't wait for `/design-review` to catch them.

### Color

- **Never use accent or category colors in data visualizations.** Bar charts, progress fills, graph lines → `var(--text-primary)` at 0.65–0.85 opacity. Using `#4F46E5` (indigo/purple) or any colored accent in charts is AI slop pattern #1.
- **Category color tokens are for badges and labels only.** They exist to identify legal work types in the UI, not to color arbitrary data.
  - Research: `--cat-research` (#4F46E5, indigo)
  - Drafting: `--cat-drafting` (#0F766E, teal)
  - Communication: `--cat-comm` (#C2410C, orange)
  - Court: `--cat-court` (#BE123C, rose)
  - Review: `--cat-review` (#475569, slate)

### Typography hierarchy

Every slide must have at least 3 visual levels:

1. **Eyebrow** — Geist Mono, `var(--text-faint)`, **16px minimum**, uppercase, letter-spacing 0.12em
2. **Headline** — Satoshi, `var(--text-primary)`, 64–96px depending on slide
3. **Body** — Plus Jakarta Sans, `var(--text-muted)` or `var(--text-secondary)`, 20–24px

**Minimum font sizes:**
- Slides: **16px** for any visible text (eyebrows, labels, table cells, chart annotations). No exceptions.
- Home/picker page: **13px** for metadata labels and tags.
- At 66.7% scale, 13px renders as ~8.7px — unreadable. Always enforce the minimums.

Rules:
- Sub-headlines placed directly below a primary headline must be visibly smaller and use `var(--text-muted)`. Never match the primary headline's size or color.
- All headlines that wrap to 2+ lines need an explicit `<br>` to control the break. Always check for orphaned single words on the last line.

### Spacing & layout

- All slides use **80px padding** on all sides. Never less.
- Card/panel grids: add `flex: 1` to the body area inside each card so cards in a row stretch to fill the container height uniformly. Never leave visible bottom gaps in card grids.

### Visual elements

- **Hero/CTA background blooms:** Use `radial-gradient(ellipse 1600px 640px at 50% 108%, ...)` as the minimum size. Position the center point *below* the slide (108%) so the glow rises from the bottom. Extend the first color stop to 40% before transparency. Smaller blooms look faint and unintentional.
- **Transformation arrows** (before→after, raw→polished): Use `⇉` or `›` glyphs at ≥32px. Always include a text label naming the transformation (e.g. "Claude AI", "summarized"). Do not rely on thin CSS `→` characters or border-based arrows — they disappear at scale.
- **Data flow step arrows:** Simple `›` glyph in a flex column with an explicit line/dash element. Avoid complex pseudo-element arrow constructions. If you can't see it clearly in a screenshot, it's not strong enough.

---

## Slide Wordmark

Use the image logo, not a text span:
```html
<img class="slide-wordmark" src="../../Donna-Logo-No-Background.png" alt="donna">
```

The `.slide-wordmark` class in `styles.css` handles positioning (**top-right**, absolute). The slide number sits bottom-right — logo and number are anchored on the same side, forming a clean right-column chrome pair.

---

## Icons

Use **Lucide** via CDN. Add to every deck's `<head>`:
```html
<script src="https://unpkg.com/lucide@latest/dist/umd/lucide.min.js"></script>
```

Add before `</body>` (after all content):
```html
<script>lucide.createIcons();</script>
```

Usage anywhere in HTML:
```html
<i data-lucide="icon-name" style="width:24px;height:24px;"></i>
```

Full icon catalog: [lucide.dev/icons](https://lucide.dev/icons)

**Icon sizing on slides:** 20–28px for inline icons, 36–44px for standalone icon blocks (e.g., step cards).
**Icon sizing on home page:** 36px for deck card icons.

Lucide replaces `<i data-lucide>` elements with inline SVGs before the page renders, so **PDF export works correctly** — icons are baked into the DOM, not loaded as external resources.

**When to use icons:**
- CTA/download buttons — `download` icon
- Feature/step cards — contextual icon per step (eye, sparkles, file-check, etc.)
- Privacy/security checklists — one icon per item (shield-check, lock, hard-drive, etc.)
- Discussion question cards — `message-circle`
- Source/reference items — `external-link`
- Home page deck cards — `presentation`, `book-open`, `trending-up`, etc.

---

## Pre-export Checklist

Before downloading the PDF:
- [ ] All bar charts / data fills use neutral dark, not accent colors
- [ ] All multiline headlines have `<br>` — no orphaned words
- [ ] Every slide has eyebrow → headline → body hierarchy
- [ ] Card grids fill height uniformly (no bottom gaps)
- [ ] Hero bloom is visible at full size (test at 1920px)
- [ ] All transformation arrows are ≥32px and labeled
- [ ] Run `/design-review` for a full screenshot audit
