# I Know a Place — Design System

**The Field Guide.** This is the system as actually built, not as proposed.
Every token, rule and ratio below is in `css/app.css` and can be checked
against it. Where a decision has a reason, the reason is here — that is the
point of the document.

For *why the redesign happened* and what shipped in which stage, see
[`VISUAL_REDESIGN_PLAN.md`](VISUAL_REDESIGN_PLAN.md).

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
| `--paper` | `#FBF7ED` | content plates, the map ground |
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

The map is a drawing, so it is drawn like one.

- **The coast** is projected from Toronto's real waterfront latitude
  (43.6355) rather than sitting at a guessed fraction of the canvas, and it
  carries the **only solid stroke on the map**. Everything else is dashed.
- **District names** sit at each neighbourhood's centroid in letterspaced
  caps. Neighbourhoods with more than one place earn a label, biggest first,
  and any label with no clear air around it is **dropped rather than crowded
  in** — so the densest part of the core stays unlabelled, which is honest.
  Hidden below 880px, where the crop would cut them mid-word.
- **Toronto Islands** is genuinely out in the lake, and at true scale it sits
  300 units south of everything else and squeezes the other 38 pins into the
  top third. A pass run *after* `project()` — the same way the collision
  relaxation pass is — compresses distance offshore. The projection function
  itself is untouched. The map says "not to scale" and means it.
- **The window on the field** is computed per viewport: the viewBox takes the
  stage's own aspect ratio, so map units land one-to-one on the element, and
  it is then positioned so the pin cluster is framed inside the part of the
  stage that is *actually free* — and, on a band taller than the fold, inside
  the part that is *actually on screen*. Below `K_MIN` it stops shrinking and
  starts cropping: 39 pins at 11px across is a texture, not a map.

### Markers

Colour **plus** the category glyph inside the disc, so category is never
carried by colour alone. Recommendation count is a badge beside the disc.
Each disc has a **paper rim**, so two overlapping pins read as two objects
and a pin over the lake keeps its edge.

| State | |
|---|---|
| Default | disc + glyph + count badge |
| Hover | halo, disc lifts, paper tag with the name |
| Selected | ink ring, halo, lifts, brought to the front, row scrolls into the index |
| Filtered out | **a small survey dot** — not a ghosted disc |

That last one matters. Ghosting left thirty grey blobs still competing for
attention; a dot reads as ground, so the matches are the only things on the
field with any weight, and the city keeps its shape.

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

A tapped pin raises the **peek card**: name, category, neighbourhood, rating,
who recommended it, a way through to the full entry and a Maps link. It is
the one raised surface on the page besides the modals, because it is
answering a tap and has to read as sitting on top of the map.

---

## 7. Motion

**One load moment, and after that motion only ever reports a change of
state.** Nothing loops, nothing drifts, nothing moves that the reader did not
ask to move.

- **Load** — the page arrives in the order you read it: masthead, then the
  field, then the pins landing on it, staggered 11ms apart so the last pin is
  down inside half a second. A 39-step queue would be a loading screen.
  Only the first render is an arrival; a pin re-landing on every keystroke
  would be motion reporting nothing.
- **State** — row expand, modal open, peek card, the mobile change-over.
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
- Markers are not in the tab order by design — 39 stops before reaching the
  index would be worse than the alternative, and every place on the map is
  reachable as a row with the same behaviour.

---

## 10. Rules that are settled

- **No dark mode.** Removed on purpose; not an open question.
- **No new runtime dependencies.** No framework, no build step, no CDN
  libraries beyond the Google Fonts stylesheet.
- **No Tailwind, no shadcn/ui, no React, no MapLibre.** Each was evaluated
  and declined; the reasons are in the plan.
- **No stock photography.**
- Data, the planner algorithm, `project()`, `escapeHtml()`, `starsHtml()`,
  the essay copy and the Google Maps URL construction are not visual
  territory. See §8 of the plan.
