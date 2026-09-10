# I Know a Place — Visual Redesign Plan

**Status:** stages 1–4a are **merged into `main` and live**. Stage 4b is
committed on `redesign/field-guide` and approved; stage 4c next.
**Last updated:** 2026-09-10

This is the living plan for the visual redesign. It supersedes the original
plan written at the start of the phase, which was revised mid-flight after a
course correction (see §2).

---

## 1. Context

The site worked but had no point of view. Data lives in `data/*.json`, the
map/list/filter/planner logic is sound, and there were no open bugs — what it
lacked was art direction.

This redesign is a **presentation-layer change only**. Application logic, data
and the planner algorithm are not rewritten. Two goals, in tension, both real:

- **1A — it must look genuinely impressive.** This is a portfolio piece.
- **1B — browsing the map and recommendations must stay useful and enjoyable.**

References: **Amici** = visual/emotional foundation, **KOBU** = restraint and
hierarchy. Neither is a map product, so their principles get translated rather
than their layouts.

### Decisions locked
| | |
|---|---|
| **Direction** | **A3 — Atlas Field with a header band** (see §3). |
| **Dark mode** | **Removed, permanently.** One committed light identity, so the site looks the same to every viewer regardless of their OS setting. Not to be re-added. |
| **Typefaces** | Instrument Serif + Instrument Sans — two families, one foundry. |
| **Ratings** | 5-star display stays, rebuilt as SVG. |
| **Friend identity** | Named contributors on collapsed rows — "*Ashton* + 2 friends". |
| **Mobile default** | List-first. Revisit after MapLibre. |
| **Map scale bar** | Cut. No implied precision on a schematic map. |
| **Contribute + City** | Visible now, both opening honest "being built" modals. |
| **Tailwind / shadcn** | **No.** See §7. |
| **Imagery** | At most two optional editorial moments, both outside the map and list. |
| **Process** | Hard approval gate after every stage: desktop + 375px screenshots, then stop. |

---

## 2. The course correction (why this plan was rewritten)

After stage 3 the site still read as the original prototype with new paint.
The diagnosis matters, because the cause was the plan itself:

1. **The original staging plan made it inevitable.** Work was split by
   *component* — tokens, chrome, list, map, mobile. Every stage was scoped to a
   component, so **no stage was ever allowed to touch the page's skeleton.**
   Stage 1 explicitly said "no layout change."
2. **The bones were untouched:** centred 1280px container, `main.split` at
   58/42, map in a bordered rounded box, controls stacked above.
3. **The Amici/KOBU weighting was backwards.** KOBU's restraint was applied to
   the *composition* and Amici was left decorating trim. It should be the
   reverse: Amici drives composition, KOBU disciplines the surface.
4. **Nothing bled, overlapped, or broke the grid.** Neither reference ever puts
   its hero in a bordered rounded rectangle.
5. **Three boxed `<select>`s and a boxed search input** sat in the most
   prominent position — the loudest generic signal on the page.

Three composition-level alternatives were mocked up with real data in a
scratchpad and compared as screenshots. **A (Atlas Field)** was chosen, then
refined into **A3** by moving identity into a header band.

> **Rule going forward:** every remaining stage is allowed to change
> composition. If a change is only materials, it is not enough.

---

## 3. The direction — A3, "Atlas Field with a header band"

```
┌──────────────────────────────────────────────────────────┐
│ I Know a Place.            (TORONTO)  why · contribute ▪ │  ← header band
│ Recommendations from people you actually trust           │    on --paper-2
├──────────────────────────────────────────────────────────┤  ← dashed rule
│  Schematic map, not to scale.        ┌─────────────────┐ │
│                                      │ 39 places       │ │
│      ·   ●    ·     ●                │ All Food Coffee │ │
│   ●     ·   ●    ●        ·          │ Price ▾ Sort ▾  │ │
│      ·    ●   ·      ●               │ Search…         │ │
│  ┌───────────┐                       ├─────────────────┤ │
│  │ legend    │  ~~~~~~~~~~~~~~~~~~~  │ Burger Drops ★★★│ │
│  └───────────┘  ▓▓ Lake Ontario ▓▓▓  │ Greg            │ │
└──────────────────────────────────────────────────────────┘
   map = full-bleed field, no border, no box
   index = paper panel floating over it, scrolls internally
```

**The thesis:** personality in the chrome, discipline in the working surface —
but the *composition* carries it, not the trim. The map stops being a widget
in a grid cell and becomes the ground the product stands on. Identity sits in
its own band above, so type never competes with the pin cluster.

**Amici → here:** the checkerboard border becomes a dashed route rule; the oval
"MENU" badge becomes the Toronto city stamp; the stickers become friend stamps
in expanded rows; the marquee becomes the neighbourhood ticker.
**KOBU → here:** one composition, no shadows except the floating panel, large
confident type, nothing decorative inside a row.

---

## 4. Design system (as built)

### Palette
Only two brand colours. **All other colour comes from the categories** — that
rule is what keeps it disciplined. Ratios measured against `--paper`.

| Token | Hex | Role | Contrast |
|---|---|---|---|
| `--paper` | `#FBF7ED` | content plates, map ground | — |
| `--paper-2` | `#F3EDDF` | chrome ground (masthead, footer) | — |
| `--paper-3` | `#EFE8D7` | recessed insets | — |
| `--ink` | `#1E241C` | primary text | **15.0:1** |
| `--ink-2` | `#55604F` | secondary text | **6.2:1** |
| `--ink-3` | `#676F60` | meta / tertiary | **4.9:1** |
| `--green` | `#2E5D3C` | buttons, links, selected | **7.2:1** |
| `--green-deep` | `#1D3B27` | display type | — |
| `--green-soft` | `#E4EBDE` | selected / hover fill | — |
| `--green-line` | `#C3CFBB` | hairlines, dashed rules | — |
| `--ochre` | `#B8863B` | stamps, marquee — **decorative only** | 3.0:1 |
| `--ochre-ink` | `#96662A` | text-safe ochre | **4.7:1** |
| `--star` | `#A8761F` | star fill | 3.7:1 |
| `--water-fill` | `#A9C6D4` | the lake | — |
| `--water-line` | `#4A7A90` | the coast stroke | 3.5:1 on `--water-fill` |
| `--water-ink` | `#2A4E60` | text on the water | **5.0:1 on `--water-fill`** |

**Categories:** Food `#A8412C` · Coffee `#8A5A22` · Culture `#1F6F63` ·
Entertainment `#35508F` · Nightlife `#6E3A86` · Outdoors `#5C8A2E` ·
Shopping `#A33461` · Wellness `#237286`. Outdoors sits clear of `--green` so the
brand is never read as a marker.

### Typography — two families, one job each
- **Instrument Serif** (400 + italic) — wordmark, section heads, city stamp,
  friend names, friends' quotes, search placeholder.
- **Instrument Sans** (400/500/600) — all UI, rows, controls, meta, essay body.

Instrument Serif ships **Regular only** — never request a heavier weight or the
browser synthesises a faux bold. No mono face: `font-variant-numeric:
tabular-nums` does the alignment job instead.

Scale: masthead `clamp(2.1rem, 3.6vw, 3.1rem)` · section head 1.5rem · row
title 1.125rem/600 · body 1rem @ 1.6 · meta 0.8125rem. Base 16px.

### Structure
- **Spacing** 4px base: `--s-1` 4 … `--s-9` 96.
- **Radius encodes what a thing is:** `--r-flat` 2px default, `--r-pill` 999px
  for pills/markers/stamps, `--r-sheet` 16px for modals and the mobile sheet.
- **Shadows:** none except the floating index panel and the modals.
- **Separators:** 1px `--green-line`; band breaks use the dashed route rule.
- **Selected row:** `--green-soft` fill + inset 2px `--ink` left rule.

### Controls — unboxed
Category is a row of words with a rule under the active one. Price, rating and
sort stay native `<select>` (the native control is right for them) reduced to an
underline and a caret. Search is a baseline-underlined field with a serif
italic placeholder. Default labels stay **"All activities" / "All prices" /
"All ratings"**.

### The row
```
◐  Pizzeria Badiali                    ★★★★★  ⌄
   Food · West Queen West
   Ashton + 2 friends          ← Instrument Serif italic
```
Hairline-separated, no cards. The toggle is a real `<button>` with
`aria-expanded`. Expanded rows show friend stamps (rotated 0.9° ochre hairline
tags) with each friend's own rating and their words in serif italic.

### The field
The map is a drawing, not a widget, so it is drawn like one:

- **The coast** is projected from Toronto's real waterfront latitude (43.6355)
  rather than sitting at a guessed fraction of the canvas, and it carries the
  **only solid stroke on the map**. Everything else is the dashed route rule.
- **District names** sit at each neighbourhood's centroid in letterspaced caps.
  Neighbourhoods with more than one place earn a label, biggest first; any
  label with no clear air around it is **dropped rather than crowded in**, so
  the densest part of the core stays unlabelled. Hidden below 880px.
- **Toronto Islands** is genuinely out in the lake, and at true scale it sits
  300 units south of everything else and squeezes the other 38 pins into the
  top third. A separate pass, run after `project()` exactly the way the
  relaxation pass is, **compresses distance offshore**. `project()` itself is
  untouched. The map says "not to scale" and means it.
- The ground is drawn well past the pins in every direction, because the
  visible window moves (below).

### The window on the field
`fitMapToViewport()` gives the viewBox the **stage's own aspect ratio**, so map
units land one-to-one on the element, then positions it so the pin cluster is
framed inside the part of the stage that is **actually free** — the index panel
floats over the right of the map, so the pins centre in what is left of it.
Below `K_MIN` the map stops shrinking and starts **cropping** instead: 39 pins
at 11px across is a texture, not a map.

It is measured from the element, so it is re-measured on webfont load and
through a `ResizeObserver`, not only on window resize.

### Markers
Colour **plus** the category glyph inside the disc, so category is never
communicated by colour alone. Recommendation count is a badge beside the disc.
Each disc carries a **paper rim**, so two overlapping pins read as two objects
and a pin over the lake keeps its edge.

States: default · hover (halo + lift + paper tag) · selected (ink ring, lift,
row scrolls into view) · **filtered out — a small survey dot, not a ghosted
disc.** Ghosting left thirty grey blobs still competing for attention; a dot
reads as ground, so the matches are the only things with weight on the field.

### The key
The legend is the map's key and follows the map's state: filter to one activity
and the rest of the key steps back rather than advertising colours that are no
longer on the field. Serif italic heading, hairline block, ochre dashed top
rule. The tooltip is a **paper tag** — the place in the display face, what and
where underneath — not a black chip.

### Ephemera budget
City stamp · dashed route rules · friend stamps · map legend block ·
neighbourhood marquee (ochre, stopped under `prefers-reduced-motion`) ·
expressive empty state and story modal.

**Caps:** nothing rotated more than 1.5°; never more than two expressive
elements in one viewport; **filters, buttons, selects, search and rows are
never stickers.**

---

## 5. Responsive

- **Desktop (>880px):** header band, full-bleed map stage
  `calc(100vh - 156px)`, index panel floating right.
- **Mobile (≤880px):** the stage unwinds — map becomes a band (52vh), legend
  and index run underneath on the page. Masthead wraps to its own lines.
- `fitMapToViewport()` frames the pins for whatever viewport it is given (see
  §4). Below 880px there is no panel to work around, so the whole width is
  free — but 375px is not enough for 39 readable pins, and the fit hits its
  clamp and crops. **The mobile map is cramped until stage 4c.**
- Touch targets ≥44px on nav links, primary button and the Maps link.

---

## 6. Stages

| Stage | What | Status |
|---|---|---|
| 0 | Baseline screenshots | ✅ |
| 1 | Tokens, two typefaces, one light theme, CSS → `css/app.css` | ✅ `da8cf73` |
| 2 | Masthead, paper grain, footer + marquee, Contribute + City modals | ✅ `5c91310` |
| 3 | Cards → hairline index rows, named contributors, SVG stars, friend stamps | ✅ `fc04b55` |
| **4a** | **Recompose around the map (A3)** | ✅ `0c3647b` |
| 4b | Map plate polish — viewport-aware framing, coast, districts, marker states, key | ✅ `8c50b51` |
| **4c** | **Mobile Map/List toggle + peek card ("A-lite")** | ▢ next |
| 5 | Modals restyled onto the system | ▢ |
| 6 | Motion pass — one load moment, state transitions only | ▢ |
| 7 | QA — contrast, keyboard, 375px + landscape, reduced-motion, Lighthouse | ▢ |
| 8 | *(optional)* Imagery: story modal image, then footer band | ▢ |
| 9 | Docs: `DESIGN_SYSTEM.md`, update `PROJECT_CONTEXT.md`, refresh `screenshot.png` | ▢ |

### 🚦 Approval gate — after every stage
1. Desktop screenshot 2. 375px mobile screenshot 3. What changed
4. **Stop and wait for approval.** No rolling into the next stage.

`PROJECT_CONTEXT.md` §9 records a previous redesign built end-to-end, reviewed
once at the finish and rejected wholesale with no diagnosis. The gate exists so
a wrong direction surfaces at stage 1, not stage 8.

**Screenshot method:** headless Chrome already on the machine
(`chrome --headless=new --screenshot`). Windows clamps window width to ~500px,
so **mobile shots must be rendered through a fixed 375px iframe wrapper** or
they come back misleadingly clipped. Verify overflow claims against
`scrollWidth` vs `clientWidth`, not against a screenshot.

---

## 7. Stack decisions

**Tailwind: no.** The token-based custom properties in `css/app.css` already
*are* the design system and are readable. Tailwind's payoff is consistency
across many component files; there is one page. It would also need a build step,
against the project's stated zero-dependency identity.

**shadcn/ui: no.** Evaluated properly — the same composition was built twice,
once in this art direction and once in shadcn's default idiom, and compared as
screenshots (`mockup-a3-header.html` vs `mockup-a4-shadcn-idiom.html`). Three
reasons it loses here:
1. It is React + Tailwind + Radix with a build step; "replace all the UI" means
   rewriting the map, list and planner as React components.
2. Its value is a deliberately **neutral default** aesthetic — that is the point
   of it.
3. That is precisely the problem this redesign exists to fix. The brief lists
   "component-library-demo aesthetics" under Avoid.

It remains a strong choice for a future project with forms and dashboards.

---

## 8. Do not touch

- `data/places.json`, `data/recommendations.json` — no value, key or wording changes.
- Planner algorithm: `scoreOf`, `seededRandom`, `weightedPick`, `kmeansCluster`,
  `fillBucket`, `buildPlan` and its Nightlife / back-to-back-Food special cases.
- `project()` and the collision-relaxation maths (constants may be retuned; the
  algorithm may not). The offshore-compression pass added in 4b sits *after*
  `project()` as its own named step for exactly this reason — new passes are
  fine, rewriting the projection is not.
- `escapeHtml()` and every call site.
- `starsHtml(value, size)` signature, its 0–10 → 5-star mapping, its `aria-label`.
- The "Why this exists" essay copy, signature and LinkedIn URL.
- Dropdown default labels.
- Google Maps URL construction.
- `refresh()` / `selectPlace()` control flow — restyle their output.
- `.gitignore`, including the `*.xlsx` exclusion.
- No new runtime dependencies. No MapLibre yet. No contribution form or backend.
  No multi-city data model. No stock photography.

---

## 9. Verification

Per stage, with `npx serve` (or the `.claude/launch.json` preview) running:

- Filters (category row, price, rating), search, all four sorts, empty state.
- Row expand, marker → row and row → marker sync, tooltip placement.
- Google Maps links present on every row.
- Story modal, planner form → 3 plans → shuffle → back, Contribute and City
  modals — all trapping focus and returning it to the trigger.
- Console clean.
- Contrast: every text/background pair ≥ 4.5:1; marker glyphs ≥ 3:1 on their disc.
- Keyboard: tab the whole page; Escape closes modals; nothing focused is hidden
  behind the panel.
- Re-check with `prefers-reduced-motion: reduce`.
