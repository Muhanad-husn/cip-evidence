# CIP evidence site

Static pages for CIP, the Conflict Intelligence Platform: hand-written HTML and one stylesheet, no script. Served by GitHub Pages from `main` at https://muhanad-husn.github.io/cip-evidence/. The address stays on GitHub Pages (founder decision, 19 Nov 2026): no custom domain, no `CNAME` file.

| Path | Page |
|---|---|
| `index.html` | Platform |
| `methods.html` | Capabilities and methods |
| `methodology.html` | The published CIP methodology (from `docs/methodology/CIP_Methodology_Public.md` in the CIP repository) |
| `assurance.html` | Assurance |
| `deployment.html` | Deployment and integration |
| `company.html` | For investors |
| `period-brief.html` | The Period Brief, as published in September 2026 (17 May to 30 June 2026), now static |

`figures/full-record/` holds every figure the pages show, each with its CSV and provenance file, listed in `figures/full-record/figures.json`. They are produced in the CIP repository by `reports/evidence-site/build_figures.py` (issue #1423) and copied here unchanged. `figures/methods/` holds the example figure of each library method, on the Period Brief window. The earlier pages and captures are in git history.

Contact: founder@mergeseat.com - mergeseat.com
