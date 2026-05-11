# Changelog

All notable changes to the Velocity Support Operations Dashboard are documented here.

---

## [2.2.0] — May 2026

### Changed
- **Scorecard email subject** — Subject line updated from `Your Support Scorecard — Week of [date]` to `Your Support Scorecard - Period of [period]`, so it correctly reflects the active filter (week, month, quarter, or fiscal year) rather than always showing today's date
- **Version display** — Version number now appears in two places: the load screen logo bar (`Velocity Solutions · v2.2.0`) and the top navigation bar alongside the dashboard title

### Fixed
- **Scorecard tooltip clipping** — Tooltips on the Scorecards tab were being truncated against the right and top edges of the viewport when hovering KPI tiles on cards in the right column or near the top of the page. Tooltips now use `position:fixed` and a JavaScript smart-placement listener that measures available viewport space before rendering. They prefer to open above the hovered element and fall back to below when space is insufficient. Horizontal position is clamped so the tooltip never bleeds off the right edge. The caret arrow dynamically repositions to always point back at the centre of the hovered element.

---

## [2.1] — May 2026

### Added
- **Global period filter** — Fiscal year, FY quarter, month, and week filters apply simultaneously across all tabs
- **Week filter** — Monday-anchored weeks from FY2027 start (March 2, 2026) onward
- **Insights tab** — AI-powered narrative analysis plus eight data-driven insight sections:
  - Volume signal vs. prior period
  - Backlog health
  - Top customers by case volume (with Company Profile)
  - Customers with aged open cases (with Company Profile)
  - Recurring themes keyword cloud
  - Topics appearing in aged open cases
  - Resolution time by product
  - Engineer throughput vs. open load
- **SLA compliance tracking** — Per-severity compliance bars on Overview tab; SLA % tile on each scorecard card
- **Queue health card** — Unassigned cases, unassigned product, and unassigned FI counts on Overview tab
- **Scorecard email** — Pre-composed email button on each scorecard card with configurable engineer email addresses and manager name
- **Configure panel** — Configurable performance tier thresholds (avg days, open cases, SLA compliance) and email settings, persisted in browser localStorage
- **Performance tier badges** — Elite / Strong / Needs Attention based on three configurable criteria
- **Enhanced scorecard cards** — Avatar initials, tier badge, visual bars (same-day closure, 7-day closure, longest open case), monthly volume mini chart, No product and No FI metric tiles
- **Company Profile field** — Shown in Insights customer tables and scorecard email action sections
- **Unassigned product / FI in scorecard email** — Pipe-separated case list with Company Profile, sorted by days open

### Changed
- Scorecards now recompute all metrics dynamically from raw data based on the selected period
- Open/closed determination uses `Resolved Date` field exclusively (not `Case Status`)
- Product field preserved as blank in data layer; "General" used only as display fallback
- Email button uses `window.open()` to preserve dashboard view in current tab
- Scorecard metric grid expanded to 2×3 (six tiles per card)

### Fixed
- Week filter was producing duplicate Monday/Tuesday entries due to UTC conversion in `weekKey()`; now uses local time exclusively
- `topProd` reference remained in scorecard return object after product label was removed, causing file load failure
- `Customer Acct #` field was not mapped in `processData`, causing customer insight sections to show no data

---

## [2.0] — April 2026

### Added
- Browser-based Excel file picker (SheetJS) — no Python or server required
- Interactive multi-tab dashboard: Overview, Volume trend, Aging & resolution, Scorecards, Open queue
- Global period filter (fiscal year, FY quarter, month)
- PDF export (html2canvas + jsPDF) — four landscape pages
- Engineer scorecards with hover tooltips on all metrics
- Open queue with filters, sorting, and pagination
- "Load new data" button to refresh without leaving the dashboard
- "Data refreshed" timestamp in header bar
- Configure panel with performance tier thresholds

### Changed
- Replaced static Python-generated HTML with fully dynamic browser-computed dashboard
- Replaced Excel (.xlsx) dashboard output with self-contained HTML file

---

## [1.0] — March 2026

- Initial Excel-based dashboard with static charts and scorecard tables
