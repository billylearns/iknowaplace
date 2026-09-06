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

Single page, `index.html` — plain HTML/CSS/JS, no framework, no build step,
no backend, no dependencies. The data is **not** in the page any more: it is
fetched at load time from `data/places.json` and `data/recommendations.json`
(see §5), and since the redesign the **styles live in `css/app.css`**, linked
from the page. Still no build step — it is one `<link>` — but the design
system is now a file you can open and read. This is a deliberate stack choice
(see README: "learning AI-assisted vibe coding from the ground up").

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
- **One light theme.** On `main`, dark mode is still present and swaps via
  `prefers-color-scheme`. On the redesign branch it has been **deliberately
  removed** and both token blocks deleted, so the site looks identical to
  every viewer regardless of their OS setting — which is what a portfolio
  share needs. This was re-confirmed explicitly before it was done; see §9
  and §14 for why that history matters.
- Responsive layout: collapses to a single column under 880px.

## 4. How the current page works (structure)

**This describes the redesign branch (`redesign/field-guide`). On `main` the
layout is still the original `main.split` two-column grid** — see §14.

- `header.masthead` → a band on the chrome ground holding the wordmark,
  tagline, Toronto city stamp, nav (Why this exists / Contribute a place) and
  the Plan my trip button. Identity sits above the map rather than on it, so
  the type never competes with the pin cluster.
- `main.stage` → the map is **full-bleed**, no border and no box, filling the
  viewport below the masthead. The index floats over it:
  - `.mapfield` — the SVG map, plus `#mapLegend` and the "schematic map, not
    to scale" note positioned over the field
  - `.panel` — a paper panel pinned right, containing `#listHeading`,
    `#filterBar` (category as a row of words), `#refineBar` (price / rating /
    sort as underlined selects), the search line, `#list` and `#emptyState`
  - Under 880px the stage unwinds: the map becomes a band and the panel runs
    underneath it on the page.
- `#filterBar` and `#refineBar` are both rendered by JS
  (`renderFilterBar()` / `renderRefineBar()`), not static HTML.
- Four modals (`.modal-scrim` pattern, hidden by default, toggled via a
  `.open` class), all sharing one focus trap (`openScrim()` / `closeScrim()`,
  which also handle Escape and returning focus to the trigger):
  `#storyScrim` (Why this exists), `#plannerScrim` (Plan my trip, contents
  re-rendered per step via `renderForm()` / `renderResults()` into
  `#plannerBody`), and two honest holding modals — `#contributeScrim` and
  `#citiesScrim` — which say the feature is being built rather than
  pretending to work. Neither has a form or a backend behind it.
- All rendering is imperative JS (no framework): `refresh()` is the central
  function that re-filters/re-sorts/re-renders both the map markers and the
  list whenever a filter, search, or sort changes.
- Icons are inline SVG `<symbol>` defs built at runtime into `#iconDefs` and
  referenced via `<use>` (see `ICONS` dict + `iconUse()` helper).

## 5. The data

- Lives in two JSON files, **not** in `index.html`:
  - `data/places.json` — 39 places
  - `data/recommendations.json` — 56 recommendations
  Both are pretty-printed, one field per line, so they can be read and
  edited by hand and produce small, reviewable diffs.
- `index.html`'s script is an `async` IIFE whose first act is to fetch both
  files into `PLACES_RAW` / `RECS_RAW`; every line after that is unchanged
  from when the arrays were inline. JS then derives the working objects
  (`places`, `recsByPlace`) from them exactly as before.
- The two files are linked by `place_key` (`PLC001`…`PLC039`); each
  recommendation also has its own `recommendation_key` (`REC001`…`REC056`).
  **These keys are permanent — never renumber or reuse them.**
- `recommendation_count` and `average_rating` are stored on each place even
  though they're derivable from the recommendations, because the sorting,
  filtering, map badges and planner scoring read them directly. If you edit
  the recommendations, update them to match: the counts must still sum to 56.
- **Because the page uses `fetch()`, it cannot be opened from `file://`.**
  Double-clicking `index.html` shows a "Couldn't load the recommendations"
  message instead of the site. Serve the folder over http — `npx serve` in
  the project folder — and open the address it prints. Vercel is unaffected.
- **`template.html` no longer exists.** It was a hand-synced duplicate of
  `index.html` with the two data lines swapped for `__PLACES_JSON__` /
  `__RECS_JSON__` placeholders, and no script ever performed that
  substitution. Once the data moved out of the page, `index.html` *was*
  that file, so it was deleted (recoverable from Git history). There is now
  exactly one page file to edit — no sync rule to remember.
- Source of truth history: the data originally came from a Google Form,
  was cleaned into `I_Know_A_Place_Base44_Import.xlsx`, and that workbook
  generated the original inline arrays. The JSON files were verified
  field-for-field against both the old inline arrays and that workbook.
  The workbook is now retired and gitignored (`*.xlsx`) — it is **not** on
  GitHub, because its "Raw Responses" sheet holds friends' unedited form
  wording. The JSON files are the source of truth from here on.

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

## 8. Current functionality status (of `main`)

Everything in §3 is built and working as of this handoff. The **live,
current state of `index.html` and `data/` matches what's on GitHub**
(`github.com/billylearns/iknowaplace`, `main` branch) and what is deployed
to Vercel (`iknowaplace-eta.vercel.app`). The owner's private Claude
Artifact copy of the site is **retired** — an Artifact is a single page with
no `data/` folder to fetch from, so it cannot run this version. Vercel is
now the one canonical live copy. There is no known open bug in this
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

**Update (2026-09):** a second redesign is now underway and going well —
see §14. It revisited two of these three ideas with the owner's explicit
approval: dark mode *was* removed, and the layout *was* restructured around
the map. What made the difference was not the ideas but the process — small
staged commits with a screenshot review gate after each one, instead of one
end-to-end rewrite reviewed only at the finish.

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
- **This is no longer true as of 2026-09.** `git push` works fine from the
  owner's machine: `origin` is configured to
  `https://github.com/billylearns/iknowaplace.git` and the branch
  `redesign/field-guide` has been pushed to it with real, descriptive commit
  messages. Use normal git from here on.
- **Historical note — GitHub was previously kept in sync manually** — the owner
  used GitHub's web "Add file → Upload files" button twice, not `git push`.
  This works fine and is a legitimate workflow the owner is comfortable
  with, but it means **commit history is not meaningful** (both existing
  commits are titled "Add files via upload" with no real message). Once
  Claude Code is working from a proper local clone, normal `git commit` /
  `git push` should be used going forward for real history.
- `.gitignore` excludes: `*.zip`, `check*.py`, `site-package/`, `*.log`,
  editor/OS junk, and `*.xlsx` (the private source workbook — see §5).
  The development/QA artifacts are screenshots taken while iterating on
  design changes, a stale early static-export bundle in `site-package/`,
  and Playwright screenshot scripts — safe to ignore or delete, not part
  of the deployed site. Note `screenshot.png` **is** tracked, since the
  README embeds it.
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
- Nothing needs hand-syncing, but there are now three places to edit rather
  than one: `index.html` for markup and behaviour, `css/app.css` for anything
  visual, and `data/*.json` for content. (`template.html` was deleted when the
  data moved into `data/` — see §5.)
- When testing changes, this session used headless Playwright/Chromium
  (`file://` URL on the local `index.html`) to screenshot before/after
  states rather than guessing — recommended to continue that habit, since
  the map/planner have enough generated-JS behavior that visual bugs aren't
  always obvious from reading the code alone.
- The site intentionally has **no external JS dependencies** (no npm, no
  CDN libraries) beyond the Google Fonts stylesheet link. On `main` that is Fraunces, Work
  Sans and IBM Plex Mono; on the redesign branch it is **Instrument Serif and
  Instrument Sans** — two families from one foundry, one job each. Instrument
  Serif ships Regular only, so never ask for a heavier weight or the browser
  will synthesise a faux bold. Keep it that way unless there's a strong reason not
  to — it's part of the stated design (README: "no framework, no build step,
  no backend, no dependencies"). `npx serve` is a dev-time convenience for
  viewing the page locally, not a project dependency: nothing is installed
  into the repo and the deployed site still needs no build.

## 14. The visual redesign (in progress, 2026-09)

An active redesign lives on the branch **`redesign/field-guide`**, pushed to
GitHub. `main` is untouched, so what is deployed to Vercel is unaffected.

**The full plan is in [`docs/VISUAL_REDESIGN_PLAN.md`](docs/VISUAL_REDESIGN_PLAN.md)** —
read that before touching anything visual. It carries the direction, the design
system as actually built (tokens with measured contrast ratios, type rules,
row and marker anatomy), the stage table, the do-not-touch list, and the
verification checklist.

The short version:

- **Direction: "Atlas Field with a header band."** Identity in a band up top;
  the map full-bleed below it as the ground the product stands on; the index
  floating over it on paper. References were Amici (personality, warmth) and
  KOBU (restraint, hierarchy), translated rather than copied.
- **Palette:** warm off-white paper (`#FBF7ED`) with a nature green
  (`#2E5D3C`) brand and one ochre accent used only on decorative elements.
  Category colours are the only other colour in the interface.
- **What shipped so far:** new tokens and typefaces, dark mode removed, CSS
  moved to `css/app.css`, masthead with paper grain and a footer marquee,
  Contribute and Toronto entry points, cards replaced by hairline index rows
  that name the friend who recommended each place ("*Ashton* + 2 friends"),
  stars rebuilt as SVG, and the whole page recomposed around the map.
- **Still to do:** map plate polish, the mobile Map/List toggle, modals,
  motion, QA, and docs.

**Two process notes worth keeping.** First, there is a hard approval gate after
every stage — desktop and 375px screenshots, then stop — precisely because of
the history in §9. Second, an earlier version of the plan split the work by
component, which meant no stage was ever allowed to change the page's skeleton;
after three stages the site still looked like the prototype with new paint. If
a stage only changes materials, it is not enough.

**Do not** modify `data/*.json`, the planner algorithm, or `refresh()` /
`selectPlace()` control flow as part of visual work. And no Tailwind, no
shadcn/ui, no React, no MapLibre yet — each of those was evaluated and
declined for reasons recorded in the plan.
