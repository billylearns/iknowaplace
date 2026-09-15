# I Know a Place — Design System

**The Field Guide.** This is the system as actually built, not as proposed.
Every token, rule and ratio below is in `css/app.css` and can be checked
against it. Where a decision has a reason, the reason is here — that is the
point of the document.

For *why the redesign happened* and what shipped in which stage, see
[`VISUAL_REDESIGN_PLAN.md`](VISUAL_REDESIGN_PLAN.md). For the later move from
a drawn schematic map to a real street map, see
[`REAL_MAP_PLAN.md`](REAL_MAP_PLAN.md).

---

## 1. The thesis

**Personality in the chrome, discipline in the working surface — and the
composition carries it, not the trim.**

The map is not a widget in a grid cell; it is the ground the product stands
on. Identity sits in a band above it so type never competes with the pin
cluster, and the index floats over the field on paper. References were
**Amici** (personality, warmth) and **KOBU** (restraint, hierarchy),
translated rather than copied.

Three rules do most of the work:

1. **Two brand colours, and all other colour comes from the categories.**
2. **Hairlines, not boxes.** A stack of bordered cards makes you re-enter a
   container on every item.
3. **Radius encodes what a thing is,** not taste.

---

## 2. Colour

One light theme. **Dark mode was removed deliberately and is not coming
back** — a portfolio piece should look the same to everyone who opens it,
whatever their OS is set to.

### Grounds

| Token | Hex | Role |
|---|---|---|
| `--paper` | `#FBF7ED` | content plates, the map's ground before tiles arrive |
| `--paper-2` | `#F3EDDF` | chrome: masthead, footer, the mobile switch |
| `--paper-3` | `#EFE8D7` | recessed: insets, notes, the story plate |
| `--card` | `#FFFCF5` | raised: modals and the mobile peek card only |

### Ink and brand

Ratios are measured against `--paper` unless stated.

| Token | Hex | Role | Contrast |
|---|---|---|---|
| `--ink` | `#1E241C` | primary text | **15.0:1** |
| `--ink-2` | `#55604F` | secondary text | **6.2:1** |
| `--ink-3` | `#636B5C` | meta / tertiary | **5.2:1** |
| `--green` | `#2E5D3C` | buttons, links, anything selected | **7.2:1** |
| `--green-deep` | `#1D3B27` | display type | 11.5:1 |
| `--green-soft` | `#E4EBDE` | selected row / hover fill | — |
| `--green-line` | `#C3CFBB` | separators — decorative only | 1.5:1 |
| `--line-control` | `#788870` | the edge of a control | **3.1:1** |
| `--ochre` | `#B8863B` | stamps, marquee, route dashes — **never text** | 3.0:1 |
| `--ochre-ink` | `#8A5C25` | the text-safe ochre | **5.4:1** |
| `--star` | `#A8761F` | star fill (a graphic, 3:1 bar) | 3.7:1 |
| `--water-fill` | `#A9C6D4` | the lake |
| `--water-line` | `#3C6A80` | the coast stroke | 3.3:1 on `--water-fill` |
| `--water-ink` | `#2A4E60` | text on the water | **5.0:1 on `--water-fill`** |

**Two hairline tokens, on purpose.** A separator only has to be *seen*;
the edge of a control has to be *found*, which is a 3:1 job. One value
cannot do both without making every rule on the page shout. `--green-line`
separates; `--line-control` bounds anything you can click or type into.

`--ink-3`, `--ochre-ink` and `--water-line` were all darkened in the QA pass:
each cleared its bar on `--paper` and missed it on one of the other grounds
the same text actually sits on.

### Categories

The only other colour in the interface.

| Food | Coffee | Culture | Entertainment |
|---|---|---|---|
| `#A8412C` | `#8A5A22` | `#1F6F63` | `#35508F` |

| Nightlife | Outdoors / Nature | Shopping | Wellness |
|---|---|---|---|
| `#6E3A86` | `#5C8A2E` | `#A33461` | `#237286` |

White glyphs sit on these at 4.1–8.0:1, clear of the 3:1 bar for graphics.
**Outdoors sits well clear of `--green`** so the brand is never read as a
marker.

---

## 3. Type

**Two families from one foundry, one job each.** No third face, and no mono:
`font-variant-numeric: tabular-nums` does the alignment job instead.

- **Instrument Serif** — wordmark, section and modal heads, city stamp,
  friend names and their words, plan titles, search placeholder, map labels.
- **Instrument Sans** — all UI, index rows, controls, meta, essay body.

> **Instrument Serif ships Regular only.** Never request a heavier weight or
> the browser synthesises a faux bold.

Scale — base 16px:

| | |
|---|---|
| Masthead wordmark | `clamp(2.1rem, 3.6vw, 3.1rem)` |
| Modal head | 1.75rem |
| Section head / panel head | 1.5rem |
| Plan title | 1.375rem |
| Row title | 1.125rem / 600 |
| Body | 1rem / 1.6–1.7 |
| Meta | 0.8125rem |
| Section markers | 0.625rem, `letter-spacing: .18em`, uppercase |

---

## 4. Structure

- **Spacing** — 4px base: `--s-1` 4 … `--s-9` 96.
- **Radius encodes what a thing is:** `--r-flat` 2px is the default,
  `--r-pill` 999px is for pills / markers / stamps, `--r-sheet` 16px is for
  modals and the mobile peek card. Nothing else picks a radius.
- **Elevation** — nothing floats except the index panel, the modals and the
  peek card. Everything else is flat on paper.
- **Separators** — 1px `--green-line`; band breaks use the dashed route rule.
- **Paper grain** — one tiled inline SVG noise at 5.5% over the whole page.
  No image file, no extra request; it is what stops large flat areas reading
  as screen-flat.

---

## 5. The map

A real street map, so it has to be a *quiet* one: the recommendations are the
subject and the city is the ground they stand on.

- **Rendering and data.** MapLibre GL JS (v5, pinned, one `<script>` tag)
  drawing OpenFreeMap's public vector tiles. No API key, nothing self-hosted,
  no build step. Every place sits at its true coordinates — nothing is
  projected, squashed or nudged apart.
- **The style is ours.** [`map/ikap-atlas.json`](../map/ikap-atlas.json) is
  OpenFreeMap Positron, forked and owned. It still draws OpenFreeMap's tiles,
  fonts and sprites; only the look lives in the repo. Its provenance is in
  the style's own `metadata`. Edit it directly or open it in Maputnik — do
  not re-fetch upstream over it.
- **Nothing on the base map is `--green`.** A park must never read as the
  brand or as an Outdoors marker.
- **Quiet at city zoom, detailed at street level.** Side streets and building
  footprints sit barely above the land when the whole city is on screen and
  come up as you zoom in. Arterials stay readable throughout.
- **Removed from the base map:** boundaries, road shields, country and
  province labels, footpath names, and the city's own name once the city
  fills the screen (it sat right on top of the downtown pins).
- **Neighbourhood names** are letterspaced capitals, as the drawn map's
  district labels were, and now appear on mobile too.
- **North is up, always.** Rotation and pitch are disabled — rotating a city
  map only makes its street names harder to read.
- **Labels are Noto Sans**, OpenFreeMap's glyph set. The Instrument faces
  would need self-hosted glyph files; map labels are cartography, not brand
  type, and a neutral sans is right there.

### Map palette

Derived from the tokens in §2; the hex values live in the style file.

| Layer | Colour | Note |
|---|---|---|
| Land | `#F2ECDF` | a shade under `--paper`, so warm-white streets read against it |
| Water | `#A9C6D4` | `--water-fill` — visibly blue |
| Parks | `#DDE3CD` | pale sage, well clear of `--green` |
| Arterials | `#FFFCF5` on `#DDD2BC` | `--card` with a warm casing |
| Motorways | `#FAF1DC` on `#CDBD9C` | a faint ochre, so the Gardiner is findable |
| Street names | `#636B5C` | `--ink-3` — **4.7:1** on land |
| Neighbourhoods | `#55604F` | `--ink-2` — **5.6:1** on land, 5.0:1 on park |
| Water names | `#2A4E60` | `--water-ink` — **5.0:1** on water |

### Framing and the camera

- **The whole city is framed in the part of the map you can see.** On
  desktop the index panel covers the right of the map and the camera is
  padded by it; on a phone the peek card counts the same way.
- **Selecting a place moves the camera only if it has to.** If the pin is
  already somewhere visible — not under the panel, not under the card — the
  map stays still. Otherwise it eases the pin into the free area.
- **Once someone has moved the map, it is theirs.** No automatic re-framing
  takes it back.
- **Controls are deliberately few:** zoom, where-am-I, and a *Recentre* word
  that returns to all 39 places. No compass (there is no rotation to undo)
  and no scale bar. They are paper and hairline, not MapLibre's white chips:
  top-left on desktop, where the panel isn't; the right edge at 44px on a
  phone.
- **Attribution is always readable.** On desktop it sits just left of the
  index panel; on a phone it rises above the peek card while one is open.

### Markers

Ordinary HTML positioned by MapLibre, not a sprite layer — so colour, glyph,
rim and badge stay in CSS where this system can reach them. One custom
property per marker (`--cat`) carries the category colour to every part.

Colour **plus** the category glyph inside the disc, so category is never
carried by colour alone. Recommendation count is a badge beside the disc.
Each disc has a **paper rim**, so two overlapping pins read as two objects
and a pin over the lake keeps its edge.

**Size follows zoom, a band at a time.** A pixel is not a fixed number of
metres: framed on the whole city one pixel is about twelve metres, so a
full-size disc would cover a third of a kilometre and downtown would be one
shape. Every part of the marker is measured from one size variable:

| Band | Zoom | Disc | |
|---|---|---|---|
| `wide` | < 12.6 | 15px | pulled back past the city; count badge hidden |
| `far` | 12.6–14.2 | 21px | **the whole-city fit — what people see first** |
| `mid` | 14.2–15.4 | 26px | |
| `near` | ≥ 15.4 | 30px | street level |

**Overlap is not hidden.** Places ten metres apart share a spot on the map
until you zoom in, because that is the truth. Pins stack south-over-north;
hovered and selected pins come to the front. There is no clustering: at 39
places a cluster bubble would hide more than it reveals.

| State | |
|---|---|
| Default | disc + glyph + count badge |
| Hover | halo, disc lifts, paper tag with the name |
| Selected | ink ring, halo, lifts, brought to the front, row scrolls into the index, camera reveals it if hidden |
| Filtered out | **a small survey dot** — not a ghosted disc |

That last one matters. Ghosting left thirty grey blobs still competing for
attention; a dot reads as ground, so the matches are the only things on the
map with any weight, and the city keeps its shape. **A selection that a
filter hides is released**, rather than surviving invisibly.

### When the map can't be a map

The index has never depended on the map, and no failure changes that.

| What failed | What you see |
|---|---|
| Still loading | "Drawing Toronto…" in the display face; after ten seconds, a line saying the index works now |
| OpenFreeMap tiles | The pins stay, on paper, in the right places relative to each other, under a note: *The street map didn't load* |
| MapLibre, our style file, or WebGL | The map is withdrawn with a short note pointing at the index; on a phone the switch goes too, since there is no second half to switch to |
| The place data | *No places to map*, beside the existing data-error message |

### The key

The legend is the map's key, so it follows the map's state: filter to one
activity and the rest of the key steps back rather than advertising colours
that are no longer on the field.

---

## 6. Components

### The index row

```
◐  Pizzeria Badiali                    ★★★★★  ⌄
   Food · West Queen West
   Ashton + 2 friends          ← Instrument Serif italic
```

Hairline-separated, no cards. The collapsed row **names a friend** rather
than reporting a count — "Ashton + 2 friends" reads as people, "Recommended
by 3" reads as a statistic, and people are the whole point of the site. The
toggle is a real `<button>` with `aria-expanded`. Expanded rows show friend
stamps (rotated 0.9° ochre tags) with each friend's own rating and their
words in serif italic.

Two kinds of fact get two lines. One grey middle-dot string flattens them
together — the same rule applies to plan items in the planner.

### Controls, unboxed

Category is a row of words with a rule under the active one. Price, rating
and sort stay native `<select>` — the native control is right for them — but
lose their boxes down to an underline and a caret. Search is a
baseline-underlined field with a serif italic placeholder.

Default labels are **"All activities" / "All prices" / "All ratings"**.
Not "Activity" / "Price" / "Rating": a dropdown reading "Activity" doesn't
say *no filter applied*.

### Modals

Sheets of the same paper: `--card` ground, hairline border, `--r-sheet`,
serif head over a dashed rule. **One selected state everywhere** — green
fill, paper ink. The planner previously used near-black for pills and green
for cards, which read as two different kinds of chosen.

Field labels are section markers and carry a dashed rule out to the edge.
Plans are hairline lists, not stacked boxes, with the plan letter as an ochre
mark beside a serif title.

### The mobile stage

Below 880px the map and the index **take turns**, because there is no room to
show a map and a 39-row index at once and stacking them puts the index a
scroll away from the pin you just tapped. A sticky two-word switch chooses.
**The index is the default** — the map is the beautiful part, the list is the
useful one on a phone.

Tapping **Map** brings the switch to the top of the screen, and the map
takes exactly the rest of it — measured from the switch, not guessed. The key
follows a short scroll below.

A tapped pin raises the **peek card**: name, category, neighbourhood, rating,
who recommended it, a way through to the full entry and a Maps link. It is
the one raised surface on the page besides the modals, because it is
answering a tap and has to read as sitting on top of the map. A pin low on
the screen is lifted clear of the card. In a short landscape screen the card
runs down the right side instead of across the bottom, and the controls move
to the left. Everything at the map's edges keeps clear of notches and the
home indicator.

---

## 7. Motion

**One load moment, and after that motion only ever reports a change of
state.** Nothing loops, nothing drifts, nothing moves that the reader did not
ask to move.

- **Load** — the page arrives in the order you read it: masthead, then the
  map, then the pins landing on it, staggered 11ms apart so the last pin is
  down inside half a second. A 39-step queue would be a loading screen.
  Only the first render is an arrival; a pin re-landing on every keystroke
  would be motion reporting nothing. **The arrival is opacity only** —
  MapLibre positions each pin with a transform, so an arrival that animated
  transform would land the pin off its street.
- **State** — row expand, modal open, peek card, the mobile change-over,
  and the camera easing to a selected place when it has to.
- Camera moves use the same rule as scrolling: **instant under reduced
  motion.**
- Everything is neutralised by a single `prefers-reduced-motion: reduce`
  block at the top of `css/app.css`, and the marquee stops there outright.

---

## 8. Ephemera budget

City stamp · dashed route rules · friend stamps · the map key ·
neighbourhood marquee · the story plate · expressive empty state.

**Caps:** nothing rotated more than 1.5°; never more than two expressive
elements in one viewport; and **filters, buttons, selects, search and rows
are never stickers.**

Imagery is **drawn, never photographed**. A stock photo of a city would be
someone else's Toronto. The story plate uses the site's own map vocabulary —
coast, route dash, three pins, a bearing — once, at the head of the only long
piece of writing on the site.

---

## 9. Accessibility, as verified

- Every text/background pair in §2 is **≥ 4.5:1**; every graphic that carries
  meaning is **≥ 3:1**.
- **The map meets the same bar.** Street names 4.7:1, neighbourhood names
  5.6:1, water names 5.0:1. Every marker colour is ≥ 3:1 on land, park and
  water alike — the lowest is Wellness on water at 3.06:1. (Land, water and
  park fills against each other are ground, not meaning; water is named.)
- A skip link jumps past the map to the index — a map is a lot of page to tab
  through to reach the thing you came for.
- Modals trap focus, close on Escape and return focus to the trigger. Focus
  is taken **synchronously** on open; it used to wait for an animation frame,
  and the frame came while the focus did not, which left keyboard users
  tabbing the page behind an open dialog.
- Category is carried by a glyph as well as by colour.
- Tap targets are ≥ 34px on desktop and ≥ 40px on mobile, ≥ 44px on nav
  links, primary buttons, Maps links and the mobile switch.
- No horizontal overflow at 375, 812 (landscape), 1024 or 1440.
- **Markers are not in the tab order and are hidden from screen readers**, by
  design — 39 stops before reaching the index would be worse than the
  alternative, and every place on the map is reachable as a row with
  everything said about it. The map itself is a labelled, focusable canvas:
  arrow keys pan, plus and minus zoom. Its controls are real buttons.
- Map controls are 36px on desktop and 44px on a phone.

---

## 10. Rules that are settled

- **No dark mode.** Removed on purpose; not an open question.
- **One runtime library: MapLibre GL JS**, loaded from a pinned version, for
  the map. Tiles from OpenFreeMap's public instance. No Mapbox, no Google
  Maps base map, no API keys, no self-hosting. Otherwise: no framework, no
  build step, no CDN libraries beyond the Google Fonts stylesheet.
- **No Tailwind, no shadcn/ui, no React.** Each was evaluated and declined;
  the reasons are in the redesign plan. (MapLibre was declined during the
  redesign too — for a *drawn* map. When the map became a real one it was the
  right tool, and the reasons are in [`REAL_MAP_PLAN.md`](REAL_MAP_PLAN.md).)
- **The map's look lives in `map/ikap-atlas.json`, not in JavaScript.** No
  runtime `setPaintProperty` restyling.
- **No stock photography.**
- Data, the planner algorithm, `escapeHtml()`, `starsHtml()`, the essay copy
  and the Google Maps URL construction are not visual territory. Coordinates
  are never changed without the owner's approval.
