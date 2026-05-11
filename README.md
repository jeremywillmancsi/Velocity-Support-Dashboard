# Velocity Support Operations Dashboard
### README & Feature Documentation
**v2.2.0 · FY2027 · Velocity Solutions Support Operations · May 2026**

---

## Table of Contents

1. [Overview](#1-overview)
2. [Loading Data](#2-loading-data)
3. [Global Period Filter](#3-global-period-filter)
4. [Tab Reference](#4-tab-reference)
5. [Scorecard Email](#5-scorecard-email)
6. [PDF Export](#6-pdf-export)
7. [Configure Panel](#7-configure-panel)
8. [Fiscal Year Reference](#8-fiscal-year-reference)
9. [SLA Targets Reference](#9-sla-targets-reference)
10. [Metric Formula Reference](#10-metric-formula-reference)
11. [Technical Notes](#11-technical-notes)

---

## 1. Overview

The Velocity Support Operations Dashboard is a self-contained, browser-based analytics tool designed for the Velocity Solutions support management team. It reads support case data exported from Microsoft Dynamics 365 and produces interactive visualizations, engineer scorecards, SLA tracking, queue health monitoring, and AI-powered insights — all without requiring any server, database, or IT infrastructure.

> **Privacy note:** The dashboard is a single HTML file. All data processing occurs in the browser. No case data, customer information, or engineer metrics are transmitted to any external server. AI narrative analysis in the Insights tab calls the Anthropic API using only computed summary statistics — never raw case records.

### 1.1 Key Capabilities

- Interactive multi-tab dashboard with a global period filter
- Engineer scorecards with performance tiers, SLA compliance, and email generation
- AI-powered insights with data-driven customer, topic, and volume analysis
- SLA compliance tracking across four severity levels
- Queue health monitoring for unassigned cases, products, and financial institutions
- PDF export of all dashboard sections
- Configurable performance tier thresholds and engineer email addresses

### 1.2 Data Source

The dashboard reads a Microsoft Excel (`.xlsx`) file exported from a saved view in Microsoft Dynamics 365. The following columns are required:

| Column Name | Description |
|---|---|
| `Case Number` | Unique case identifier (e.g. `CAS-1234567-XXXXXXX`) |
| `Case Title` | Short description of the support request |
| `Customer Acct #` | Numeric account identifier. Account `999` = unassigned FI. |
| `Company Profile` | Customer company name and location (e.g. `First Bank (Louisville, KY)`) |
| `Support Product` | Product area the case relates to. Blank = unassigned. |
| `Owner` | Engineer assigned. `Velocity Solutions Support` = unassigned. |
| `Case Severity` | One of: `1-Critical`, `2-Medium`, `3-Low`, `4-General` |
| `Created On` | Date and time the case was opened |
| `Resolved Date` | Date and time the case was resolved. Blank = open case. |
| `Case Status` | Current workflow status (`Open`, `Ready for Review`, `Resolved`, etc.) |
| `# of Days From Created to Resolved` | Pre-computed resolution time in calendar days |

> **Open vs. closed logic:** A case is considered open if and only if the `Resolved Date` field is blank. `Case Status` is used only for display purposes. All 218 "Ready for Review" cases have a `Resolved Date` and are therefore treated as closed.

---

## 2. Loading Data

The dashboard opens to a load screen on first access. To populate the dashboard:

1. Click **Select Excel file**
2. Navigate to your Dynamics export (`.xlsx`) on any local or OneDrive-synced path
3. Select the file — the dashboard processes it immediately in the browser

To refresh with updated data, click the **Load new data** button in the top-right corner of the dashboard at any time. The selected period filter is preserved across refreshes. A "Data refreshed" timestamp in the header bar shows when data was last loaded.

> **Important:** The file picker is the only way to load data. The browser's security sandbox prevents the dashboard from reading files automatically from a path, even if the path is configured. This is a deliberate browser security feature, not a dashboard limitation.

---

## 3. Global Period Filter

A period filter bar is visible below the header on all tabs. Every metric, chart, and scorecard on the dashboard responds to this filter simultaneously.

| Period Type | Behaviour |
|---|---|
| **Fiscal year** | Full fiscal year. FY2027 = March 1, 2026 – February 28, 2027. Defaults to the most recent FY on load. |
| **FY quarter** | Q1=Mar–May, Q2=Jun–Aug, Q3=Sep–Nov, Q4=Dec–Feb. All quarters present in the data are listed. |
| **Month** | Individual calendar month. All months present in the data are listed, most recent first. |
| **Week** | Monday-anchored weeks from FY2027 start (March 2, 2026) onward. New weeks appear automatically as data is loaded. |

Metrics that are always all-time (independent of the filter) are noted with an `(all time)` label on their chart titles. These are: open case aging, open load by engineer, total open count, >90 days open, and avg open age.

---

## 4. Tab Reference

### 4.1 Overview

The Overview tab provides a high-level summary of the selected period.

#### KPI Cards

| KPI | Definition |
|---|---|
| Cases created | COUNT of cases with a `Created On` date within the selected period |
| Cases closed | COUNT of cases with a `Resolved Date` within the selected period |
| Total open | COUNT of all cases with no `Resolved Date` (all time, not period filtered) |
| Ready for review | COUNT of cases with `Case Status = "Ready for Review"` (all time) |
| Resolved all time | COUNT of all cases that have a `Resolved Date`, regardless of period |
| Closed within 7 days | % of closed cases (with days data) resolved in 7 or fewer calendar days |

#### Charts

- **Weekly case volume** — grouped bar chart of cases created vs. closed by week within the period
- **Cases by product** — horizontal bar chart of case volume by Support Product for the period
- **Open case aging** — doughnut chart of all-time open cases grouped by age bucket *(all time)*
- **Resolution time** — bar chart of closed cases by time-to-close bucket for the period
- **Open load by engineer** — bar chart showing all-time open case count per engineer, color-coded red/orange/blue by severity *(all time)*

#### SLA Compliance Card

A full-width card below the charts shows SLA performance for the selected period. SLA targets are:

- **Critical** (`1-Critical`): resolve within 1 day
- **Medium** (`2-Medium`): resolve within 3 days
- **Low** (`3-Low`): resolve within 7 days
- **General** (`4-General`): resolve within 14 days

Each severity shows a progress bar, met/total case count, and percentage. An overall compliance rate with a status badge appears in the top right of the card:

- **On track** — ≥ 80%
- **At risk** — ≥ 60%
- **Below target** — < 60%

SLA is calculated on closed cases only.

#### Queue Health Card

Three tiles highlight open cases requiring immediate attention *(all time, not period filtered)*:

- **Unassigned cases** — open cases owned by `Velocity Solutions Support` (not yet assigned to an engineer)
- **Unassigned product** — open cases where the `Support Product` field is blank
- **Unassigned FI** — open cases where `Customer Acct #` is `999` (not linked to a real customer)

---

### 4.2 Volume Trend

A line chart showing cases created (solid line) and cases closed (dashed line) over time within the selected period. Toggle between **weekly** and **daily** granularity using the buttons above the chart. Summary stats below the chart show total created, total closed, averages per day, and net backlog change.

---

### 4.3 Aging & Resolution

Two charts with supporting tables:

- **Open case aging** — bar chart of all-time open cases by age bucket (0–7d, 8–14d, 15–30d, 31–60d, 61–90d, >90d). Table shows count and percentage for each bucket.
- **Resolution time** — bar chart of closed cases (in period) by days-to-close bucket. Table shows count and percentage. Key metrics card shows avg, median, same-day rate, 7-day rate, and open cases >90 days.

---

### 4.4 Scorecards

One card per engineer, sorted by total open case count. All period-sensitive metrics (Assigned, Cases closed, Avg days to close, SLA compliance, resolution rate) reflect the selected global filter period. Open case metrics (Total open, >90 days, Avg open age, No product, No FI) are always all-time.

#### Card Layout

| Section | Contents |
|---|---|
| Header | Avatar initials, engineer name, performance tier badge, open case count badge |
| Stats row | Cases assigned, Cases closed, Avg days to close (with median below) |
| Visual bars | Same-day closure %, Closed within 7 days %, Longest open case |
| Metric tiles | Total open, >90 days, Avg open age, SLA compliance, No product, No FI |
| Resolution bar | Resolved cases as a % of assigned cases for the period |
| Mini chart | Monthly case volume by calendar month (2026) for context |
| Email button | Generates a pre-composed email for the engineer (see [Section 5](#5-scorecard-email)) |

#### Performance Tier Badges

Each engineer receives one of three tier badges based on three configurable criteria that must all be met simultaneously:

| Tier | Avg Days to Close | Open Cases | SLA Compliance |
|---|---|---|---|
| **Elite** | ≤ 10 days | ≤ 15 | ≥ 90% |
| **Strong** | ≤ 30 days | ≤ 30 | ≥ 80% |
| **Needs Attention** | Fails any one criterion above | | |

> SLA compliance is skipped from tier evaluation when no eligible closed cases exist in the selected period. Thresholds are fully configurable in the Configure panel (see [Section 7](#7-configure-panel)).

#### Metric Tooltips

Every metric tile and visual bar on the scorecard has a hover tooltip explaining the calculation formula, plain-English description, and scope (period vs. all-time). Tooltips use smart viewport-aware positioning — they open above the hovered element when space allows, fall back to below when near the top of the screen, and are horizontally clamped so they never bleed off the right edge of the viewport.

---

### 4.5 Open Queue

A filterable, sortable table of all currently open cases *(all time, not period filtered)*. Filters available:

- **Owner** — filter to a specific engineer
- **Status** — filter by `Case Status` value
- **Age** — show only cases over 90, 60, or 30 days, or under 7 days
- **Search** — free-text search across Case Number, title, and owner

Column headers for Case Number, Owner, and Days open are sortable by clicking the ⇅ icon. Results are paginated at 30 cases per page. Age badges are color-coded: green (≤30d), yellow (≤60d), orange (≤90d), red (>90d).

---

### 4.6 Insights ✦

The Insights tab surfaces analytical findings computed directly from the loaded data for the selected period. All data sections update immediately when the period filter changes.

#### AI Narrative Banner

A navy banner at the top shows a 150–200 word management briefing generated by Claude (Anthropic). The AI receives only computed summary statistics — no raw case records, customer names, or personal data. The narrative is grounded in the actual numbers and written in second-person for the support manager. It is clearly labelled as AI-generated analysis.

> **Note:** The AI narrative requires an internet connection to the Anthropic API. If unavailable, all eight data-driven insight sections below the banner still function fully using local data only.

#### Data Insight Sections

| Section | What It Shows |
|---|---|
| Volume signal | Cases created and closed vs. the prior equivalent period. Badge and recommended action. |
| Backlog health | Open case count, % over 90 days, oldest case. Status badge and action. |
| Top customers by volume | Top 8 accounts by case count in the period, with company name and % of total. |
| Customers with aged cases | Top 8 accounts with open cases >30 days, sorted by oldest case, with company name. |
| Recurring themes | Keyword cloud from case titles in the period (stopwords filtered, including "bank"). |
| Topics in aged cases | Same keyword analysis applied only to open cases >60 days — shows what topics get stuck. |
| Resolution time by product | Average days to close by product for cases resolved in the period. |
| Engineer throughput | Compact table of assigned, closed, open, and close rate per engineer. |

---

## 5. Scorecard Email

Each scorecard card has an **Email scorecard** button at the bottom. Clicking it runs a two-step flow: first it generates and downloads a PDF of that engineer's scorecard, then it opens the default mail client (Outlook or equivalent) with a pre-composed email. The dashboard tab remains open throughout.

### 5.1 Scorecard PDF

The downloaded PDF is a portrait A4 document named `Scorecard_[Engineer]_[Date].pdf` (e.g. `Scorecard_Wake_Donovan_2026-05-11.pdf`). It contains the engineer's full scorecard card rendered at high resolution, with the same navy header and footer style as the full dashboard export. Performance tier badges and the email button are automatically hidden in the PDF capture.

> **Attachment note:** The `mailto:` protocol used to open the email does not support programmatic file attachment — this is a hard browser security boundary. The two-step flow is the standard workaround: the PDF saves to your Downloads folder and you attach it manually to the email that opens.

### 5.2 Email Content

The email body is plain text with up to five sections:

1. **Period summary** — Cases assigned, Cases closed, Resolution rate
2. **Response quality** — Avg days to close, Median, Same-day closure %, Closed within 7 days %, SLA compliance
3. **Open cases** — Currently open, Open >90 days, Avg open case age, Longest open case
4. **Action required: Unassigned product** *(only if applicable)* — pipe-separated table listing `Case Number | Days Open | Company | Title`, sorted by days open descending
5. **Action required: Unassigned FI** *(only if applicable)* — same pipe-separated table format

Action required sections only appear when there are cases to list. An engineer with no unassigned product or FI cases receives a shorter email with only the three metric sections.

### 5.3 Email Fields

| Field | Value |
|---|---|
| To | Engineer's email address (configured in the Configure panel) |
| CC | Manager's email address (configured in the Configure panel) |
| Subject | `Your Support Scorecard - Period of [active period]` |
| Sign-off | Manager's name from the Configure panel |

---

## 6. PDF Export

The **Export PDF** button in the top-right header bar generates a multi-page landscape A4 PDF of the dashboard. It is available after data is loaded.

| Page | Contents |
|---|---|
| Page 1 | Overview: KPI cards (rendered natively) and the two chart rows |
| Page 2 | Volume trend chart and summary stats |
| Page 3 | Aging & resolution charts with tables |
| Page 4 | Engineer scorecards (continues to page 5 if needed) |

Each page has a navy header band showing the section title, period label, and filename, and a footer with the export date and page numbers. The PDF filename includes the export date (e.g. `Velocity_Support_Dashboard_2026-05-01.pdf`).

> **PDF rendering note:** Performance tier badges (Elite / Strong / Needs Attention) and email buttons are hidden during PDF capture. A `.pdf-export` CSS class is applied to the page body immediately before capture and removed immediately after, so the live dashboard is completely unaffected.

---

## 7. Configure Panel

The **Configure** button (gear icon ⚙) in the top-right header bar opens a settings panel below the filter bar. It has two sections.

### 7.1 Performance Tier Thresholds

Three configurable criteria for the Elite and Strong tier badges. All three must be met simultaneously for a tier to be assigned.

| Criterion | Default — Elite | Default — Strong |
|---|---|---|
| Avg days to close | ≤ 10 days | ≤ 30 days |
| Open cases | ≤ 15 | ≤ 30 |
| SLA compliance | ≥ 90% | ≥ 80% |

Click **Apply & refresh scorecards** to apply changes immediately. Click **Reset to defaults** to revert to the values above. **Needs Attention** is assigned automatically when an engineer does not qualify for either tier.

### 7.2 Email Settings

Stored in your browser's local storage — entered once and persisted across sessions.

- **Your name** — appears in the email sign-off as manager name
- **Your email** — added to the CC field of all scorecard emails
- **Engineer email addresses** — one field per engineer, used to pre-fill the To field

Click **Save email settings** to persist. Email addresses are never transmitted outside the browser.

---

## 8. Fiscal Year Reference

The dashboard uses the Velocity Solutions fiscal year: **March 1 through February 28/29**.

| Quarter | Months | FY2027 Dates |
|---|---|---|
| Q1 | March, April, May | March 1, 2026 – May 31, 2026 |
| Q2 | June, July, August | June 1, 2026 – August 31, 2026 |
| Q3 | September, October, November | September 1, 2026 – November 30, 2026 |
| Q4 | December, January, February | December 1, 2026 – February 28, 2027 |

FY2027 began March 1, 2026. The week filter shows only weeks from **March 2, 2026** onward (the first Monday of FY2027). Weeks are always anchored to Monday.

---

## 9. SLA Targets Reference

| Severity Label | Display Name | Resolution Target | Applies To |
|---|---|---|---|
| `1-Critical` | Critical | 24 hours (1 day) | Closed cases with days data |
| `2-Medium` | Medium | 3 calendar days | Closed cases with days data |
| `3-Low` | Low | 7 calendar days (1 week) | Closed cases with days data |
| `4-General` | General | 14 calendar days (2 weeks) | Closed cases with days data |

SLA compliance is computed only for closed cases that have a value in the `# of Days From Created to Resolved` column. Cases without this value are excluded from SLA calculations. Open cases are never included.

---

## 10. Metric Formula Reference

| Metric | Formula | Scope |
|---|---|---|
| Cases assigned | `COUNT(created in period, owner = engineer)` | Selected period |
| Cases closed | `COUNT(Resolved Date in period, owner = engineer)` | Selected period |
| Resolution rate | `resolved ÷ assigned × 100` | Selected period |
| Avg days to close | `AVG(# of Days From Created to Resolved)` | Closed in period (or all-time fallback) |
| Median days to close | `MEDIAN(# of Days From Created to Resolved)` | Closed in period (or all-time fallback) |
| Same-day closure % | `COUNT(days = 0) ÷ COUNT(closed) × 100` | Closed in period |
| Closed within 7 days % | `COUNT(days ≤ 7) ÷ COUNT(closed) × 100` | Closed in period |
| SLA compliance % | `COUNT(met SLA target) ÷ COUNT(eligible) × 100` | Closed in period |
| Total open | `COUNT(no Resolved Date)` | All time |
| >90 days open | `COUNT(open where today − Created > 90)` | All time open cases |
| Avg open age | `AVG(today − Created On) for open cases` | Current open cases |
| Longest open case | `MAX(today − Created On) for open cases` | Current open cases |
| No product | `COUNT(open where Support Product is blank)` | All time open cases |
| No FI | `COUNT(open where Customer Acct # = 999)` | All time open cases |

> **Avg days to close fallback:** When no cases were closed in the selected period for a given engineer, the avg and median days to close fall back to that engineer's all-time average across all closed cases with days data.

---

## 11. Technical Notes

### 11.1 Browser Compatibility

The dashboard is tested and supported in:

- Microsoft Edge *(recommended for corporate environments)*
- Google Chrome
- Mozilla Firefox *(AI narrative may not function if API access is restricted)*

> **Microsoft Edge — Tracking Prevention:** Edge's Tracking Prevention feature can block requests to Cloudflare CDN, which prevents the required libraries (SheetJS, Chart.js, html2canvas, jsPDF) from loading. The dashboard detects this and shows a message on the load screen if any library fails. To resolve, go to **Settings → Privacy, search, and services → Tracking Prevention** and set it to **Basic**, or add `cdnjs.cloudflare.com` to the exceptions list. Alternatively, host the dashboard on a web server or SharePoint rather than opening it as a local file — this avoids the tracking prevention trigger entirely.

> **Timezone note:** The week filter and date calculations use the browser's local timezone. For consistent results, all team members should use the same timezone setting. Week boundaries are always computed in local time, not UTC.

### 11.2 Libraries Used

| Library | Version | Purpose |
|---|---|---|
| SheetJS (xlsx) | 0.18.5 | Parses the Excel file in the browser |
| Chart.js | 4.4.1 | All charts and graphs |
| html2canvas | 1.4.1 | Captures DOM sections for PDF export |
| jsPDF | 2.5.1 | Generates the PDF file |
| Anthropic API | claude-sonnet-4-20250514 | AI narrative on Insights tab |

All libraries are loaded from the Cloudflare CDN (`cdnjs.cloudflare.com`). An internet connection is required on first load. Subsequent chart rendering and data processing are entirely local.

### 11.3 Hosting on SharePoint

The dashboard HTML file can be hosted in a SharePoint document library and shared with the team as a URL.

1. Upload `Velocity_Support_Dashboard_v2_2_0.html` to a SharePoint document library
2. Share the direct file URL with the team (they can bookmark it)
3. Each team member clicks **Load new data** and selects their local Excel export
4. To update the dashboard code, simply upload a new version of the HTML file

> **Local storage note:** Email configuration and tier thresholds are stored in each user's browser local storage. Each user who accesses the dashboard will need to configure their own email settings if they plan to send scorecard emails.

---

*Velocity Solutions · Support Operations · FY2027*
