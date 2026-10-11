# PLAN v1.0

| Field | Value |
|---|---|
| Feature | Dashboard |
| Screen | DashboardMaster (Executive Overview) |
| FRD | docs/Dashboard/Dashboard-frd.md |
| TechSpec | docs/Dashboard/Dashboard-techspec.md |
| Plan Date | 2026-06-11 |

---

## TASK-01

| Field | Value |
|---|---|
| Title | Frontend Model + Types |
| Scope | frontend |
| FR Ref | FRD §4–§13 |
| Description | TypeScript interfaces: KpiCardData, TrendPoint, SavingsByServiceRow, VendorRow, SlaComplianceRow, AlertItem (severity, status), AiInsight, DateRangePresetValue + option list, FilterState (service/vendor/region/claimType/severity arrays), VendorSortField. Constants: SERVICE_OPTIONS (15 services), VENDOR_OPTIONS, REGION_OPTIONS, CLAIM_TYPE_OPTIONS, SEVERITY_OPTIONS, DATE_RANGE_OPTIONS. |
| Input | FRD §4–§13, TechSpec §3 |
| Output | sourceCode/apps/frontEnd/src/models/dashboard/executiveOverviewModel.ts, sourceCode/apps/frontEnd/src/pages/Dashboard/DashboardMaster/types.ts |
| Depends On | none |
| Stack | TypeScript |
| Skin Ref | n/a |

---

## TASK-02

| Field | Value |
|---|---|
| Title | Mock Data Service |
| Scope | frontend |
| FR Ref | FRD §4–§10, §12 |
| Description | executiveOverviewService.ts — mock generator functions matching TechSpec REST response shapes: getKpis(range), getLaeRatioTrend(range), getSavingsByService(range, filters, limit), getSubrogationTrend(range), getVendors(filters, sortBy, order, limit), getSlaCompliance(filters, limit), getAlerts(filters, limit), getAiInsight(). Date range scales values (pattern from existing PERIOD_SCALE). Filters narrow result sets (service/vendor/severity). All return Promise (simulated async) for forward compatibility with future API wiring. |
| Input | TASK-01, FRD sample tables (§4–§10) |
| Output | sourceCode/apps/frontEnd/src/services/dashboard/executiveOverviewService.ts |
| Depends On | TASK-01 |
| Stack | TypeScript |
| Skin Ref | n/a |

---

## TASK-03

| Field | Value |
|---|---|
| Title | useExecutiveOverview Hook |
| Scope | frontend |
| FR Ref | FRD §13 |
| Description | useExecutiveOverview.ts — state: dateRange, filters, vendorSort, kpiVisibility (per-card toggle), section data + loading flags. Effects re-fetch all sections on dateRange/filters/vendorSort change (TechSpec processing workflow §12). Handlers: setDateRange, applyFilters, clearFilters, toggleKpiVisibility, setVendorSort. Computes activeFilterCount for Filters button badge. logError() on fetch failures. |
| Input | TASK-02 |
| Output | sourceCode/apps/frontEnd/src/hooks/dashboard/useExecutiveOverview.ts |
| Depends On | TASK-02 |
| Stack | React hooks, TypeScript |
| Skin Ref | n/a |

---

## TASK-04

| Field | Value |
|---|---|
| Title | Shared Dashboard Components |
| Scope | frontend |
| FR Ref | FRD §4–§10, §12, §13 |
| Description | components/dashboard/: KpiCard.tsx (icon, label, value, trend arrow %, visibility checkbox — §4); AreaTrendChart.tsx (@nivo/line, shaded area, hero value, point labels — used for §5 LAE Ratio and §7 Subrogation Recovery); HorizontalBarChart.tsx (custom inline SVG, optional per-bar color thresholds for §9); VendorPerformanceTable.tsx (§8 — rank, vendor, score, SLA%, cycle time, trend, sort dropdown); AlertsPanel.tsx (§10 — severity icon/color, title, description, count badge, ordered Critical→High→Warning→Info); AiInsightsPanel.tsx (§12 — insight text + "View Insights →"); DateRangePicker.tsx (§13 — preset dropdown incl. default "Last 20 Days"); FiltersPanel.tsx (§13 — multi-select Service/Vendor/Region/Claim Type/Severity, badge count, Clear All). |
| Input | TASK-01, TASK-03, sourceCode/skin/css/main.css, custom.css |
| Output | sourceCode/apps/frontEnd/src/components/dashboard/KpiCard.tsx, AreaTrendChart.tsx, HorizontalBarChart.tsx, VendorPerformanceTable.tsx, AlertsPanel.tsx, AiInsightsPanel.tsx, DateRangePicker.tsx, FiltersPanel.tsx |
| Depends On | TASK-01 |
| Stack | React 18, TypeScript, @nivo/line, Triage skin classes |
| Skin Ref | .dash-metric-card, .dash-chart-widget, .dash-activity-table, .badge-*, .btn--ghost |

---

## TASK-05

| Field | Value |
|---|---|
| Title | custom.css Additions |
| Scope | frontend |
| FR Ref | FRD §4–§13 |
| Description | Add new classes to sourceCode/skin/css/custom.css (no modification of main.css): `.dash-metric-card__visibility` (checkbox), `.chart-hero-value` (LAE/Subrogation hero number), `.dash-info-icon` + `.dash-info-tooltip` (§9 info icon hover), `.sla-bar--green/--amber/--red`, `.alert-row`, `.alert-severity-icon--critical/--high/--warning/--info`, `.alert-count-badge`, `.ai-insight-panel` (sidebar-pinned card), `.date-range-picker` + `.date-range-picker__menu`, `.filters-panel` + `.filters-panel__group` + `.filters-panel__badge`, `.view-all-link` (static, non-navigating). |
| Input | FRD §4–§13, existing custom.css dash-* classes |
| Output | sourceCode/skin/css/custom.css (append only) |
| Depends On | none (parallel) |
| Stack | CSS |
| Skin Ref | existing .dash-*, .ops-kpi-*, .badge-*, .kpi-filter-* sections |

---

## TASK-06

| Field | Value |
|---|---|
| Title | AI Insights Panel — Sidebar |
| Scope | frontend |
| FR Ref | FRD §3, §12 |
| Description | Add AiInsightsPanel (TASK-04) pinned to bottom of Sidebar.tsx, fetching getAiInsight() from executiveOverviewService. Existing dynamic MongoDB-driven nav items (FRD §3.1 sidebar list) are NOT changed in this RUN — only the bottom-pinned insight panel is added, rendered identically whether sidebar is expanded or collapsed (icon-only) per existing pattern. |
| Input | TASK-02, TASK-04 |
| Output | sourceCode/apps/frontEnd/src/components/layout/Sidebar.tsx (modified) |
| Depends On | TASK-02, TASK-04 |
| Stack | React 18, TypeScript |
| Skin Ref | .sidebar-logo, new .ai-insight-panel |

---

## TASK-07

| Field | Value |
|---|---|
| Title | Executive Overview Screen |
| Scope | frontend |
| FR Ref | FRD §3–§10, §13 |
| Description | Replace pages/Dashboard/DashboardMaster/index.tsx + styles.ts. Header card: title "Executive Overview", subtitle, DateRangePicker, Filters button (opens FiltersPanel, badge = activeFilterCount), active-filter summary + Clear All. Sections in order: KPI Summary Cards (§4, KpiCard row), LAE Ratio Trend (§5, AreaTrendChart), Top 5 Savings by Service (§6, HorizontalBarChart + view-all-link), Subrogation Recovery Trend (§7, AreaTrendChart green), Vendor Performance Overview (§8, VendorPerformanceTable + sort dropdown + view-all-link), SLA Compliance by Service (§9, HorizontalBarChart w/ thresholds + info tooltip + view-all-link), Top Alerts & Exceptions (§10, AlertsPanel + view-all-link). Existing 3-tab structure (Claim KPI Tracking / Vendor KPI Tracking / Analytics) removed — single Executive Overview page per FRD. Wrapped in AppShell. Wired to useExecutiveOverview (TASK-03). Skeleton/loading state on initial fetch (TechSpec §12 step 4). logError() on any handler error. |
| Input | TASK-01..06 |
| Output | sourceCode/apps/frontEnd/src/pages/Dashboard/DashboardMaster/index.tsx, sourceCode/apps/frontEnd/src/pages/Dashboard/DashboardMaster/styles.ts |
| Depends On | TASK-01, TASK-02, TASK-03, TASK-04, TASK-05 |
| Stack | React 18, TypeScript, Triage skin classes |
| Skin Ref | .dash-page, .dash-header-card, .dash-section, .dash-metric-row, .dash-chart-row, .dash-chart-widget, .dash-activity-widget |

---

## TASK-08

| Field | Value |
|---|---|
| Title | Unit Tests |
| Scope | test |
| FR Ref | All FRD requirements |
| Description | DashboardMaster.test.tsx — useExecutiveOverview hook (date range change re-fetches, filter apply/clear, activeFilterCount, KPI visibility toggle, vendor sort); executiveOverviewService mock generators (shape, date-range scaling, filter narrowing); component render smoke tests (KPI cards render 5, AlertsPanel orders by severity, VendorPerformanceTable shows top 5). |
| Input | TASK-01, 02, 03, 04 |
| Output | sourceCode/apps/frontEnd/src/pages/Dashboard/DashboardMaster/unitTest/DashboardMaster.test.tsx |
| Depends On | TASK-07 |
| Stack | Vitest, React Testing Library, TypeScript |
| Skin Ref | n/a |

---

## TASK-09

| Field | Value |
|---|---|
| Title | E2E Spec |
| Scope | test |
| FR Ref | All FRD acceptance criteria (Phase 1 scope) |
| Description | Playwright E2E: page loads with title "Executive Overview" and subtitle; 5 KPI cards render with trend indicators; KPI card visibility checkbox hides/shows a card; LAE Ratio and Subrogation Recovery charts render; Top 5 Savings and SLA Compliance bar charts render top 5 rows; Vendor Performance table shows top 5 sorted by score, sort dropdown re-sorts; Top Alerts shows 4 alerts ordered by severity; Date Range Picker changes refresh KPI values; Filters panel opens, applies a Service filter, shows badge count, Clear All resets. |
| Input | TASK-07 |
| Output | sourceCode/apps/frontEnd/src/pages/Dashboard/DashboardMaster/e2e/DashboardMaster.spec.ts |
| Depends On | TASK-07 |
| Stack | Playwright, TypeScript |
| Skin Ref | n/a |

---

## AMBIGUITIES

| # | Issue | Resolution |
|---|---|---|
| 1 | TechSpec stack = Next.js/Fastify/REST+WebSocket/Postgres+ClickHouse+Redis | RESOLVED — AGENTS.md overrides: React+Vite, no backend this RUN. Mock service shaped to match TechSpec REST response fields for forward compatibility. |
| 2 | FRD §3 defines an 11-item sidebar nav list (Overview, Services Performance, Vendor Scorecards, ...) different from the app's existing MongoDB-driven menu | RESOLVED — out of scope (would be an app-wide nav restructure with no corresponding screens). Existing menu unchanged. |
| 3 | FRD §3/§12 — AI Insights panel pinned to sidebar bottom | RESOLVED — added to shared Sidebar.tsx (TASK-06), additive only, does not alter nav items. |
| 4 | FRD §3 — notification bell w/ unread badge, avatar/profile dropdown, Export Report button (top header) | RESOLVED — out of scope for Phase 1 (Topbar.tsx is a shared global component; Export Report generation is explicitly deferred per §14). Not added this RUN. |
| 5 | FRD §3 default date range = "rolling 20-day period" but §13 preset list has no 20-day option | RESOLVED — add "Last 20 Days" as an additional preset, selected by default, alongside the §13 preset list. |
| 6 | "View All →" links throughout (§6,8,9,10) — target pages deferred | RESOLVED — rendered as static `.view-all-link` elements (visual only, non-navigating) for this RUN. |
| 7 | Bar chart components for §6 and §9 — TechSpec implies a charting lib but @nivo/bar is not an installed dependency | RESOLVED — custom inline SVG HorizontalBarChart (TASK-04), consistent with the existing SimpleBars precedent already approved in DashboardMaster. @nivo/line (already installed) used for the two area trend charts. |
| 8 | Filters panel (§13) — Service/Vendor/Region/Claim Type/Severity, with mock data only | RESOLVED — Service filter narrows Savings-by-Service & SLA Compliance rows; Vendor filter narrows the Vendor table; Severity filter narrows Alerts. Region/Claim Type are retained in filter state and shown in the active-filter summary for forward compatibility but do not alter Phase-1 mock numbers. |
| 9 | KPI card visibility checkbox (§4) | RESOLVED — local UI state in useExecutiveOverview; hidden cards are removed from the row (grid reflows). |

---

## READY FOR DEVELOPER: YES
