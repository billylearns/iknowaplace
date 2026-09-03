# I Know a Place

Recommendations from people you actually trust — not a scraped review, not an algorithm's guess.

Every place on this site was recommended by someone real, with their own rating and their own words. It's a browsable map + list for exploring Toronto through 56 recommendations from 22 friends, plus a "Plan my trip" tool that builds a real day plan out of them (time / energy / budget in, a few realistic plans out — no AI guessing, no generic itinerary).

![The I Know a Place website: a hand-drawn map of Toronto with colour-coded pins, beside a scrollable list of recommended places showing each one's category, neighbourhood, star rating and how many friends recommended it.](screenshot.png)

**Live site:** [iknowaplace-eta.vercel.app](https://iknowaplace-eta.vercel.app/)

## Why this exists

Full story is on the site itself (the "Why this exists" link), but in short: recommendations from friends are usually scattered across texts and group chats, disconnected from how much time or money you actually have. This turns them into something usable.

## Stack

Plain HTML, CSS, and JavaScript — no framework, no build step, no backend, no dependencies. `index.html` is the whole site: markup, styles and logic in one file. It reads its content from two JSON files in `data/`, so any static host (this one runs on Vercel) can serve the folder as-is.

## Run it locally

The page loads its data with `fetch()`, and browsers block that on `file://` URLs — so opening `index.html` by double-clicking it won't work. Serve the folder over http instead. With [Node](https://nodejs.org) installed, run this in the project folder and open the address it prints:

```bash
npx serve
```

## Data

Sourced from a Google Form filled out by real friends, then cleaned and anonymized (first-name aliases only, no last names). It lives in two files you can open and edit directly:

- `data/places.json` — the 39 places (name, category, neighbourhood, coordinates, address, rating summary)
- `data/recommendations.json` — the 56 recommendations (who suggested it, their rating, their note)

The two are linked by `place_key`: every recommendation names the place it belongs to. Those keys are permanent — never renumber them.

## Status

Prototype — first city is Toronto. Built as a hands-on project to learn AI-assisted ("vibe") coding from the ground up.
