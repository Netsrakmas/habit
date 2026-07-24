# WEEK

A weekly commitment tracker — not a habit tracker. You commit to at most four
goals on Sunday, log each evening in ~15 seconds, and review the week in ~90
seconds the next Sunday. A weekly contract with a forgiveness budget.

Built to the spec in [`PROMPT.md`](PROMPT.md); research trail in
[`RESEARCH.md`](RESEARCH.md).

## Run

Open `index.html` — it's a single, self-contained file. No build step, no
dependencies. Works from `file://` or GitHub Pages. State persists in
`localStorage` (key `week.v1`); if storage is unavailable it falls back to an
in-memory demo mode with a banner.

## Goal grammars

- **Quota** — X of 7 days, with a spare budget (e.g. no snacking, 5/7).
- **Pool** — a body of work due Sunday, spread advisory across the week.
- **Counter** — a per-day count on a target number of days (e.g. water, 3/day).
- **Fixed** — scheduled weekdays, each instance swappable once.

## Test harness

Append `?test=1` to the URL. An inline harness runs the pure goal-math
functions against the acceptance criteria and prints `PASS`/`FAIL` to the
console.

## Notes

- Timezone is device-local; weeks are ISO (Monday–Sunday).
- Only external request is Google Fonts (Archivo + IBM Plex Mono), with full
  fallback stacks. No analytics, accounts, or network calls.
- Export / import JSON and a full wipe live in Settings (gear icon).
