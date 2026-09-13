# Capture log — five dashboard figures on the 31 August snapshot

Captured 2026-09-13 (session ran into 2026-09-14 local) from the shipped analyst UI in `D:\CIP-code`
at `dcbefa6` plus the uncommitted working tree described under "What changed in the repo".
Nothing here is a mock or a re-render of Period Brief figures: every PNG is a full-element
Playwright screenshot of the Lab's `active-canvas` after a real `POST /api/v1/capabilities/{id}/run`,
approved out of the Feed, and every `.envelope.json` beside it is that run's verbatim API response.

## Common provenance (identical for all five)

| Item | Value |
|---|---|
| Data root | `D:\CIP-sandbox` (read-only snapshot of `D:\CIP-data`, DEC-331); `AEO_DATA_ROOT=D:/CIP-sandbox` on the API |
| `data_as_of` shown in the snapshot caveat | `2026-08-31T03:32:37.841686+00:00` |
| Snapshot taken (`snapshot_created_at`) | `2026-08-31T19:44:29Z` |
| Window | 2026-05-17 to 2026-06-30 |
| Calibration config served | `config/calibration/config_v6.json` (`calibration_config_version: 6` in every envelope) |
| Neo4j | Aura, up, 83,392 nodes / 310,178 edges at session start |
| Servers | `uv run --with sentence-transformers uvicorn cip_api.main:app --port 8000`; `corepack pnpm dev --port 5173` (started separately; `cip serve all` fails spawning the frontend on Windows) |
| Browser | Playwright Chromium (`@playwright/test` 1.60 from `frontend/node_modules`), headless, device pixel ratio 1, dark theme (the app is dark-only) |
| Capture method | Standalone Node driver (not committed, kept outside the repo): select capability in the Capabilities tab, fill both date inputs, Run, wait for the run response, Approve the pending Feed card, wait for the surface, collapse both side panels, dispatch `resize`, hide the `active-canvas-header` (the "Promote to" toolbar), set `active-canvas` to shrink to its content, then adjust the viewport width until `active-canvas` measures exactly 968 px (viewport 1080 px wide) and take `active-canvas.screenshot()` |
| Width | 968 px in all five, verified from the PNG IHDR |

### Why config_v6 exists (and why v5 could not produce these captures)

The 31 August report numbers came from the calibration scripts run directly on the expanded corpus;
nothing that night composed a new configuration. On `config_v5` the dashboard returned 174 hotspots,
272 of 1,792 narratives, refused source reliability (409, the stale fit fails its cohort-consistency
gate) and refused clustering (422 `hard_limit_violation`, 6,796 actors against `max_nodes: 5000`).
The four corpus-dependent capabilities were therefore re-fitted on the same snapshot with `--write`
to new journal and fragment paths, and composed into `config_v6` (parent v5, calibration date
2026-09-13). Each re-fit reproduced the report exactly:

| Capability | Journal | Fragment | Reproduced |
|---|---|---|---|
| geospatial_hotspot_analysis | `reports/calibration/geospatial_hotspot_sweep_004.json` | `config/calibration/fits/geospatial_hotspot_fit_v2.json` | 164 hotspots; 9,156 of 26,464 plottable (34.6%) |
| narrative_shift_detection | `reports/calibration/narrative_shift_detection_sweep_003.json` | `config/calibration/fits/narrative_shift_detection_fit_v2.json` | 261 detected of 1,515 eligible; threshold 0.085985 |
| source_reliability_audit | `reports/calibration/source_reliability_audit_sweep_003.json` | `config/calibration/fits/source_reliability_audit_fit.json` (first fragment for this capability) | 15 of 44 sources; prior pseudo-count 160 |
| actor_network_clustering | `reports/calibration/actor_network_clustering_sweep_004.json` (+ `actor_network_clustering_feed_proposal_003.json`) | `config/calibration/fits/actor_network_clustering_fit_v2.json` | 85 clusters; modularity 0.723786 |

`config_v6.json` recomposed to a temp path is byte-identical (checksum
`84ba09a2c8341a1542fadca6af889b5d6d49b9fb4fc5e8b38b140bc566d469d7`).

## Per file

### 01_escalation_detection.png — 968 x 545

| Item | Value |
|---|---|
| Capability / surface | `escalation_detection` / `timeline-surface` |
| run_id | `51cc0f77cb2e46139277c4297d33cd5d` |
| Caveats in frame | 1 (`snapshot_data`), fully expanded, `data as of 2026-08-31T03:32:37.841686+00:00, snapshot taken 2026-08-31T19:44:29Z` legible |
| Headline | Flagged days **2026-06-07, 2026-06-08, 2026-06-10** (`data[].escalation == true` in the envelope); 06-09 is anomalous but not composition-confirmed |
| Known issue applied | The time series does not annotate flagged days (25 August finding, still holds). The dates are not visible in the PNG; read them from `01_escalation_detection.envelope.json`. |

### 02_geospatial_hotspot_analysis.png — 968 x 1072

| Item | Value |
|---|---|
| Capability / surface | `geospatial_hotspot_analysis` / `map-surface` |
| run_id | `42de7533a4c74cca96543c940527c54b` |
| Caveats in frame | **5**, not the 6 the request estimated: `sparse_geo`, `coarse_geocode_precision`, `classification_not_validated_on_window`, `truncated_by_limit`, `snapshot_data`. The numbers inside them match the report. |
| Headline | Sparse-geo caveat reads **9156 of the window's 26464 events (34.6% plottable)**; classification caveat reads **19 of 164 hotspots are persistent, 4 are spikes**; envelope `hotspots_detected: 164` |
| Result line in frame | `Showing 164 of 17472 places (1% geo-resolved); 17308 places are text-only and not on the map.` |
| Defect (captured as-is, per instruction) | That banner is wrong on this data: `MapSurface` divides the 164 hotspot points by `unmapped_count` (17,308 unplotted events), so "places" are hotspots on one side and events on the other and "1% geo-resolved" contradicts the 34.6% the caveat above it states. Frontend banner bug in `MapSurface` / `map-data.ts buildUnmappedBanner`; `unmapped_count` in `geospatial_hotspot_analysis.py` was deliberately not changed. |
| Map framing | Fits one world-spanning cluster because one hotspot is a known mis-geocode to New York (#1043, disclosed in the coarse-geocode caveat). Not a bug; left as is. |

### 03_source_reliability_audit.png — 968 x 745

| Item | Value |
|---|---|
| Capability / surface | `source_reliability_audit` / `results-surface` (Plotly table) |
| run_id | `cbaa496bce2b49b78a434a93061df99e` |
| Caveats in frame | 2: `attribution_cohort`, `snapshot_data` |
| Headline | Cohort caveat reads **15 of 44 declared sources**, **10.5% of the corpus's assertion history (41390 of 394233 assertions)**; table shows all **15** rows. **Prior pseudo-count 160** is not rendered anywhere in the client; it is `meta.params_echo` / `scoring_parameters.sample_size_minimum` in `03_source_reliability_audit.envelope.json`. |
| Known issue applied | Plotly table height clips 12 of 15 rows at the shipped 380 px container. Capture-only workaround: the `[class*="chartContainer"]` inside `results-surface` was set to `height: 470px` in the page DOM and a `resize` event dispatched so react-plotly redrew. Nothing committed. |
| Cosmetic | The first and last table columns are narrower than their text (`abu_ali_express_tg` shows as `bu_ali_express_t`, `event_count_contributed` header truncated). Plotly column sizing in the shipped view; not altered. |

### 04_narrative_shift_detection.png — 968 x 1100

| Item | Value |
|---|---|
| Capability / surface | `narrative_shift_detection` / `timeline-surface` |
| run_id | `0e87d55427be4f328883aba96ede9f68` |
| Caveats in frame | **7**, not the "two warnings" the request estimated: `narrative_layer_not_in_system_of_record`, `window_insufficient_for_arc`, `arc_not_observable`, `detection_is_a_rate`, `no_tier2_floor`, `framing_unlabelled`, `snapshot_data` |
| Headline | Detection caveat reads **75.75 of 1515 eligible narratives -- an expected false-discovery share of 0.2902 among the 261 detected**; envelope `detected: 261`, `narratives_eligible: 1515` |

### 05_actor_network_clustering.png — 968 x 1027

| Item | Value |
|---|---|
| Capability / surface | `actor_network_clustering` / `network-surface` |
| run_id | `4fd73f68d19e41c6ad7d403d299244cf` |
| Caveats in frame | 2: `undated_relationships`, `snapshot_data` |
| Render-cap note in frame | `2591 actors returned, 300 kept by the render cap` (header badge); footer `8 of 14 community hulls hidden — too scattered to outline` |
| Headline | **85 clusters, modularity 0.723786 (0.7238)** are not rendered anywhere in the client: they are `meta.confidence_signal.value` and `meta.confidence_signal.published_partition.clusters` in `05_actor_network_clustering.envelope.json` (the 14 hulls in the footer are the communities among the 300 rendered actors, not the 85). |
| Capture steps specific to this one | After the surface rendered: clicked "Load more" twice so all 300 kept actors are drawn (the initial reveal is 100), pinned the graph box to 760 px, clicked "Re-run layout", waited 30 s for the force simulation to settle. The zoom is the shipped surface's own fit. |
| Known issue applied | `confidence_signal` renders nowhere in the client (25 August finding, still holds). |

## Known issues from the 25 August pass, status on this run

| Issue | Status |
|---|---|
| `confidence_signal` renders nowhere in the client | Still holds; affected 05 (modularity) and 03 (prior strength). Envelopes carry them. |
| Plotly table height clips the reliability table | Still holds; capture-only 470 px override on 03, not committed. |
| Escalation time series does not annotate flagged days | Still holds; affected 01. Envelope carries them. |
| Playwright worker lingers after runs | Still holds; a `node.exe` running the driver stayed up after its last log line each time and was killed between runs (`Get-CimInstance Win32_Process` filtered on `capture`). Results were read from the driver's log file, not its exit. |

## What changed in the repo (all uncommitted, nothing pushed)

- `config/calibration/config_v6.json` (new), four new fit fragments and four new journals (listed above), `reports/calibration/actor_network_clustering_feed_proposal_003.json`.
- `scripts/compose_calibration_config.py`: version-6 fragment overrides; a v6-aware "what is fitted here" paragraph (the v5 sentence "source_reliability_audit is carried forward … verbatim" would be false); the "409 capability_gated unconditionally" paragraph is only composed for versions ≤ 5 (DEC-350 opened the socket before v6). v3, v4 and v5 still recompose byte-identically.
- `scripts/calibrate_source_reliability.py`: `--fragment-path` and `--calibration-date`; the admin-notes unresolved share is computed rather than the pinned "88.4%".
- `config/methods/registry.yaml`: `hard_limits.max_nodes` 5000 → 10000 with the reason (the key is shared by bounded Neo4j traversals and the corpus-wide clustering capability). `tests/cip_api/test_integration.py` (10001 / 10000) and `tests/cip_api/test_integration_neo4j.py` (`<= 10000`, neo4j-marked, not run here) updated.
- `src/cip_methods/.../source_reliability_audit.py`: `ASSERTIONS_RESOLVED_VIA_CLAIM_PATH = 41_390`, `ASSERTIONS_IN_CORPUS_AT_FIT = 394_233` (10.5%), docstring updated; `tests/scripts/test_calibrate_source_reliability_published_fit.py` asserts 10.5.
- Tests added: v6 section in `tests/scripts/test_compose_calibration_config.py`; fragment CLI and derived-share tests in `tests/scripts/test_calibrate_source_reliability.py`.
- `geospatial_hotspot_analysis.py` `unmapped_count`: **not changed**, on purpose (see 02).

Footnote, no action: a raw `SELECT COUNT(*) FROM assertions` on the snapshot gives 418,280, and that is
the denominator the re-fit's own fragment `admin_notes` states ("41390 of 418280 corpus assertions").
The report's 394,233 is what the evidence page already publishes, so the pinned constants that feed
the dashboard caveat match it (10.5%); the fragment's 418,280 gives 9.9%.

## Gates run on the working tree

`uv run ruff check` clean; `uv run ruff format --check` clean; `uv run mypy src/` clean;
`uv run lint-imports` 8 kept / 0 broken; `uv run pytest -n auto tests/cip_methods tests/scripts tests/cip_api`:
2,805 passed, 5 skipped, 1 xfailed, 70 failed (3 min 07 s) when launched from PowerShell, every one of
the 70 the `C:\WINDOWS\system32\bash.EXE` WSL shim (`execvpe(/bin/bash) failed`) in the ten
bash-driver test files; those ten files re-run from Git Bash: 85 passed, 0 failed (3 min 14 s).
No failure touches the changes above.
