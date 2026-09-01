# Design brief — "The Period Brief: 17 May – 30 June 2026"
**For:** Claude Design, working in this repo (cip-evidence-site) · **From:** MergeSeat CEO desk · **Date:** 1 Sep 2026
**Status:** build now with real data and figures; the analysis TEXT arrives separately after founder verification. Nothing publishes until the ship checks are signed (see §6).

## 1. What you are building and why

A third page for the live evidence site (https://muhanad-husn.github.io/cip-evidence/): the first CIP artifact a NON-technical reader can judge. The site's existing two pages prove method rigor to technical readers; this page shows what the machinery produces — a full analytical read of 17 May–30 June 2026 — with every claim traceable to the method run behind it. Two audiences on one page: an executive who gets the story in 90 seconds above the fold, and a domain expert who will hunt for one wrong or unsupported claim and discard the page on finding it. The design's job is to make checking effortless, because "you can check it and refuse it" is the product's entire brand.

Match the existing site's design language (dark ground, violet accent, the index.html register). A structural skeleton exists at `D:\The Merge Seat\CIP\period-brief\period-brief-skeleton.html` — treat it as the structural contract (sections, ordering, components), not a visual mock; restyle freely to the site's language.

## 2. Page structure (fixed order — do not reshuffle)

1. **Hook (above the fold):** the escalation story. "The escalation detector crossed its pre-set threshold on 7, 8 and 10 June. The threshold was journaled before the data arrived." One chart (see §4.1), one citation chip. Immediately below it, one honest sentence: on the original corpus the detector flagged 9 June; on the doubled corpus it revised itself to 10 June — the delta table shows both.
2. **Scorecard:** the six calibrated capabilities as cards. The FAILING one (actor centrality, completeness 0.2065 vs 0.5 floor) renders AS PROMINENTLY as the five passes, marked "below its own bar". This is deliberate and is the page's strongest credibility device — do not soften, shrink, or bury it.
3. **Delta table:** original vs expanded corpus, rendered verbatim from `run-output\delta_table.md`. Unedited, misses included.
4. **The period in five findings:** five text blocks — PLACEHOLDERS for now (the verified synthesis arrives separately). Each will carry ~120 words, a confidence label, and 1–3 citation chips.
5. **Three actors in depth:** United States, IRGC, Hezbollah. PLACEHOLDER text; the section carries a standing label: "Built on exploratory methods; selection anchored in the calibrated escalation run." Uses the actor figures (§4.3). Content is pending a data fix — leave clearly marked.
6. **How this was made:** short section; will include the narrative-shift filter statement (FDR 0.29 stated in-line) and the plottable-share caveat (the map speaks for 34.6% of events, not all).
7. **Appendix (collapsed):** every method readout from `run-output\methods\` with its figure, each labeled Tier A (calibrated, sweep journal ID shown) or "Tier B — Exploratory, uncalibrated, directional only" (visually distinct: dashed chip style). Centrality appears here flagged "below bar — worked example only".

## 3. Non-negotiable rules (these render the acceptance rule as UI)

- **Citation chips everywhere:** every factual claim carries `method · run date · sweep/journal ID · confidence · tier` as a compact inline chip. No chip = the sentence does not ship.
- **Tier separation is visual and structural:** Tier B content never appears in sections 1–5 as a finding; in the appendix it is unmistakably labeled.
- **Below-bar content is flagged, never hidden.**
- **Narrative shifts are never day-precise:** all shifts date to the 8 June window boundary by construction; any narrative-shift mention says "at the window boundary", and carries "≈29% of detections expected false" wherever the method is cited.
- **Past tense only, no predictions, no interpretive flourish** in any label, caption, or microcopy you write. You write STRUCTURE and captions only — never analytical claims. If a caption needs a fact, take it verbatim from the source readout.
- Framing line near the top, verbatim: **"Written by the platform. Checkable by you. Every sentence traces to a method that was calibrated before it saw the data."**

## 4. Data and figures (all real, all in `D:\The Merge Seat\CIP\period-brief\run-output\`)

1. **Escalation chart (the hook):** build from `tierA\escalation_detection_timeseries.csv` (date, event_count, baseline_median, robust_z, threshold 2.5, flagged). Annotate the three flagged days (7, 8, 10 Jun) AND the three high-z unflagged days (1 Jun z 4.48, 28 Jun z 5.06, 29 Jun z 2.95) — their per-day rejection reasons are being added as `tierA\escalation_rejected_days.json`; wire the annotations to read from it. An expert who sees high-z-unflagged without an explanation reads the whole page as inconsistent.
2. **Method figures:** the 26 PNGs in `methods\` (same stem as each readout) were rendered mechanically by `render_viz.py` from saved figure specs — deterministic, no LLM. USE THESE as-is; do not redraw or beautify data figures. A caption line under each: "Rendered mechanically from the saved run output." Tables in figures cap at 25 rows — say so where it applies; the geo map excludes unmapped records (65.4% of window events are not plottable — the caption must say the map speaks for 34.6%).
3. **Actor figures:** 3 per actor in `actors\`.
4. **Scorecard and delta numbers:** from `tierA\*.json` and `delta_table.md`. Never retype a number by hand — read from the files.
5. **Ignore `model-experiments-2026-09-01\`** — engineering experiments, not part of the Brief.

## 5. Practical constraints

- Self-contained page (inline CSS/JS; images may live as repo files beside the page — the site already serves static assets). Responsive; wide tables scroll in their own container, never the page.
- Fix the site-wide title while you are here if trivial: the two existing pages carry `<title>Bundled Page` — this page's title is "The Period Brief — CIP Evidence".
- Link the new page from `index.html` and `methods.html` navigation; link back to both.
- **Repo state (verified 2 Sep against the live site and remote):** remote `main` (HEAD 29c4542) IS the live site — `index.html` 2.3 MB + `methods.html` 1.8 MB, the 25 Aug redesign. The LOCAL working folder was stale (old 4.7 MB index, no methods.html) and has been synced to remote main on 2 Sep. Work from the synced files or pull `origin/main` fresh; do not trust any older local copy.

## 6. What you do NOT do

Do not write, summarize, or paraphrase any analytical content — the five findings and actor texts come from a founder-verified synthesis, delivered as final copy to drop into the placeholders. Do not publish or deploy: the page goes live only after the founder signs the six ship checks in `D:\The Merge Seat\CIP\period-brief\ACCEPTANCE_RULE_2026-08-30.md`, and publication is the founder's click. Your deliverable is the finished page with real scorecard/delta/figures and clearly marked text placeholders, committed to this repo on a branch.
