# WEEK — master build spec (v1, working title)

A weekly commitment tracker built to be self-tested for 10 weeks. Not a habit tracker: a **weekly contract with a forgiveness budget**. You commit to at most 4 goals on Sunday evening, log in ~15 seconds each evening, and review in 90 seconds the next Sunday. The build target is the loop, not the feature list.

**Role framing:** you are building a precision instrument, not a wellness app. Every screen is a control panel for one week of a person's life. If a choice is between friendly and exact, choose exact.

---

## 1. Hard constraints

- **One HTML file.** Vanilla JS + CSS, no build step, no frameworks, no libraries. Inline SVG for icons.
- Deploy target: GitHub Pages. Must also open from `file://`.
- **Persistence: `localStorage`**, key `week.v1`, versioned schema (`schemaVersion: 1`). If storage is unavailable (e.g. artifact preview), show a top banner "Storage unavailable — demo mode" and run in-memory.
- Mobile-first at **390 px**; usable to 768 px. Tap targets ≥ 44 px. Visible keyboard focus. `prefers-reduced-motion` honored.
- Allowed external requests: **Google Fonts only** (Archivo + IBM Plex Mono, `font-display: swap`, full fallback stacks). Everything else self-contained. No analytics, no accounts, no network calls.
- Total file ≤ 150 KB excluding fonts. Zero console errors/warnings.
- Timezone: device-local (Europe/Amsterdam in practice). Day boundary = local midnight. Weeks are **ISO: Monday–Sunday**, labeled `WEEK 31 · 27 JUL – 02 AUG`.
- All tunables in one `const CFG = {...}` block at the top of the script. All goal math as **pure functions** (state in, verdict out) so the test harness can call them directly.
- Language: English UI strings in one `STR` object (Dutch swap later).

---

## 2. Product spec

### 2.1 Concepts and lifecycle

- **Week states:** `planning → active → review-due → archived`.
- Review unlocks **Sunday 18:00**; a quiet banner appears on Today. Default ritual time 20:30 (setting `reviewHour`, cosmetic only — no notifications in v1; user sets a phone alarm).
- **Grace window:** *today* always editable. *Yesterday* editable until **12:00**. At 12:00 (or next app open, whichever comes first) a resolver sweeps: any unlogged past day resolves per goal-type rules below. Resolved-unlogged renders differently from an explicit miss (hatch + dot vs hatch) but counts the same. Days older than yesterday are view-only.
- **Rollover:** opening the app in a new ISO week with the prior week unreviewed **forces the Review screen first** (catch-up review), then the commit flow. Weeks skipped entirely archive as `unreviewed`.
- **Late commit proration:** committing on weekday `W` (Mon=0): `days_in_play = 7 − W`. Quota target `T' = clamp(round(T × days_in_play / 7), 1, days_in_play)`. Counter `daysTarget` prorated the same way. Fixed instances before commit day are dropped. Pool unchanged (deadline is still Sunday). Days before commit render as `not in play` (blank, excluded from all math).

### 2.2 The four goal grammars

Exactly four types. Do not add a fifth. Do not merge them in the UI (shared fields in the data model are fine).

**QUOTA — "X of 7 days" (e.g. no snacking, 5/7)**
- Params: `target T ∈ 1..7`. Day states: `hit | miss | unlogged`. Resolver: `unlogged → miss` (rendered hatch+dot).
- Math: `spares_total = 7 − T`; `misses = |miss ∪ resolved|`; `spares_left = spares_total − misses`.
- Live copy: `3/5 · 2 SPARE`. If `spares_left < 0`: `OVER BY 1` (ink, never red) — week can no longer HIT but logging stays open (honesty is tracked separately).
- Week verdict: HIT iff `hits ≥ T`.

**POOL — a body of work due Sunday (e.g. pull ups · rows · push ups · pike push ups)**
- Params: `items[] {name, suggestedDay}` (2–8 items). Suggested days auto-spread Mon–Sat on commit; each item's day chip is tappable to reassign. **Suggestion is advisory** — an item can be checked off on any day.
- Risk states after the evening's logging, with `days_left = full days remaining after today (Sun incl.)`:
  `safe` if `remaining < days_left` · `tight` if `==` (one-line ink nudge) · `risk` if `>` (signal-orange left rule + nudge). Nudges live in the goal row, not popups.
- Week verdict: HIT iff `remaining == 0` at week end. Resolver: nothing to resolve (undone is undone).

**COUNTER — per-day count (e.g. water, 3 bottles/day)**
- Params: `perDay P ∈ 1..12`, `daysTarget D ∈ 1..7` (default 7). Stepper +/−, no upper clamp (`4/3` allowed, day still hit).
- Day hit iff `count ≥ P`. Resolver: untouched day keeps its count (0 ⇒ not hit).
- Live copy: `TODAY 2/3 · DAYS 4/7`. Week verdict: HIT iff `daysHit ≥ D`.

**FIXED — scheduled days (e.g. treadmill 10 min, Mon/Wed/Fri)**
- Params: `days ⊆ {Mon..Sun}`, optional `label` ("10 MIN"). Instance states: `hit | miss | unlogged→miss | moved`.
- **Swap:** any instance (incl. a just-missed one) can be moved once to another free day this week, while ≥1 future day remains. Original cell renders `→`, destination gets the instance. One swap per instance.
- Week verdict: HIT iff every instance (post-swap position) is hit.

### 2.3 Slots and friction

- 4 active goal slots, free. Adding a **5th** opens a friction dialog: "Full week. Five-goal weeks fail more often than four-goal weeks. Park one instead?" — buttons `Park one…` (primary) / `Add anyway` (quiet). **6 is a hard cap** (add button disabled, reason shown inline).
- Goals can be `active | parked | dropped`. Parked goals keep history and reappear as one-tap suggestions in planning.

### 2.4 Screens

Bottom nav, 3 tabs: **TODAY · WEEK · TROPHIES**. Gear icon in the header → Settings sheet. Review is a takeover flow, not a tab.

**TODAY (default).** Header: big day name (display face), week label under it (mono caps). Then, in order: ⑴ tip slot (max one, dismissible), ⑵ *Yesterday — open till 12:00* section iff in grace with unlogged items (same row components, recessed panel), ⑶ one row per active goal with its native control (quota: 7-cell strip, today's cell tappable hit/miss; pool: item checklist; counter: stepper; fixed: today's instance button if scheduled, else the strip in read-only). ⑷ Sunday ≥18:00: review banner. The whole evening ritual = one screen, no scrolling past ~4 goals.

**WEEK.** The week as a table: goal rows × 7-cell strips, all states visible (hit / miss / resolved / moved / future / not-in-play). Tapping a goal expands detail + `Edit` (planning fields) + `Park`. `+ Add goal` respects slot rules. This is also the planner.

**REVIEW (Sunday ≥18:00 or forced on rollover).** One screen, three zones:
1. Results: goal name + verdict token (`HIT` = ink-filled square with check; `MISS` = hatched square + one dry diagnostic line, e.g. "Treadmill missed Wed twice running").
2. Achievements earned this week (usually none — that's the point). Stamp animation 240 ms.
3. Per-goal decision chips `Keep · Adjust · Park · Drop`, with the rule-table suggestion (§2.5) pre-highlighted in signal orange (the screen's single accent). `Adjust` opens the minimal field (target/items/days).
Footer: `COMMIT WEEK 32 →` (primary). Committing archives, applies decisions, seeds the new week. 90 seconds total.

**TROPHIES.** 8 stamp tokens (§2.6), locked = hatched. Under them, the archive: one compact row per past week (`W30 · 3/4 · SWEEP`).

**SETTINGS (sheet).** Export JSON (file download + copy-to-clipboard fallback) · Import JSON (file picker, validates `schemaVersion`, confirms overwrite) · review hour · wipe all (double-confirm, type `WIPE`).

### 2.5 Review adjustment rules (pre-select, never auto-apply)

R1 quota missed with `misses ≥ spares_total + 2` → suggest `T − 1`.
R2 quota HIT using 0 spares, 2 weeks running → suggest `T + 1` (max 7) — chip label "Raise".
R3 pool with ≥2 items never done → suggest remove 1 item.
R4 pool cleared by Friday, 2 weeks running → suggest `+1 item`.
R5 fixed: same weekday missed in 2 consecutive weeks → suggest moving that weekday.
R6 counter `daysHit ≤ 3` → suggest `P − 1` (min 1).
R7 any goal HIT 3 consecutive weeks → highlight `Park` with sublabel "mastered".
Default when no rule fires: `Keep`. Tone: every suggestion is phrased as making next week *easier or freer*, never as punishment.

### 2.6 Achievements (8, all denominated in completed weeks)

`FIRST` first review completed · `SWEEP` all goals HIT in one week · `DOUBLE` two consecutive SWEEPs · `COMEBACK` a goal missed week N, HIT week N+1 · `HONEST` every day of a week logged before grace expiry (results irrelevant) · `NO-SPARE` a quota with `T ≤ 5` logged 7/7 hits · `EARLY` pool cleared by Friday 23:59 · `STEADY` 4 consecutive reviews completed.
Stamps are 28 px squares, 1.5 px border, mono glyph/initials, drawn in SVG — no emoji, no gold, no glow. Unlock announcement only inside Review.

### 2.7 Tips engine

One slot on Today, dismissible, per-tip cooldown 21 days, priority = order below, **state-triggered only** (never calendar-triggered):
T1 fixed instance skipped 2× this week → "Shrink it. 10 min → 5. Small enough to start is the whole trick."
T2 pool enters `risk` → "Split the pile — do the easiest item tonight."
T3 counter `daysHit/daysElapsed < 0.5` on/after Thu → "Anchor it: one bottle with each meal beats remembering."
T4 quota `spares_left == 0` before Sat → "Spares gone, week's still live. Protect the days that remain."
T5 first active week → "Log honestly. A hatched day teaches more than a fake tick."
T6 everything `safe` on Fri → "Ahead. Bank a Sunday item today if you fancy."
T7 previous week archived `unreviewed` → "The Sunday review is the whole engine. 90 seconds."
T8 ≥3 goals simultaneously failing/at-risk → "Overloaded. Parking one goal is a decision, not a failure."

### 2.8 Seed (first run)

Planning screen pre-filled, fully editable:
1. QUOTA — "No snacking" — 5/7
2. POOL — "Strength" — pull ups · rows · push ups · pike push ups
3. COUNTER — "Water" — 3/day, 7 days
4. FIXED — "Treadmill 10 min" — Mon · Wed · Fri

### 2.9 Data model (localStorage `week.v1`)

```json
{
  "schemaVersion": 1,
  "settings": { "reviewHour": 20.5, "lang": "en" },
  "goals": [ { "id": "g1", "type": "quota", "name": "No snacking",
               "params": { "target": 5 }, "status": "active", "createdWeek": "2026-W31" } ],
  "weeks": { "2026-W31": {
      "committedOnDay": 0,
      "goals": [ { "goalId": "g1", "snap": { "target": 5 },
                   "days": { "0": "hit", "1": "miss", "2": "resolved" },
                   "swaps": {}, "counts": {}, "poolDone": {}, "verdict": null } ],
      "reviewedAt": null, "decisions": {} } },
  "achievements": { "FIRST": "2026-W31" },
  "tips": { "T5": "2026-W31" }
}
```
Export = pretty-printed dump of this object. Import must round-trip to deep-equal.

---

## 3. Style bible v2 (locked) — neobrutalist control surface

> Supersedes the v1 monochrome bible. Rationale and the computed palette validation
> are in `RESEARCH.md`, addendum 2026-07-25. v1 is kept in git history, not here.

**Anchors:** neobrutalism (hard border + hard offset shadow as the two non-removable
properties) applied to the v1 instrument-panel grammar. Still a control surface, not
a card feed: the week *is* a 7-step sequence and the step row remains the signature
component. Turn the dial up, add colour — do not restyle into a generic app.

**Governing rule — legibility outranks rawness.** WEEK is a precision instrument.
Where the neobrutalist reflex and comprehension conflict, comprehension wins. Bold
is a means, never the goal.

### 3.1 Palette (role-named, locked)

```
--paper    #F4F3EE   page
--card     #FFFFFF   raised faces: cells, chips, steppers, sheets
--ink      #12100E   text, every border, all shadows
--ink-2    #5A574F   meta text, diagnostics
--rule     #D9D7CF   hairline dividers only (never a control border)
```
Four goal-type hues. **Colour encodes identity, never decoration.** A hue appears
only on a mark that means something; ink and paper stay the dominant surfaces.
```
--t-quota    #2440E8   on-colour #FFFFFF   (7.06:1)
--t-pool     #FF4A00   on-colour #12100E   (5.63:1)
--t-counter  #00A878   on-colour #12100E   (6.21:1)
--t-fixed    #F075C0   on-colour #12100E   (7.24:1)
```
Rules: **done = fill in the goal's hue**, glyph in that hue's on-colour — never
green-for-good. **Miss = 45° ink hatch on card.** Resolved = hatch + 3 px ink dot.
No gradients. No glass. No second shadow colour. Adding a fifth hue is forbidden —
a fifth goal type does not exist. Every coloured fill is enclosed by an ink border
and accompanied by a text label; colour is never the only channel.

### 3.2 Type
- **Display:** `Archivo` 700/800. Day name 34/36 ExtraBold, `letter-spacing −0.02em`.
  Section heads 20 Bold. Goal names 16 Bold. Fallback `system-ui, sans-serif`.
- **Data/labels:** `IBM Plex Mono` 500/600 — all numerals (`tabular-nums`), week
  labels, badges, chips, stamps. Caps labels 11/14, `letter-spacing 0.06em`.
- **Body:** system sans 15/22. No other families. Banned: Inter, Roboto, Poppins,
  Space Grotesk, Nunito, Plus Jakarta.

### 3.3 Shape, depth, motion
- Borders **2.5 px `--ink`** on every control and raised face; hairline `--rule`
  1 px for row dividers only. Radii **0 / 6 / 14** — deliberately mixed; a single
  uniform radius everywhere is a named AI tell.
- Depth is one hard offset shadow, ink, never blurred: **4 px 4 px 0** on cards,
  sheets, dialogs and primary buttons; **2 px 2 px 0** on chips and small controls.
- **Press = displacement.** Active control translates `+2px,+2px` and its shadow
  goes to 0, so the surface reads as physically pushed. 90 ms.
  `prefers-reduced-motion`: opacity only, no transform.
- Spacing on a 4 px grid but deliberately varied (row 18, section 34, header 40).
  Bold blocks need air — crowding is the failure mode of this style.
- Progress is always discrete cells, never a continuous bar.

### 3.4 Per-object specs
- **Day cell:** flex-filled (≥ 44 px) × 52, `--card`, 2.5 px ink, r6, 3 px gap; mono
  day initial 10 top. Today: 3 px ink border + 2 px 2 px 0 shadow. Hit: goal-hue
  fill + on-colour check. Miss: hatch. Resolved: hatch + dot. Moved: `→`. Future:
  empty card. Not-in-play: no border, 0.3 opacity.
- **Type badge:** mono caps 10, goal-hue fill, on-colour text, 2.5 px ink, r6,
  h 22 — the row's identity, read before any text.
- **Goal row:** badge + name (Archivo 16 Bold) + mono stat line; control below.
  At-risk rows carry a 6 px `--t-pool` left bar.
- **Stepper:** `[−][ 2/3 ][+]` full-width, 52 tall, ink borders, press displacement.
- **Pool item:** 26 px ink-bordered box, fills goal-hue on done; day chip r6.
- **Buttons:** h 52, r6, 2.5 px ink, mono caps. Primary = ink fill / paper text.
  `COMMIT WEEK →` is a `--t-pool` block — the one loudest element per screen.
- **Stamps:** square, ink border, r6; locked = hatch, earned = goal-agnostic ink
  fill with mono initials. No emoji, no gold, no glow.
- **Nav:** active tab sits on a filled ink block with paper text.

### 3.5 Copy
Unchanged from v1: control labels ≤ 3 words, diagnostic voice, never cheerleader.
Buttons say what they do. Errors state cause + fix.

---

## 4. Forbidden list (audit before done)

Gradients · backdrop-blur/glass · any blurred or non-ink shadow · emoji · Inter/
Roboto/Poppins/Space Grotesk/Nunito · purple/violet accents (`#6C5CE7` family) ·
"Tailwind blue" `#3B82F6` · warm-cream + terracotta pairing · red failure states ·
green-for-success (green is the counter's identity hue, not a verdict) · flame/
streak icons · smooth progress bars · confetti/particles · soft-shadow floating
card grids · one uniform radius across all elements · hero-plus-three-cards ·
scroll-triggered fade-ins · skeleton shimmer · motivational-poster copy · toasts
that praise · a fifth accent hue · colour as the only channel for any state ·
icons other than inline 1.5 px-stroke SVG (max 12).

## 5. Acceptance criteria (self-checkable)

`?test=1` runs an inline harness against the pure functions and prints PASS/FAIL to console — all must pass:

1. Quota T=5, days `[hit,hit,miss,hit,resolved,hit,hit]` → hits 5, misses 2, `spares_left 0`, verdict **HIT**.
2. Quota T=5 with 3 misses → `OVER BY 1`, verdict **MISS**; days remain loggable.
3. Pool 4 items: Wed evening 3 remaining, `days_left 4` → `safe`. Thu evening 3 remaining, `days_left 3` → `tight`. Fri evening 3 remaining, `days_left 2` → `risk`.
4. Fixed Mon/Wed/Fri: Wed missed, swapped→Thu, Thu hit, Mon+Fri hit → verdict **HIT**; Wed renders `→`; second swap of the same instance rejected.
5. Counter P=3, D=7, counts `[3,4,0,3,3,2,3]` → daysHit 5 → verdict **MISS (5/7)**.
6. Late commit Wednesday: `days_in_play 5`; quota T=5 → `T′=4`; Mon/Tue render not-in-play; fixed Mon instance dropped.
7. Grace: Tue 11:59 yesterday editable; Tue 12:00 resolver flips its `unlogged→resolved`; Wed, Monday is view-only.
8. Rollover: open on Wed with prior week unreviewed → Review forced first; commit then prorates per (6).
9. Achievements: simulate a SWEEP week → `SWEEP` unlocks once, never re-unlocks; `HONEST` requires zero resolved days.
10. Slot friction: 5th goal → dialog; 6th → hard-blocked.
11. Export → wipe → import → deep-equal state.
12. Non-functional: zero console errors on all screens · file ≤ 150 KB excl. fonts · every interaction paints < 100 ms · tap targets ≥ 44 px · visible focus rings · reduced-motion verified · forbidden-list audit clean · screenshot squint-test against OP-1/Braun anchors ("could this be a hardware control face?").

---

## 6. Milestone build order (gates are blocking)

- **M0 — Engine.** `CFG`, `STR`, data model, storage guard, pure goal math, resolver, proration, ISO-week utils, test harness. *Gate: criteria 1–9 pass headlessly (no UI).* 
- **M1 — Today.** All four controls live, grace/yesterday section, resolver on open. *Gate: scripted evening of taps reproduces criterion values; criterion 7.*
- **M2 — Week/Planner.** Strip table, expand/edit/park, add-goal with slot friction, pool day chips, fixed swap UI. *Gate: criteria 4, 10.*
- **M3 — Review + commit.** Unlock time, verdicts, decision chips + rule table, proration path, rollover forcing. *Gate: criteria 6, 8.*
- **M4 — Trophies + tips.** Both engines + archive rows. *Gate: criterion 9; each tip trigger simulated once.*
- **M5 — Style transform.** Until now the UI may be plain. Apply the full bible: palette, type, per-object specs, dot grid, signature strip everywhere. *Gate: forbidden-list audit + squint test (criterion 12).* 
- **M6 — Polish.** Motion + haptics + reduced-motion, export/import, seed flow, settings, wipe. *Gate: full acceptance run, criteria 1–12.*

- **M7 — Colour transform (style bible v2).** Replace the v1 monochrome surface with
  the neobrutalist system above. Pure restyle: no goal logic, no state shape, no
  class renames (the test harness selects on them). *Gate: 53/53 assertions still
  green · every browser suite zero console errors · tap targets ≥ 44 px · contrast
  spot-checks hold · forbidden-list audit clean · squint test — still a control
  surface, now unmistakably colour-coded.*
  - [x] palette computed and validated (`RESEARCH.md` 2026-07-25)
  - [x] style bible v2 written
  - [x] implemented in `index.html`
  - [x] verified and screenshotted

Out of scope v1 (do not build): notifications/push · cohorts/social · Health Connect/wearables · maintenance/graduated-goal tracking · paid tier · dark mode · Dutch strings · PWA service worker.
