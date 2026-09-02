# I Know a Place — Project Context / Handoff

This document exists to bring a new Claude Code session (or any developer) up to
speed on this project without needing to re-derive decisions from scratch. It
was written at a handoff point where the site is in a known-good, working
state. Read this before making changes.

---

## 1. What this website/app is supposed to do

**I Know a Place** ("IKAP") is a personal-portfolio project: a recommendations
site built entirely from real suggestions the owner (Billy Liu) collected from
friends and family, rather than scraped reviews or algorithmic rankings.

It has two main things a visitor can do:
1. **Browse** — a map + list of recommended places in Toronto, filterable by
   category, price, and rating, with each place showing who recommended it,
   their rating, and their personal note.
2. **Plan my trip** — a small planning tool. The visitor answers a few
   questions (interests, available time, energy level, budget), and the tool
   assembles a few different day-plan options purely from the real
   recommendation data — no AI/LLM is involved in generating the plans. It's
   a deterministic/weighted-random algorithm (see §6).

The project is meant to be shared as a portfolio piece (e.g. on LinkedIn), so
it should look intentional and finished, not like an in-progress prototype.

## 2. The overall travel/recommendation concept

- Data originally came from a Google Form filled out by real friends, then
  cleaned and anonymized (first-name aliases only, no last names).
- Every **place** can have multiple **recommendations** attached to it (one
  per friend who suggested it), each with its own rating (0–10), a short
  personal note, and its own price/energy/time-commitment levels.
- The pitch (stated explicitly in the site's "Why this exists" story and in
  the README) is that this is *not* TripAdvisor/Google Maps: the value is
  that recommendations come from people whose taste you actually know and
  trust, not strangers or an algorithm. AI is described as a tool used to
  *organize* the data, not to invent recommendations.
- Toronto is explicitly the pilot city. The stats baked into the copy: **39
  places, 56 recommendations, 22 friends** (these are real counts of the
  embedded data — see §5 — not placeholder numbers, so if the dataset
  changes these strings need to be updated too, in both the story text and
  the README).
- Stated future intent (from the owner, in the story copy): reuse the same
  format for future personal trips by collecting recommendations before
  leaving.

## 3. Everything already built

Single self-contained page, `index.html` — plain HTML/CSS/JS, no framework,
no build step, no backend. All data is inlined as JS arrays in a `<script>`
tag. This is a deliberate stack choice (see README: "learning AI-assisted
vibe coding from the ground up").

Built and working:
- **Header** — wordmark, tagline, "Why this exists →" link (opens a modal),
  "Plan my trip" primary button.
- **Filter bar** — three dropdowns: Activity/category, Price, Rating
  (labelled "All activities" / "All prices" / "All ratings" as their default
  option — see §7 for why this exact wording matters).
- **Map** — a hand-drawn *schematic* SVG map (not a real map tile provider,
  no Mapbox/Google Maps JS API). Places are projected from real lat/lng onto
  an SVG grid via a custom `project()` function, with a collision-relaxation
  pass so overlapping pins nudge apart. A decorative bottom band represents
  Lake Ontario. Markers are colored by category; a numeric badge on a marker
  is that **place's recommendation count** (not a map cluster count — only
  one place per marker).
- **List** — sortable (highest/lowest rating, most recommended,
  neighbourhood), searchable by name, expandable cards showing every
  recommendation's alias, rating, and note, plus a "Open in Google Maps"
  link built from the place's address.
- **Map ↔ list sync** — clicking a marker selects the place, opens/expands
  its card, and scrolls the list to it (and vice versa via card click).
- **"Why this exists" modal** — the personal essay described in §2, ending
  in a signature and a "Connect on LinkedIn" button (real link to
  billyhliu's profile).
- **"Plan my trip" planner** — a multi-step modal:
  - Form: activity interests (multi-select pills, "Open to anything"
    default), available time (Up to 2h / 2–4h / 1 day / 2 days / 3 days),
    energy level (Low/Medium/High), max budget (Free/$/$$/$$$/$$$$).
  - Results: generates **3 plan options** at once (`buildPlan` called for
    seeds 0,1,2), each with a title (auto-generated from the dominant
    category mix, e.g. "Food + Nightlife"), a blurb, and day-by-day stop
    lists with hours/links.
  - "Show me different plans" reshuffles (increments a seed, regenerates).
  - "Adjust my answers" goes back to the form.
- Full **dark mode support** — the palette is token-based (CSS custom
  properties) and swaps automatically via `prefers-color-scheme`, with a
  `data-theme` attribute override available. (Note: a later redesign
  attempted to *remove* dark mode entirely in favor of one committed light
  theme — that attempt was built, reviewed, and explicitly rejected by the
  owner; see §9. Dark mode is currently intact and working.)
- Responsive layout: collapses to a single column under 880px.

## 4. How the current page works (structure)

- `header.top` → brand + story link + Plan my trip button
- `#filterBar` → rendered by JS (`renderFilterBar()`), not static HTML
- `main.split` → CSS grid, two columns:
  - `.map-pane` (≈58%) — sticky-positioned SVG map + legend
  - `.list-pane` (≈42%) — sort/search controls + `#list` (cards) +
    `#emptyState` (no-matches message)
- Two modals (`.modal-scrim` pattern, both hidden by default, toggled via a
  `.open` class): `#storyScrim` (Why this exists) and `#plannerScrim` (Plan
  my trip, contents re-rendered per step via `renderForm()` /
  `renderResults()` into `#plannerBody`).
- All rendering is imperative JS (no framework): `refresh()` is the central
  function that re-filters/re-sorts/re-renders both the map markers and the
  list whenever a filter, search, or sort changes.
- Icons are inline SVG `<symbol>` defs built at runtime into `#iconDefs` and
  referenced via `<use>` (see `ICONS` dict + `iconUse()` helper).

## 5. The data

- Embedded directly in `index.html` as `const PLACES_RAW = [...]` and
  `const RECS_RAW = [...]` (raw JSON-shaped arrays, one row per Google Form
  export record). JS then derives working objects (`places`, `recsByPlace`)
  from these at load time.
- **`template.html`** is a parallel file, byte-for-byte identical to
  `index.html` except those two arrays are replaced with placeholder tokens
  `__PLACES_JSON__` / `__RECS_JSON__`. The intent is that it's a template
  for regenerating the site from fresh data.
  - **Gap to know about**: there is currently no build script that actually
    performs that substitution. `check.py` / `check2.py` are Playwright
    screenshot/QA scripts (used during development to visually verify
    changes), not a build/generation pipeline. If the underlying data
    changes, someone needs to either hand-edit both files' arrays in sync,
    or write a small script to fill `template.html`'s placeholders and
    output a new `index.html`. Whenever `index.html` is edited by hand,
    remember to mirror the same edit into `template.html` (everything
    except the two data lines should stay identical between them — this was
    verified line-by-line during this session).

## 6. Planner algorithm details (technical decisions)

No AI/LLM generates the plans — it's a scored, weighted-random selection:
- `scoreOf(place) = avgRating*1.4 + min(recCount,4)*0.9` — better-rated,
  more-recommended places are more likely to be picked, but it's not purely
  greedy (uses `seededRandom()` + weighted pick, so re-shuffling gives
  genuinely different results).
- Multi-day trips (2–3 days) use a small k-means-style clustering
  (`kmeansCluster`) on place coordinates so each day's stops are
  geographically clustered rather than scattered across the city.
- `fillBucket()` fills each day within its time cap (`CATEGORY_DAY_CAP`,
  default 2 per category per day) while respecting energy/budget filters
  already applied upstream.
- Special case: if **Nightlife** is the *only* interest selected, its
  per-day cap is lifted entirely (treated as an intentional bar-crawl
  request rather than a balanced day).
- Special case: if a plan ends up with two Food stops back-to-back, a
  post-pass swaps in whatever non-Food/non-Nightlife stop is available just
  before them, so the plan doesn't read as "eat, then eat again."

## 7. Important design decisions made this session

- **Price filter must be a `<select>` dropdown**, not a slider. A slider was
  tried (styled, with tick labels) and the owner found it "too big and
  jarring" — reverted back to a plain dropdown matching the other two
  filters. This was tried twice in slightly different label wording:
  - First reverted to short labels ("Activity" / "Price" / "Rating"),
    cherry-picked from an older exported zip the owner had downloaded.
  - Owner then caught that this makes the *default/placeholder* option
    read oddly (a dropdown showing "Activity" instead of "All activities"
    doesn't read as "no filter applied"). **Final, current wording is "All
    activities" / "All prices" / "All ratings"** for all three dropdowns'
    default option. Don't re-introduce the short form.
- **"Available time" planner cards must match the pill selection style.**
  Originally, selecting a time-option card left a faint tinted background
  with dark text, inconsistent with how selected pills elsewhere (Activity,
  Energy, Budget) go to a solid dark/accent background with light text.
  Fixed by giving `.option-card.selected` a solid `var(--accent)`
  background + `var(--accent-ink)` text, matching the pill pattern.
- **Story section stats row (39 places / 56 recs / 22 friends as a
  standalone stat row) was intentionally removed** from the "Why this
  exists" modal per owner request — the numbers still exist elsewhere (map
  caption, list heading), just not as a dedicated block inside the essay.
- **The map is deliberately a stylized/schematic SVG, not a real map
  provider.** This has been treated as a feature, not a placeholder — no
  request has been made to swap in Mapbox/Google Maps, and doing so would
  be a substantial change (would need a real API key, tile provider, and
  reworked marker/tooltip code). Flag this explicitly to the owner before
  assuming it's wanted.

## 8. Current functionality status

Everything in §3 is built and working as of this handoff. The **live,
current state of `index.html`/`template.html` matches what's on GitHub**
(`github.com/billylearns/iknowaplace`, `main` branch) and what's published
to the owner's private Claude Artifact. There is no known open bug in this
version — all issues raised during this session (slider, stats row, dropdown
label wording, option-card text contrast) were fixed and confirmed.

## 9. A redesign attempt was tried and rejected — know this before proposing similar changes

Later in this session, a larger visual/structural redesign was attempted at
the owner's request, based on three ideas discussed earlier:
1. Drop dark-mode support entirely, commit to one refined "gallery/stone"
   light palette, and use larger/more confident type + more generous
   spacing (rationale: consistent look on a portfolio/LinkedIn share
   regardless of viewer's OS theme).
2. A "luxury real-estate site" style reference (quieter neutral base, more
   negative space, bigger type) — while explicitly keeping the category
   colors vivid on the map/cards, since muting those was considered a loss.
3. Restructure to "map as hero, list as toggle" — full-width/large map as
   the primary view, with the list moved into a slide-in drawer panel
   (triggered by a "Browse list" button or by clicking a marker), inspired
   by AllTrails/Airbnb's map-first UX pattern.

All three were implemented, screenshot-tested (desktop, mobile, modals,
drawer interactions, marker→drawer linking — all worked correctly with no
JS errors), and then **the owner reviewed it and said it looked worse.** The
entire attempt was reverted back to the last GitHub-saved commit (the state
described in §3–§8), and that reverted version is what's currently live
everywhere (workspace, GitHub, artifact).

**Implication for whoever continues this project**: the owner has already
seen and rejected this specific direction once. If asked to revisit map
layout, palette, or dark-mode, don't just re-propose the same combination —
find out specifically what didn't land (this wasn't diagnosed in detail
before the revert; the owner's exact objection to *which part* of the
redesign is unknown — it could be the palette, the layout restructure, the
type sizing, or the combination of all three at once). Worth asking rather
than assuming.

## 10. Unfinished features

- **Contribute form** — explicitly deferred, not built. Direction already
  agreed with the owner: use **Formspree** (formspree.io) because it's
  "white-label" — a plain HTML form posts to a Formspree endpoint with no
  visible widget/iframe/branding on the page. Plan was to design fields
  (place name, category, rating, comment, maybe a link) styled to match the
  existing card design, and wire the form's `action` URL to a Formspree
  endpoint. Estimated ~20 minutes of build time. Worth double-checking
  Formspree's current free-tier limits before wiring it up (pricing/limits
  can change).
- **Live URL** — README has a placeholder: `**Live site:** _(add your
  Vercel URL here once deployed)_`. The owner was walked through deploying
  to Vercel (import the GitHub repo, no build command needed since it's
  static HTML) but as of this handoff it's unconfirmed whether that was
  completed. Check the README/ask the owner, and fill in the real URL once
  known.

## 11. Features discussed but not committed to

- Nothing else concrete was discussed beyond the contribute form and the
  (rejected) redesign in §9. If the owner brings up dark-mode removal, a
  palette refresh, or a map-as-hero layout again, see §9 first.

## 12. Git / GitHub / deployment notes

- Repo: `https://github.com/billylearns/iknowaplace.git`, single `main`
  branch.
- **A local git repo already exists in this project folder** (`git init`
  was run, one commit exists: `8f81526 "Initial commit: I Know a Place"`,
  containing `README.md`, `index.html`, `template.html`, `.gitignore`).
  This local repo has never successfully been `git push`ed anywhere — the
  cloud session it was created in had a git-credential proxy that refused
  to push to this repo ("not in this session's authorized repository set").
  This limitation was specific to that sandboxed remote session's proxy; it
  should **not** apply when working from Claude Code running locally on the
  owner's own machine with their own git/GitHub credentials — a normal
  `git remote add origin ...` + `git push` should just work there. Verify
  this local repo's remote isn't already misconfigured before assuming.
- **In practice, GitHub has so far been kept in sync manually** — the owner
  used GitHub's web "Add file → Upload files" button twice, not `git push`.
  This works fine and is a legitimate workflow the owner is comfortable
  with, but it means **commit history is not meaningful** (both existing
  commits are titled "Add files via upload" with no real message). Once
  Claude Code is working from a proper local clone, normal `git commit` /
  `git push` should be used going forward for real history.
- `.gitignore` excludes: `*.png`, `*.zip`, `check.py`, `check2.py`,
  `site-package/`. These are development/QA artifacts (screenshots taken
  while iterating on design changes, a stale early static-export bundle in
  `site-package/`, and Playwright screenshot scripts) — safe to ignore or
  delete, not part of the deployed site. There are many such stray files
  sitting in the project root from this session's iteration history; feel
  free to clean them up.
- No CI/CD, no tests. `check.py`/`check2.py` are manual Playwright scripts
  (screenshot + console-error check) run ad hoc during development, not
  wired into any automated pipeline. Useful pattern to reuse for visual QA:
  launch Chromium via Playwright, screenshot key states (default, filters
  applied, planner form, planner results, mobile viewport), check console
  for errors.

## 13. Anything else a new developer should know

- The owner (Billy) is a **beginner** with git/terminal/deployment concepts
  — comfortable with the *what* and *why* but needs step-by-step, plain-
  language walkthroughs for *how* (confirmed: needed Git/GitHub explained
  from first principles, needed Windows-specific PowerShell instructions
  spelled out step by step). Calibrate explanations accordingly — don't
  assume familiarity with terminal commands, git concepts, or deployment
  jargon. This is explicitly a learning project for the owner ("Built as a
  hands-on project to learn AI-assisted vibe coding from the ground up" —
  from the README).
  - However, they set up a Formspree recommendation and Vercel deployment
    conversation already, so they're ramping up — don't over-explain things
    already covered.
- `index.html` and `template.html` must be kept in sync on every edit
  (identical except the two data-array lines) — this was manually verified
  line-by-line after every change in this session. A quick way to check:
  diff the two files' line arrays and confirm only the `PLACES_RAW`/
  `RECS_RAW` lines differ.
- When testing changes, this session used headless Playwright/Chromium
  (`file://` URL on the local `index.html`) to screenshot before/after
  states rather than guessing — recommended to continue that habit, since
  the map/planner have enough generated-JS behavior that visual bugs aren't
  always obvious from reading the code alone.
- The site intentionally has **no external JS dependencies** (no npm, no
  CDN libraries) beyond the Google Fonts stylesheet link (Fraunces, Work
  Sans, IBM Plex Mono). Keep it that way unless there's a strong reason not
  to — it's part of the stated design ("one self-contained file that any
  static host can serve as-is").
