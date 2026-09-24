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
cluster. Since the polish pass the map and the list share the screen as
equals-with-a-lead: the map is a rounded card taking about 70% of the width,
the recommendations one column beside it. References were
**Amici** (personality, warmth) and **KOBU** (restraint, hierarchy),
translated rather than copied.

Since the 2026-09 polish pass the target is **modern interaction design
with personal editorial character**: Airbnb as the reference for layout,
spacing, rounded controls and interaction hierarchy; Amici for personality
(expressive type accents, the checker strip, photos placed rather than
framed). Functional UI is soft, rounded and quiet; the personality comes from
typography, imagery and a few accents — never from making controls messy.

Three rules do most of the work:

1. **Two brand colours, and all other colour comes from the categories.**
2. **Whitespace and soft elevation, not boxes.** Not every element needs a
   container; spacing, type hierarchy and a low shadow do the work.
3. **One rounded family.** Pills for buttons, a medium radius for cards, a
   large one for sheets and panels.

---

## 2. Colour

One light theme. **Dark mode was removed deliberately and is not coming
back** — a portfolio piece should look the same to everyone who opens it,
whatever their OS is set to.

### Grounds

| Token | Hex | Role |
|---|---|---|
| `--paper` | `#FDFAF4` | content plates, the map's ground before tiles arrive |
| `--paper-2` | `#F9F5EC` | the page: masthead, the mobile switch |
| `--paper-3` | `#F2EDE2` | recessed: chips, hover fills |
| `--card` | `#FFFEFB` | raised: panels, popovers, modals, the peek card |
| `--line-soft` | `#E6E0D2` | hairlines on the lighter grounds |

The grounds were lifted about two shades in the polish pass — the old chrome
ground read as dull. They stay warm; the page is never stark white.

### Ink and brand

Ratios are measured against `--paper` unless stated. The lighter grounds
only raise them: `--ink-3` is 5.1:1 on `--paper-2` and 4.75:1 on `--paper-3`.

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
| `--ochre` | `#B8863B` | accents and marks — **never text** | 3.0:1 |
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

- **Instrument Serif** — wordmark, section and modal heads, plan titles, and
  the expressive accents: the tagline, **activity type and neighbourhood**
  ("*Food · Liberty Village*"), the signature, the "More cities coming soon!"
  note. Italic is the expressive voice.
- **Instrument Sans** — everything functional: friend names, what friends
  wrote, prices, time, energy, buttons, navigation, filters, search, and the
  essay body.

**Readable first.** Functional information is never set in the italic
display face. What friends wrote used to be serif italic at 1.125rem and was
hard to read; it is Instrument Sans at 0.9375rem / 1.6 now, the same voice as
the essay.

> **Instrument Serif ships Regular only.** Never request a heavier weight or
> the browser synthesises a faux bold.

Scale — base 16px:

| | |
|---|---|
| Masthead wordmark | 2.25rem desktop · `clamp(1.55rem, 6.8vw, 1.95rem)` phone |
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
- **Radius — one soft family:** `--r-sm` 10px (inputs, selects, map
  controls, menu items), `--r-md` 16px (cards, rows, popovers, peek card),
  `--r-lg` 24px (modals, panels, the map card), `--r-pill` (buttons, chips,
  markers). The old 2px `--r-flat` default is retired.
- **Elevation** — `--shadow-1` lifts a control off the map; `--shadow-2` is
  for things floating over the page (popovers, panels, modals). Low and soft,
  never a hard outline.
- **Separators** — 1px `--line-soft`. The dashed route rules are gone; the
  checker strip is the one decorative band.
- **The app shell** — checker strip, masthead, then the stage takes exactly
  the rest of the window (`100dvh`). Nothing lives below it: no footer, no
  stats, no marquee.
- **Paper grain** — one tiled inline SVG noise at 4% over the whole page.
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
| Land | `#F7F3EA` | lifted a shade in the polish pass; still just under `--paper` |
| Water | `#B4CEDA` | `--water-fill` — visibly blue, a step softer |
| Parks | `#E3E9D5` | pale sage, well clear of `--green` |
| Buildings | `#EEE8DA` | |
| Arterials | `#FFFEFA` on `#DDD2BC` | near-white with a warm casing |
| Motorways | `#FAF1DC` on `#CDBD9C` | a faint ochre, so the Gardiner is findable |
| Street names | `#636B5C` | `--ink-3` — **5.0:1** on land |
| Neighbourhoods | `#55604F` | `--ink-2` — 5.3:1 on park |
| Water names | `#2A4E60` | `--water-ink` — **5.4:1** on water |

The land colour is shared by residential areas, piers and the gaps in the
rail dashes, and the minor-road ramp starts from it — change them together.

### Framing and the camera

- **The whole city is framed in the part of the map you can see.** On
  desktop nothing covers the map any more (the list sits beside it), so the
  camera is only padded clear of the button row along the top; on a phone
  the peek card counts as covered.
- **Selecting a place moves the camera only if it has to.** If the pin is
  already somewhere visible — not under the card — the map stays still.
  Otherwise it eases the pin into the free area.
- **The list follows the map.** Filters decide which places exist (they
  drive the markers); the map's viewport decides which of those the list
  shows. Zoom into a neighbourhood and the list is that neighbourhood —
  "12 places in this area" — zoom out and it is the city again. It updates
  once the map settles (`moveend`, debounced), and only rebuilds when the
  set of places actually changed. Search is a filter, so it is held to the
  visible map too. The selected place stays listed even when panned out of
  view. With no usable map — loading, failed, or hidden — there is no
  viewport, and the list is every match. A viewport with nothing in it says
  so and offers **Zoom out to all of Toronto**.
- **Full-screen map.** A round expand button at the map's top-left folds
  the list column away (the map card widens, the masthead stays); it becomes
  a **Show list** pill, and Escape also brings the list back.
- **Once someone has moved the map, it is theirs.** No automatic re-framing
  takes it back.
- **Controls are deliberately few:** zoom, where-am-I, and a *Recentre*
  pill that returns to all 39 places. No compass (there is no rotation to
  undo) and no scale bar. They are card-coloured squircles with a soft
  shadow and no outline. Layout on desktop: expand top-left, **Filters**
  top-right with the map's own controls stacked under it, *Why this exists*
  bottom-left, attribution bottom-right.
- **Attribution is always readable.** On desktop it sits in the map card's
  bottom-right corner; on a phone it rises above the peek card while one is
  open.

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

### No key

The map legend was removed in the polish pass: the glyph in every marker and
the activity named on every row carry category on their own.

---

## 6. Components

### The index row

```
◐  Pizzeria Badiali                    ★★★★★  ⌄
   Food · West Queen West      ← Instrument Serif italic, 0.9375rem
   Recommended by Ashton + 2   ← Instrument Sans, name in 600
```

The collapsed row **names a friend** and says what they did — "Recommended
by Ashton + 2". A bare name gave no context; "Recommended by 3 friends"
reads as a statistic, and people are the whole point of the site. The toggle
is a real `<button>` with `aria-expanded`. Expanded rows show price, time
and energy as small chips (0.75rem) on one line (all 39 fit at 375px).
Price is shown as the plain range - "$50–100", "Under $20", "$100+",
"Free" - derived from the data's label by `priceText()`; the dollar-sign
tier only restated the range next to it. Then come each friend's name (plain, 600) with their own
rating and their words in the readable body face.

Two kinds of fact get two lines. One grey middle-dot string flattens them
together — the same rule applies to plan items in the planner.

### The recommendations column

Recommendations only: a count heading in semibold sans ("39 places" /
"12 places in this area"), then the rows. No box, no filters. Rows are soft
tiles separated by whitespace — a tile only shows on hover (`--paper-3`) or
when selected (`--green-soft`). The column is `clamp(384px, 30%, 480px)`
wide, which keeps price, time and energy on one line at every width.

### Filters

Every filter lives behind one **Filters** pill (sliders icon, a green count
badge when any are on). On desktop it sits on the map's top-right and opens
an anchored panel; on a phone it opens the same panel as a sheet from the
bottom. Inside: search; activity as chips (with each category's glyph);
**maximum price** as chips in the same words the recommendations use — Any,
Free, Under $20, $20–50, $50–100 (a ceiling, as it always was); rating and
sort as rounded selects; *Clear all*. Filters apply as they change; the
panel's **Show N places** button just closes it. Sort does not count
towards the badge.

Default labels are **"All activities" / "Any" / "All ratings"** — each
says *no filter applied*.

Plan my trip's *Maximum budget* uses the same five ranges (Free … $100+).

### The masthead

A checker strip (two rows of 8px checks, `--green-deep` on `--paper`, pure
CSS) sits along the very top on every screen — the only checkerboard on the
site. Below it, on desktop, one compact band (~70px): wordmark and the
tagline on a single line; then the Toronto selector, Contribute a place and
a **Plan my trip** pill. On desktop **Why this exists** is a small floating
pill in the map's bottom-left corner - the one corner the map's own
controls don't use. On a phone it is
its own centred treatment: wordmark and tagline about a fifth smaller, then
one row — *Why this exists · Contribute a place* as quiet links and Plan my
trip as a small pill.

**The city selector** reads "Toronto ⌄" and opens a small popover menu:
Toronto (ticked) and a muted *More cities coming soon!*. Popovers are
anchored panels, not dialogs — Escape, clicking elsewhere or tabbing out
closes them and returns focus to the trigger.

### Modals

Sheets on `--card`: no border, `--r-lg`, a soft shadow, the serif head over a
hairline. Buttons and option pills are pills; time-option cards use `--r-md`.
**One selected state everywhere** — green fill, paper ink.

**Why this exists** is an editorial spread: three phone photos (9:16, white
print border, a small tilt, a strip of tape) dropped on the left and hanging
slightly off the sheet's edge, the essay beside them at 0.875rem. It fits a
1280×800 screen without scrolling inside itself. The photos are placeholders
until the real ones arrive — each `<figure>` takes an `<img>` in place of its
placeholder span. The planner previously used near-black for pills and green
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
  block at the top of `css/app.css`.

---

## 8. Ephemera budget

The checker strip · the italic tagline and activity/neighbourhood lines ·
the story photos · the signature · expressive empty state.

**Caps:** nothing rotated more than 1.5°; never more than two expressive
elements in one viewport; and **filters, buttons, selects, search and rows
are never stickers.**

**No stock photography.** A stock photo of a city would be someone else's
Toronto. The only photographs are the owner's own, in the story.

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
- Tap targets are ≥ 34px on desktop and ≥ 40px on mobile, ≥ 44px on
  primary buttons, Maps links and the mobile switch. The compact phone
  masthead is the deliberate exception: its three actions are 34–36px tall
  (above the 24px WCAG 2.2 minimum) so the header stays one row.
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
- **No stock photography.** The owner's own photos, in the story, only.
- Data, the planner algorithm, `escapeHtml()`, `starsHtml()`, the essay copy
  and the Google Maps URL construction are not visual territory. Coordinates
  are never changed without the owner's approval.
