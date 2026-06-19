# ROOTCODE — Game Design Document

*Compile the World. Awaken the Roots.*

An interactive, single-file HTML game design document for **ROOTCODE**, an action-adventure where the player learns real JavaScript by reading, editing, and recompiling a broken bio-digital world. Built as a 22-section GDD with a scroll-driven "root path" navigation system, a live SVG world map and skill tree, filterable puzzle banks, flip-to-reveal boss cards, and a fully separate mobile layout.

Designed & documented by **[THENUX](https://www.thenuxofc.store)**.

---

## 1. What's in this package

| File | Description |
|---|---|
| `rootcode-gdd.html` | The entire GDD. One self-contained HTML file — markup, CSS, and JavaScript all live in this single document. No build step, no install, no backend. |
| `README.md` | This file. |

The HTML file is roughly 1,800 lines / ~113KB and has exactly one external dependency: Google Fonts (loaded via CDN `<link>` tags). Everything else — layout, animation, interactivity, the world map, the skill tree — is hand-written CSS and vanilla JavaScript with zero frameworks and zero build tooling.

## 2. Quick start

**Just open it.** Double-click `rootcode-gdd.html`, or drag it into any modern browser tab. That's the whole setup process.

To host it (recommended for sharing a link instead of a file):
- Drop it into any static host — GitHub Pages, Netlify, Vercel, Cloudflare Pages — as the single file, or rename it to `index.html` so it serves at the site root.
- No server-side code, database, or API keys are required anywhere in the document.

If you need it to work fully offline (no internet for the Google Fonts CDN), see [§6 Customization → Fonts](#fonts).

## 3. Document structure

The 22 requested GDD sections are grouped into **five Acts**, each with its own accent color, so the document's own structure mirrors the journey a player takes through the game's five biomes.

| Act | Theme / Accent | Sections |
|---|---|---|
| **I — The Seed** | Teal `#4dffc8` | 01 Executive Summary · 02 Game Vision |
| **II — The Lore** | Violet `#b98cff` | 03 Story & Worldbuilding |
| **III — The System** | Cyan / Magenta `#5ee7ff` / `#ff4fa3` | 04 Coding Learning System · 05 Core Gameplay Loop · 06 Quest Design · 07 Puzzle Design · 08 Skill Tree · 09 Game Economy |
| **IV — The Trial** | Crimson / Amber `#ff5c5c` / `#ffb454` | 10 Enemy Design · 11 Boss Battles · 12 UI/UX Design · 13 Visual Direction · 14 Audio Design · 15 Multiplayer · 16 Retention · 17 Monetization |
| **V — The Build** | Gold `#ffd27a` | 18 Technical Architecture · 19 Development Roadmap · 20 Visual Assets · 21 Risk Analysis · 22 Final Deliverable |

Each `<section class="doc">` carries a `data-act="1"`–`"5"` attribute, which is what drives its accent color via CSS custom properties (see §6).

## 4. Interactive features

| Feature | Where | How it works |
|---|---|---|
| **Root-path scroll nav** (signature element) | Desktop sidebar | A wavy SVG path runs the height of the nav. A glowing dot is positioned along it on every scroll frame using `path.getPointAtLength(progress * totalLength)`, so it visually "travels" the vine as you read. Its glow color shifts to match the Act currently in view. |
| **Scroll progress + active section tracking** | Global | An `IntersectionObserver` watches all 22 sections, highlights the matching nav link, and recolors the progress bar / nav accent to the active Act. |
| **World map** | §03 Story & Worldbuilding | Rendered into `#worldmapMount` at runtime from a `biomes` JS array. Hover any biome node for a name + one-line description tooltip. |
| **Skill tree** | §08 Skill Tree | A hand-authored SVG: 5 branches × 3 tiers (15 nodes) plus 4 cross-branch "graft" nodes (19 total). Hover any node for its native tooltip (`<title>`). |
| **Puzzle filter bank** | §07 Puzzle Design | 30 puzzle cards (10 coding / 10 logic / 10 debugging), toggled by category tabs — only the active category's cards render. |
| **Boss flip cards** | §11 Boss Battles | CSS 3D-transform flip cards. Hover to flip on desktop; tap to flip on touch devices (`matchMedia('(hover: none)')` detection). |
| **Mockup viewer** | §20 Visual Assets | Tab-switched live mockups for the Main Menu and HUD — built in CSS/HTML, not images. |
| **Scroll-reveal animations** | Every section | `IntersectionObserver`-driven fade/slide-up on every `.reveal` element as it enters the viewport. |
| **Hero load sequence** | Top of page | The "ROOTCODE" title is split into per-letter `<span>` elements at runtime and staggered in with a blur/glow "compile-in" animation; ambient floating particles ("motes") are generated and randomized on load. |

## 5. Mobile layout

The page does **not** just shrink the desktop layout — it swaps to a dedicated mobile pattern below `880px`:

- The sidebar root-path nav is hidden entirely.
- A thin **fixed progress bar** appears at the very top of the viewport, filling and recoloring as you scroll.
- A floating **hamburger button** (bottom-right) opens a full-screen overlay listing every section grouped by Act; tapping a link scrolls to it and auto-closes the overlay.
- Grids (enemy cards, boss cards, character cards, checklists, puzzle cards) collapse from multi-column to single/double column via CSS `@media` breakpoints at `880px`, `760px`, and `600px`.

## 6. Customization guide

All editable data lives in clearly labeled places — you shouldn't need to touch layout logic to update content.

### Colors & typography
Edit the `:root` block at the top of the `<style>` in `<head>`:
```css
--act1:#4dffc8;  /* Act I accent */
--act2:#b98cff;  /* Act II accent */
--act3:#5ee7ff;  /* Act III primary accent */
--act3b:#ff4fa3; /* Act III secondary accent */
--act4:#ff5c5c;  /* Act IV primary accent */
--act4b:#ffb454; /* Act IV secondary accent */
--act5:#ffd27a;  /* Act V accent */
--font-d:'Space Grotesk',sans-serif; /* display/headings */
--font-b:'Inter',sans-serif;         /* body text */
--font-m:'JetBrains Mono',monospace; /* code, labels, eyebrows */
```

### Adding or renaming a section
Two places must stay in sync:
1. The `sections` array near the top of the `<script>` block (id, number, title, act) — this drives **both** the desktop and mobile nav automatically.
2. The matching `<section class="doc" id="sX" data-act="N">…</section>` block in the HTML body.

### Editing the world map
Edit the `biomes` array in the `<script>` block — each entry takes `n` (number), `name`, `x`/`y` (SVG coordinates, viewBox is `0 0 900 260`), `color`, and `blurb`.

### Editing the puzzle bank
Each puzzle is a static `<div class="puzzle-card" data-cat="coding|logic|debug">` inside `#puzzleGrid` in §07. Add a new one with the same markup pattern and it will be picked up by the existing tab-filter JS automatically — no script changes needed.

### Editing the skill tree
The skill tree is static, hand-positioned SVG inside §08 (not data-driven), since node/link layout benefits from manual placement. Add a `<g class="stnode">` with a `<circle>`, `<title>` (tooltip text), and `<text>` label, then connect it with a `<line class="stlink">`.

### Fonts (offline use) {#fonts}
If you need the page to work with no internet connection, download `Space Grotesk`, `Inter`, and `JetBrains Mono` as local `.woff2` files, replace the `<link>` tags in `<head>` with `@font-face` declarations pointing at those local files, and keep the same `font-family` names used in the CSS so nothing else needs to change.

## 7. Browser support & accessibility

- Built for evergreen browsers (Chrome, Edge, Firefox, Safari 16.2+). The one modern CSS feature in use is `color-mix()` (in the Act banner border) — on older Safari this degrades to a slightly less transparent border, nothing breaks.
- Relies on `IntersectionObserver` and SVG `getPointAtLength()`, both broadly supported; the script includes a fallback (`reveal.in` applied immediately) if `IntersectionObserver` is unavailable.
- Respects `prefers-reduced-motion`: scroll-behavior and animation durations are neutralized for users with that OS-level setting enabled.
- Keyboard focus states are visible on all links and buttons (`:focus-visible` outline using the active accent color).
- Type/role indicators in the puzzle and enemy sections are labeled with text, not color alone.

## 8. Known limitations

- This was built and validated for HTML/CSS/JS correctness (tag balance, JS syntax, brace matching) without a live browser preview in the build environment — a quick scroll-through on your end is worth doing before sharing widely, in case any spacing or timing wants a small tweak once seen live.
- The skill tree and world map use fixed SVG `viewBox` coordinates; if you add many more nodes, you'll want to widen the `viewBox` rather than just adding nodes into the existing space.
- All puzzle, boss, quest, and character content is original creative writing for this fictional game — none of it is sourced from or references any existing copyrighted game, franchise, or third-party IP.

## 9. Credits

**Game design, narrative, curriculum mapping, and this document:** [THENUX](https://www.thenuxofc.store) — www.thenuxofc.store

---

*ROOTCODE is a design concept. This repository contains design documentation only — no game code, engine, or playable build.*
