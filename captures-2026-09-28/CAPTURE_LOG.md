# Capture log: dashboard figures after the four client-rendering fixes, on the 31 August snapshot

Captured 2026-09-28 from the analyst UI in `D:\CIP-code` on branch `client-capture-fixes-2026-09-28`
(tip `2bbe959`, eight commits on top of `main` at `2e9e285`; PR [#1131](https://github.com/Muhanad-husn/cip/pull/1131), squash-merged to `main` as `6b47b79`). Nothing here is a mock
or a re-render of Period Brief figures. Every PNG is a full-element Playwright screenshot of the Lab's
`active-canvas` after a real `POST /api/v1/capabilities/{id}/run`, approved out of the Feed. Every
`.envelope.json` beside it is that run's verbatim API response.

This pass re-captures the views the four fixes touch. It does not replace `captures-2026-08-31\`.

## The four fixes and the views they touch

| # | Fix | Commit | View(s) re-captured |
|---|---|---|---|
| 1 | The hotspot map banner reads the run's placed share (events against events), not 164 hotspots against 17,308 unplaceable events | `9147338`, `2bbe959` | 02 |
| 2 | The sparse-geo caveat quotes the run's own counts. The pinned corpus-wide "5882 of 11182" sentence and its `corpus_*` context keys are removed | `388f6d3` | 02 |
| 3 | The escalation time series marks each flagged day with a triangle and a label giving its acceleration ratio (`growth_ratio`) | `4d332a9`, `c556d02` | 01 |
| 4 | A confidence-signal / run-parameters readout renders beside every Tier-1 result, so modularity (clustering) and prior pseudo-count (source reliability) are on screen | `9a0f37b` | 05, 03 (and 04, which now carries the readout too) |

## Common provenance (identical for all five)

| Item | Value |
|---|---|
| Data root | `D:\CIP-sandbox` (read-only snapshot of `D:\CIP-data`, DEC-331); `AEO_DATA_ROOT=D:/CIP-sandbox` on the API. Snapshot not refreshed this session. |
| `data_as_of` shown in the snapshot caveat | `2026-08-31T03:32:37.841686+00:00` (read from each envelope's `snapshot_data` warning) |
| Snapshot taken (`snapshot_created_at`) | `2026-08-31T19:44:29Z` |
| Window | 2026-05-17 to 2026-06-30 |
| Calibration config served | `config/calibration/config_v6.json` (`calibration_config_version: 6`) for 02–05; **`config_v7.json`** for 01 (see its entry) |
| Neo4j | Aura. At session start the hostname did not resolve (NXDOMAIN) and every run returned `503 store_unavailable`. The founder resumed the instance and the API was restarted, after which startup completed with no `stores_degraded`. Node and edge counts were not taken this time. |
| Servers | `uv run --with sentence-transformers uvicorn cip_api.main:app --port 8000` (no `--reload`; restarted after the last code change, so 01 and 05 ran against tip `c556d02`); `corepack pnpm dev --port 5173 --strictPort` |
| Browser | Playwright Chromium (`@playwright/test` from `frontend/node_modules`), headless, device pixel ratio 1, dark theme |
| Capture method | Standalone Node driver (not committed, kept in the session scratchpad), same flow as the 31 Aug pass. Select capability in the Capabilities tab, fill both date inputs, Run, wait for the run response, Approve the pending Feed card, wait for the surface, collapse both side panels, dispatch `resize`, hide `active-canvas-header` (the "Promote to" toolbar), shrink `active-canvas` to its content, adjust the viewport until `active-canvas` measures exactly 968 px (viewport 1080 px), then `active-canvas.screenshot()` |
| Width | 968 px in all five, verified from the PNG IHDR |

## Per file

### 01_escalation_detection.png (968 x 669) — re-captured on config_v7

| Item | Value |
|---|---|
| Capability / surface | `escalation_detection` / `timeline-surface` |
| run_id | `23c69814d1164d5cbe3cc209c0761701` |
| Calibration config | **`config_v7.json`** (`calibration_config_version: 7`). v7 re-fits escalation_detection on this snapshot (`escalation_detection_fit_v2.json`, journal `escalation_detection_sweep_005.json`, identical to the Brief's `sweep_004`). The other four captures ran on config_v6, whose other five blocks v7 carries forward unchanged. |
| Caveats in frame | 1 (`snapshot_data`), fully expanded and legible |
| Headline | Now matches the published Brief. Run parameters: baseline 7, anomaly threshold 2.5, acceleration threshold 1.5. The chart marks `▲ 7 Jun · acceleration 1.84`, `▲ 8 Jun · acceleration 1.61`, `▲ 10 Jun · acceleration 1.65`. Envelope robust-z is 7.69 / 5.09 / 2.79. 1 Jun, 28 Jun and 29 Jun are level anomalies the acceleration gate declined (1.18 / 0.87 / 1.08), so they are not marked. No day is composition-rejected. |
| Superseded | On config_v6 (cal_2's 2026-08-23 fit, baseline 21) this view showed robust-z 7.22 / 9.11 / 4.80, had 9 Jun anomalous, and did not show the 1/28/29 Jun anomalies. That contradicted the Brief. |

### 02_geospatial_hotspot_analysis.png (968 x 1242)

| Item | Value |
|---|---|
| Capability / surface | `geospatial_hotspot_analysis` / `map-surface` |
| run_id | `1f9ea0bb89964f4389d8e4536c495d80` |
| Caveats in frame | 5: `sparse_geo`, `coarse_geocode_precision`, `classification_not_validated_on_window`, `truncated_by_limit`, `snapshot_data` |
| Result line in frame (fix 1) | `Showing 164 map points drawn from 9,156 of 26,464 events (34.6% placed); 17,308 events are text-only and not on the map.` ("drawn from", not "covering": the hotspots capture 6,486 of the 9,156, per the readout; reworded in `2bbe959` after review) It was `Showing 164 of 17472 places (1% geo-resolved); 17308 places are text-only …` on 31 Aug. |
| Sparse-geo caveat (fix 2) | Reads `This map draws 9156 of the window's 26464 events (34.6% plottable); 17308 (65.4%) cannot appear on it at all, of which 4735 (17.9%) are named-but-uncoordinated … This is the degraded State B of Tier-1 Grounding §4.4 (DEC-346), measured on this run's window.` The pinned corpus-wide `5882 of 11182` sentence is gone. |
| Headline | `hotspots_detected: 164`; readout shows Events In Analysis 9,156, Plottable Share 0.346 |
| Map framing | Unchanged: one hotspot is the known New York mis-geocode (#1043), disclosed in the coarse-geocode caveat. |

### 03_source_reliability_audit.png (968 x 926)

| Item | Value |
|---|---|
| Capability / surface | `source_reliability_audit` / `results-surface` (Plotly table) |
| run_id | `fb68f715bbbf40fc8278102ef4697448` |
| Caveats in frame | 2: `attribution_cohort`, `snapshot_data` |
| Headline (fix 4) | **Prior Pseudo Count 160** now renders in the Run Parameters row. Cohort caveat reads 15 of 44 declared sources, 10.5% (41390 of 394233 assertions). The readout shows Sources Audited 15 / Sources Declared 44. The table shows all 15 rows. |
| Table height | **No override.** All 15 rows render at the shipped height, because a table's container now grows with its row count (380 px floor, 900 px cap; commit `62c3571`). The 31 Aug pass needed a hand-set 470 px. |
| Cosmetic | First and last table columns are still narrower than their text (unchanged, not in scope). |

### 04_narrative_shift_detection.png (968 x 1270)

| Item | Value |
|---|---|
| Capability / surface | `narrative_shift_detection` / `timeline-surface` |
| run_id | `2129a73fdecf4eeba549dea2076f5eb9` |
| Caveats in frame | 7: `narrative_layer_not_in_system_of_record`, `window_insufficient_for_arc`, `arc_not_observable`, `detection_is_a_rate`, `no_tier2_floor`, `framing_unlabelled`, `snapshot_data` |
| Headline | Readout (fix 4) shows Narratives Published 261, Narratives Eligible 1,515, Shift Magnitude Threshold 0.08599. Re-captured only because the new readout appears on this view too; its figure is unchanged. |

### 05_actor_network_clustering.png (968 x 1174)

| Item | Value |
|---|---|
| Capability / surface | `actor_network_clustering` / `network-surface` |
| run_id | `fa6d1b5522c94ab0b5c1249b61bab81d` |
| Caveats in frame | 2: `undated_relationships`, `snapshot_data` |
| Headline (fix 4) | **Modularity Score 0.7238**, **Published Partition Clusters 85** (over 2,225 actors), scored partition 268 communities over 2,591 actors, minimum 0.3 met. These are `meta.confidence_signal` in the envelope, now rendered above the graph. |
| Render-cap note in frame | `2591 actors returned, 300 kept by the render cap` (header badge); footer `6 of 14 community hulls hidden — too scattered to outline` (8 of 14 on 31 Aug; the force layout is not deterministic) |
| Capture steps specific to this one | Clicked "Load more" twice (100 to 300 actors), pinned the graph container (`[class*="graphContainer"]`) to 760 px, clicked "Re-run layout" and waited 30 s, set the canvas width, then re-ran the layout and waited another 30 s. The zoom is the shipped surface's own fit. |
| Note | A first attempt pinned the wrong element (the graph grew to 1,614 px and zoomed in) and was discarded. This file is the re-capture. |

## Known issues from the 25 August / 31 August passes, status on this run

| Issue | Status |
|---|---|
| `confidence_signal` renders nowhere in the client | **Fixed** (fix 4). Visible in all five captures. |
| Escalation time series does not annotate flagged days | **Fixed** (fix 3). Visible in 01. |
| Hotspot banner "1% geo-resolved" | **Fixed** (fix 1). Visible in 02. |
| Sparse-geo caveat quotes pinned corpus constant | **Fixed** (fix 2). Visible in 02. |
| Plotly table height clips the reliability table | **Fixed** (the table height follows its rows). 03 was captured with no override. |
| Playwright worker lingers after runs | Still holds. The first driver run stayed alive after its `DONE` line and was killed (`Get-CimInstance Win32_Process` filtered on `capture.mjs`). Results were read from the driver's `RESULT` / `DONE` log lines, not its exit code. |

## Tests run for the touched components

- `tests/cip_methods/test_tier1_geospatial_hotspot_analysis.py`, `tests/scripts/test_calibrate_geospatial_hotspot*.py`: 97 passed
- `tests/cip_methods/test_tier1_escalation_detection.py`: 38 passed (two new tests)
- Scoped `tests/cip_api` + `tests/cip_methods` for escalation / hotspot / tier1 / capabilities: 304 passed
- `bash scripts/check.sh`: ruff, format, mypy (406 files), lint-imports (8 kept), fast lane 1285 passed / 1 skipped / 1 xfailed, slow lane 14 passed
- Frontend (`corepack pnpm`): lint clean; vitest 58 files / 1286 tests passed (new `ResultSignal.test.tsx`, new banner tests in `map-data.test.ts`, extended `ActiveCanvas.test.tsx`); build OK
