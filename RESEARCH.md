# RESEARCH.md — evidence trail

## Addendum 2026-07-24 — kickoff research (market, retention, anti-AI design)

### 1. Competitive shape (why the mechanic is the wedge, not the scope)

- **Fabulous** owns "all-in-one self-care, one habit at a time": Journeys (3–12 wks, one habit at a time), Challenges (5–30 days), audio coaching, Circles community. Duke Behavioral Economics Lab origin, claims 37M users, $39.99/yr.
  - https://help.thefabulous.co/en/support/solutions/articles/101000427430-how-does-fabulous-work-
  - https://www.thefabulous.co/
  - Known criticism: rigid — "You can only work on one habit at a time" listed as a limitation (https://tapptitude.com/blog/fabulous-app-develop-healthy-benefits).
- **All-in-one trackers** exist and are saturated: Wellness Core (13 connected trackers — https://wellnesscore.app/), CARE (bio/psycho/social/spiritual + Circles — https://www.trendhunter.com/trends/all-in-one-wellness-app), Vantage Fit (corporate).
- **Category-wide retention failure**: "the gamification becomes a chore… the mechanic that delights in week one feels like one more thing to tend in month three" (https://habitbox.app/blog/finch-app-review). Novelty decay ~month 3 is the real enemy, not competition.
- **Data-entry burden** is the acknowledged killer of breadth: best apps use wearable automation, one-tap logging, and *suggest limiting to 2–3 dimensions* (https://www.mindfulsuite.com/reviews/best-wellness-coaching-apps).
- **Coaching-as-rescue economics are bad**: Noom's coaching-dependent model → cost-cut death spiral described at https://nutrola.app/en/blog/what-happened-to-noom; pricing context https://www.bentobunny.app/reviews/noom-review. Decision: sell ambition, not rescue (out of scope v1 anyway).

**Consequences baked into the spec:** week (not day, not journey) as the unit; 4 goal *grammars* instead of many trackers; forgiveness by construction (spares, grace window); one evening screen; Sunday review as the single ritual; achievements denominated in completed weeks; social/cohorts deferred; v1 = solo self-test of the loop.

### 2. "AI-generated look" — named tells to forbid (sources)

- Default layout tell: hero + three feature cards + testimonials + CTA; "most statistically common layout" (https://shuffle.dev/blog/2026/01/why-do-most-ai-generated-websites-look-the-same/).
- Font/color tells: Inter/Roboto defaults, "Tailwind Blue," purple gradients, uniform rounded corners (https://axe-web.com/insights/ai-website-design-sameness/).
- Uniformity tells: identical padding, identical border-radius, identical card heights; motion tells: generic fade-ins, buttons that snap (https://www.925studios.co/blog/ai-slop-web-design-guide).
- Frosted glass / gradient-heavy heroes / perfect symmetry called out as "smells AI" (https://evartology.substack.com/p/a-complete-guide-5-simple-steps-to-build-awesome-web-apps-sites-that-doesnt-smell-ai).
- Emoji-as-UI reads lazy/unoriginal (https://www.mexc.com/news/982194).
- Additional internal calibration: three current AI default clusters to avoid even though they look "designed": (a) warm-cream `#F4F1EA` + high-contrast serif + terracotta accent; (b) near-black + single acid-green/vermilion; (c) broadsheet hairlines, zero radius, dense columns. The chosen direction must not collapse into any of these.

### 3. Style direction chosen (and why it dodges the clusters)

**Anchor set: hardware instrument panels — Teenage Engineering OP-1/OP-Z UI, Braun/Rams control faces, step sequencers.**

- Fit: the product's core object *is* a 7-step row (a week). A step-sequencer visual grammar encodes the data truthfully instead of decorating it. User builds music software (Tijdlijn) — the vocabulary is native to him.
- Dodges cluster (a): cool aluminum gray panel, not warm cream; industrial signal orange, not terracotta; grotesque type, not serif display.
- Dodges cluster (c): component-driven (chunky 1.5px control borders, 4px radii, recessed wells, dot grid), not broadsheet hairline columns.
- Dodges generic-AI tells: no gradients, no glass, segmented (not smooth) progress, one accent max per screen, monospace tabular numerals as the data voice.

### 4. Spec-craft notes applied

- Master prompt requirements per researched-feature skill: role framing, hard constraints, locked style bible (role-named hexes, per-object drawing specs, named anchors), feature checklist with numbers, forbidden list, self-checkable acceptance criteria with worked examples, milestone order with gates, mechanics-first-then-aesthetic-transform.
- All tunables in one constants block; all goal math as pure functions with an inline test harness (`?test=1`) so acceptance is literally executable.
