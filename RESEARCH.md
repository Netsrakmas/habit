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

## Addendum 2026-07-25 — design direction II (colourful, "hip") 

Brief: the owner asked for "veel beter, modern, kleurrijk, populair, hip". That
overrides §3 of PROMPT.md v1 (monochrome aluminium + one signal orange, no second
accent). This addendum records why the replacement is *neobrutalism with semantic
colour* and not the trend-listicle default.

### 1. What the generic answer would have been — and why it is rejected
Trend round-ups for 2026 converge on "dark themes + vibrant gradients + glowing
accent colours … electric neon, sunset coral, holographic silver"
(https://elements.envato.com/learn/color-scheme-trends-in-mobile-app-design,
https://www.designstudiouiux.com/blog/mobile-app-ui-ux-design-trends/).
That is verbatim the AI-slop cluster the 2026-07-24 addendum §2 already forbade:
gradient-heavy, glass, purple haze. Following it would make the app look *more*
generated, not less. Rejected.

### 2. Direction chosen — neobrutalism
Named, rule-based, and currently popular, which is what "hip" has to cash out to.
Two properties are non-removable: a hard border and a hard offset shadow
(https://www.typeui.sh/design-skills/neobrutalism, https://open-design.ai/plugins/design-system-neobrutalism/).
Beyond that: bold flat colour, chunky type, generous whitespace, no gradients.

Fit with what already exists is the deciding argument. WEEK v1 already has hard
1.5 px borders, exactly one hard offset shadow (`2px 2px 0`), 0/4/6 px radii,
segmented (never continuous) progress and tabular mono numerals. Neobrutalism is
that same grammar with the dial turned up and colour added — an amplification of
the existing identity, not a replacement. The OP-1/Braun instrument reading
survives; the app still looks like a control surface.

Known failure mode, from the critical sources: "raw" becomes unusable, with
anti-design layouts measured at 8–10 % task success on information-heavy pages
(https://brutalism.plus/neobrutalism-02); the ethical duty is that aesthetics must
not impede comprehension (https://medium.com/design-bootcamp/brutalism-to-neubrutalism-what-it-means-for-ux-a11y-464b3bdfb248).
NN/G's best practice: restrict to 2–3 dominant bold colours, meet WCAG
(≥ 4.5:1 body, ≥ 3:1 large), enforce contrast during design rather than after,
and keep ample whitespace (https://www.nngroup.com/articles/neobrutalism/).
WEEK is a precision instrument, so legibility outranks rawness wherever they
conflict. That is written into the bible as a rule, not a hope.

### 3. Colour is information, not decoration
The app has exactly four goal grammars. Giving each a signature hue makes a row
identifiable before any text is read, and makes the week strip parse at a glance.
That is categorical encoding, which is why a four-hue palette is defensible next
to NN/G's "2–3 colours" guidance: the hues carry identity, they are not ornament.
Ink and paper stay the dominant surfaces; colour appears only on marks that mean
something.

### 4. The palette was computed, not eyeballed
Candidates were run through the dataviz skill's validator
(OKLCH lightness band, chroma floor, Machado-Oliveira-Fernandes CVD ΔE, normal-vision
floor, WCAG vs surface), `--pairs all` because any two goal types share a screen.

Findings that decided it:
- Four saturated hues at *similar* lightness fail CVD hard (deep set: ΔE 3.0–4.3).
  Lightness spread across the band is what buys separation.
- Amber/yellow is unusable as a fourth hue next to orange: either outside the
  lightness band (L 0.82–0.85) or ΔE 1.5–6.5 against orange.
- Violet passes the numbers (ΔE 10.7) but is the purple AI cluster. Rejected on
  the earlier addendum's grounds, not on the maths.
- Cyan+pink passes deutan (13.7) but collapses for tritanopia (2.1). Rejected.

**Chosen: `#2440E8` blue · `#FF4A00` orange · `#00A878` green · `#F075C0` pink.**
CVD separation ΔE 10.3 (above the 8 target, not merely the 6 floor), tritan 13.7,
normal-vision floor 19.1, lightness band and chroma floor pass. Orange is the v1
signal colour, so the existing identity carries forward.

The validator's one WARN is mark-vs-surface contrast below 3:1 for three hues.
That WARN "obligates visible labels or a table view". Both reliefs are present by
construction: every coloured fill sits inside a 2.5 px ink border, and every row
carries a text type-badge plus per-cell day initials. No mark is ever colour-alone.

Glyph colour is per hue, computed: white on the blue (7.06:1), ink on orange
(5.63), green (6.21) and pink (7.24) — all above 4.5:1. Body ink on paper is
17.09:1, muted 6.49:1.
