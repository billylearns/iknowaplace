# I Know a Place — Real Map Plan

**Status:** **complete.** Stages 1–4 are done. The map as built is written up
in [`DESIGN_SYSTEM.md`](DESIGN_SYSTEM.md) §5.
**Last updated:** 2026-09-16

This records the phase that replaced the drawn, schematic SVG map of Toronto
with a real street map: what was decided, why, and what shipped in which
stage. It follows [`VISUAL_REDESIGN_PLAN.md`](VISUAL_REDESIGN_PLAN.md), and
overrides one decision recorded there.

---

## 1. Context

The redesign treated the schematic map as a feature. It was a good drawing —
a projected coast, district names, pins pushed apart so none overlapped — but
it could tell you *that* there were six places in King West and not *where*
King West was, how far Ossington is from Chinatown, or what street anything
was on. The collision-relaxation pass made it actively misleading: two places
ten metres apart were drawn forty units apart. Every place already had real
coordinates; the only thing reading them was the projection.

The goal was a genuine street map that keeps the product around it intact:
list ↔ map selection, filters, search and sort, the Google Maps links, the
mobile Index/Map switch and the peek card.

## 2. Decisions

| Decision | Choice | Why |
|---|---|---|
| Renderer | **MapLibre GL JS v5.24.0**, pinned, one `<script>` tag | v6 is ESM-only and needs WebGL2; the page is one plain script, and v5's UMD build drops in unchanged and still falls back to WebGL1. OpenFreeMap documents v5. |
| Tiles | **OpenFreeMap**, public instance | No key, no account, no limits, no self-hosting. |
| Not used | Mapbox, a Google Maps base map, React, a build step, a database | Out of scope by the owner's brief, and none was needed. |
| Starting style | **Positron** | The quietest of OpenFreeMap's styles, already without POI clutter. Liberty is better maintained but labels every shop. |
| Style ownership | Hosted Positron for stages 1–2, then **forked into `map/ikap-atlas.json`** | Owned, versioned, immune to upstream changes, opens in Maputnik. Valid JSON; provenance in the style's `metadata`. No runtime restyling in JS. |
| Markers | **HTML markers**, not a symbol layer | Keeps the design language in CSS. A symbol layer would need every state baked into sprites. |
| Overlap | **Not hidden, not clustered** | Pins sit on their buildings. Zoom-banded sizing, stacking order and camera reveal do the work. At 39 places a cluster hides more than it shows. |
| List → map | **Ease only if needed** | The camera moves only when the selected pin is off screen or under the panel or card. |
| Controls | Zoom, where-am-I, Recentre | No compass (no rotation), no scale bar, no fullscreen. |
| Keyboard | Markers out of the tab order and hidden from screen readers | The index is the accessible path to every place; the canvas pans and zooms from the keyboard. |
| Location confidence | Copied verbatim from the retired workbook | 29 High, 10 Medium. Nothing inferred. One coordinate corrected afterwards, with approval - see §3. |

## 3. Data

`data/places.json` gained two fields on all 39 places, copied read-only from
`I_Know_A_Place_Base44_Import.xlsx` (still outside the repo and gitignored):

- `location_confidence` — `"High"` or `"Medium"`.
- `location_note` — the workbook's own sentence, or `""`.

The ten Medium places are the ones where the original recommendation named a
business without saying which branch (and Pedal Pub, which moves). Two High
places carry notes about hours and pricing rather than location; they were
copied as they were. That change was additions only, and every workbook
coordinate matched the JSON to nine decimal places.

### The Medium ten, audited (2026-09-16)

Each of the ten was checked against OpenStreetMap - what building the pin
lands on, and where every branch of that business actually is - and, where
OSM had no name at the point, against the business's own listings.

**Nine were already on the right building and were left alone.** Where a
business has several branches, the pin is on the one that matches the
neighbourhood recorded for the place: Bagels on Fire on Queen West (the other
is in the Beaches), Dark Horse on John Street, Neo Coffee Bar at 161
Frederick, Evviva on Lower Simcoe, Columbus Café in the PATH. Jaybird looks
wrong at first glance - OpenStreetMap names its pin "Jimmy's Coffee" - but
the studio is the second floor of that same building, so the ambiguity is
vertical and a pin cannot express it. Koh Lipe, Mizzica and Othership
Adelaide turned out to have no branch to be unsure about; they are still
marked Medium because that is what the workbook said.

**One was wrong and was corrected, with the owner's approval.** Pedal Pub
Toronto was pinned at Stackt Market, 28 Bathurst Street. The operator departs
from Chefs Hall, 121 Richmond Street West, Unit 101 - about 1.6km east, and
confirmed on Google Maps. Moving the pin also meant changing that place's
`address_or_location`, its Google Maps URL (built from name + address) and
its neighbourhood, which was "Bathurst Quay" and is now Financial District.
It stays **Medium**: the departure point is knowable, but the tour moves.

**In the interface** the ten Medium places carry one quiet line in the
expanded index row and the peek card: *Approximate location — branch not
specified* (or *— routes vary* for Pedal Pub). The workbook's notes are
written for whoever cleaned the data ("the form did not specify…"), so they
inform that line rather than being shown as-is. High-confidence notes about
hours and pricing are not shown.

## 4. Stages

After each stage: desktop and 375px screenshots, what changed, then stop for
approval.

| Stage | What | Status |
|---|---|---|
| 1 | Data audit and confidence fields; MapLibre + hosted Positron; all 39 markers at true coordinates | ✅ `e2ef796` |
| 2 | Marker states and zoom bands; tooltip via the map; `revealOnMap()`; selection released when filtered out; the arrival | ✅ `ef7dea4` |
| 3 | Forked style `map/ikap-atlas.json`; controls and attribution; mobile map height, peek-card clearance, safe areas, landscape | ✅ `689a150` |
| 4 | Old SVG map code removed; loading, failure and no-WebGL paths; approximate-location line; favicon; accessibility; QA; docs | ✅ |

### What changed from the plan, and why

- **The caption "Schematic map, not to scale" went in stage 1**, not stage 4:
  on a street map it was simply false.
- **The peek card does not use `map.setPadding`.** It shifts the whole map
  every time a card opens. The card's size feeds the same "what part of the
  map can you see" calculation the desktop panel already uses instead.
- **A fourth zoom band (`wide`)** was added once measurement showed the
  whole-city fit lands near zoom 13.6, so every viewport was sitting in the
  smallest band with nowhere smaller to go.
- **The QA method had to change.** The in-app preview never fires
  `requestAnimationFrame`, and headless Chrome's `--virtual-time-budget`
  doesn't either, so neither can photograph a WebGL map. Screenshots and
  checks were driven over the Chrome DevTools Protocol against a real
  headless browser with software WebGL — see `PROJECT_CONTEXT.md` §13.

## 5. What was removed

From `index.html`: `project()` and its constants, the offshore squash, the
collision-relaxation loop, `svgEl()`, the atlas chrome (graticule, drawn
coast, lake label), `renderDistricts()`, `fitMapToViewport()` and its
viewBox maths, and the SVG `renderMarkers()`. From `css/app.css`: the
`.ground` rules and the `.schematic` caption.

Kept: the story plate in the "Why this exists" modal (a drawing, not the
map), `.row-mark`, the planner's `kmeansCluster()` (which always used raw
coordinates), the legend, the tooltip, `selectPlace()` and `refresh()`.

## 6. Verified

- 43 desktop checks: 39 markers with colour, glyph and the right 12 count
  badges; all 8 category filters, search and sort; selection in both
  directions; zoom bands on real wheel input; camera reveal only when needed
  and clear of the panel; hover tag; all 39 Google Maps links unchanged; "Approximate location" on exactly the ten Medium places; the
  data fields.
- 19 mobile checks each at 375×812, 812×375 and 768×1024: Index default,
  map filling the screen under the switch, all pins framed, 44px controls,
  peek card clear of the edge, a low pin lifted clear of the card,
  attribution never under the card, close and "Read what they said".
- Failure paths: tiles blocked, MapLibre blocked, style file blocked, WebGL
  disabled (desktop and phone), data file blocked.
- Reduced motion: camera moves instant, arrival neutralised.
- Console clean on the normal path: no errors, no warnings.
- No horizontal overflow at 375, 768, 812 landscape, 1024 and 1440.

## 7. Later

- **Maputnik pass** on `map/ikap-atlas.json`: per-zoom label density,
  road-width ramps, halo tuning over water.
- Revisit whether the ten-metre pairs (Wang Lang / Wheatsheaf Tavern) want a
  small fan-out at full zoom.
