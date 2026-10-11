# PLAN v1.0

| Field | Value |
|---|---|
| Feature | ClaimIntake |
| Screen | claimIntakeMaster |
| FRD | docs/ClaimIntake/claimIntakeMaster-frd.md |
| TechSpec | docs/ClaimIntake/claimIntakeMaster-techspec.md |
| Plan Date | 2026-05-29 |

---

## TASK-00

| Field | Value |
|---|---|
| Title | Project Scaffolding + custom.css |
| Scope | frontend, backend |
| FR Ref | — |
| Description | Scaffold the React+Vite frontend (package.json, vite.config.ts, main.tsx, tsconfig.json, index.html with skin/js/main.js script tag, import of skin CSS in main.tsx, fs.allow for skin/ in vite.config). Scaffold Node.js+GraphQL backend (package.json, server.ts, Apollo Server config). Create sourceCode/skin/css/custom.css with all layout classes missing from main.css: .page, .btn, .btn--ghost, .btn--sm, .page-header-card, .content-topbar, .ag-grid-in-card, .sidebar-nav__link, .sidebar-nav__link--parent, .sidebar-nav__child |
| Input | AGENTS.md, skin/css/main.css |
| Output | sourceCode/apps/frontEnd/config/package.json, sourceCode/apps/frontEnd/config/vite.config.ts, sourceCode/apps/frontEnd/config/tsconfig.json, sourceCode/apps/frontEnd/index.html, sourceCode/apps/frontEnd/main.tsx, sourceCode/apps/api/package.json, sourceCode/apps/api/server.ts, sourceCode/skin/css/custom.css |
| Depends On | none |
| Stack | React 18, Vite, TypeScript, Node.js, Apollo Server, GraphQL |
| Skin Ref | n/a |

---

## TASK-01

| Field | Value |
|---|---|
| Title | GraphQL Entity Helper — Claim Intake |
| Scope | backend |
| FR Ref | All upload/query operations |
| Description | GraphQL schema definition (uploadClaim mutation, uploadedClaims query, uploadedClaimDetails query). MongoDB entity helper with all CRUD for claim_intake and claim_intake_audit collections. No $skip — use in-memory pagination. |
| Input | TechSpec §3 (GraphQL APIs), §5 (MongoDB Collections) |
| Output | sourceCode/apps/api/graphQL/claimIntakeEntity.ts |
| Depends On | TASK-00 |
| Stack | MongoDB SDK, GraphQL, TypeScript |
| Skin Ref | n/a |

---

## TASK-02

| Field | Value |
|---|---|
| Title | Backend Services |
| Scope | backend |
| FR Ref | FRD §7 Processing Logic |
| Description | claimIntakeService.ts — orchestrate upload, validate, store, retrieve grid. excelParserService.ts — parse xlsx/xls, validate required columns, extract claim data. jsonValidatorService.ts — validate JSON structure and required fields. xmlValidatorService.ts — validate XML via xml2js, extract claim data. claimDetailsService.ts — retrieve full claim detail by uploadId. errorLogService.ts — shared error logging to Triage_errorlog MongoDB collection. All errors go through errorLogService. |
| Input | TASK-01 (entity helper), TechSpec §4 |
| Output | sourceCode/apps/api/services/claimIntakeService.ts, sourceCode/apps/api/services/excelParserService.ts, sourceCode/apps/api/services/jsonValidatorService.ts, sourceCode/apps/api/services/xmlValidatorService.ts, sourceCode/apps/api/services/claimDetailsService.ts, sourceCode/apps/api/services/errorLogService.ts |
| Depends On | TASK-01 |
| Stack | Node.js, xlsx, xml2js, joi, uuid, MongoDB |
| Skin Ref | n/a |

---

## TASK-03

| Field | Value |
|---|---|
| Title | Backend Controller + Routes |
| Scope | backend |
| FR Ref | FRD §7 Processing Logic |
| Description | claimIntakeMasterController.ts — request/response only, delegates to services. claimIntakeMasterRoutes.ts — Express routes. GraphQL resolvers wired to entity helper. All errors stored via errorLogService → Triage_errorlog. .env vars used — no hardcoded credentials. |
| Input | TASK-02 (services) |
| Output | sourceCode/apps/api/controllers/claimIntakeMasterController.ts, sourceCode/apps/api/routes/claimIntakeMasterRoutes.ts |
| Depends On | TASK-02 |
| Stack | Node.js, Express, GraphQL |
| Skin Ref | n/a |

---

## TASK-04

| Field | Value |
|---|---|
| Title | Frontend Model + Types |
| Scope | frontend |
| FR Ref | TechSpec §3, §5 |
| Description | TypeScript types: UploadType enum (EXCEL, JSON, XML), ClaimIntakeRecord, UploadedClaimsGridRow, UploadedClaimDetails, UploadClaimInput, UploadClaimResponse. |
| Input | TechSpec §3, §5 |
| Output | sourceCode/apps/frontEnd/models/claimIntakeMaster/claimIntakeModel.ts, sourceCode/apps/frontEnd/pages/ClaimIntake/claimIntakeMaster/types.ts |
| Depends On | none (parallel with TASK-01..03) |
| Stack | TypeScript |
| Skin Ref | n/a |

---

## TASK-05

| Field | Value |
|---|---|
| Title | Frontend Service — GraphQL API Calls |
| Scope | frontend |
| FR Ref | TechSpec §3 |
| Description | claimIntakeService.ts (frontend): uploadClaim(input) → uploadClaim mutation, getUploadedClaims() → uploadedClaims query, getUploadedClaimDetails(uploadId) → uploadedClaimDetails query. logError() on all errors. |
| Input | TASK-04 (types), TechSpec §3 |
| Output | sourceCode/apps/frontEnd/services/claimIntakeMaster/claimIntakeService.ts |
| Depends On | TASK-04 |
| Stack | Apollo Client / fetch, TypeScript |
| Skin Ref | n/a |

---

## TASK-06

| Field | Value |
|---|---|
| Title | Frontend Validation Schema |
| Scope | frontend |
| FR Ref | FRD §6 Validation Rules |
| Description | Validation schema: uploadType required; excelFile required when uploadType=EXCEL, must be .xlsx/.xls, max 10MB; jsonPayload required when uploadType=JSON, must be valid JSON, not empty; xmlData required when uploadType=XML, must be valid XML, not empty. |
| Input | FRD §6, TechSpec §6 |
| Output | sourceCode/apps/frontEnd/validations/claimIntakeMaster/claimIntakeSchema.ts |
| Depends On | TASK-04 |
| Stack | TypeScript |
| Skin Ref | n/a |

---

## TASK-07

| Field | Value |
|---|---|
| Title | Frontend Hooks |
| Scope | frontend |
| FR Ref | All FRD upload and grid requirements |
| Description | useClaimIntake.ts: state (uploadType, file, jsonPayload, xmlData, isLoading, error, success, uploadedClaims, selectedRow, claimDetails). Handlers: handleUploadTypeChange, handleFileChange, handleJsonChange, handleXmlChange, handleUpload (validate → service → refresh grid → toast), handleRowSelect (fetch details). logError() on all async errors. |
| Input | TASK-05 (service), TASK-06 (validation) |
| Output | sourceCode/apps/frontEnd/hooks/claimIntakeMaster/useClaimIntake.ts |
| Depends On | TASK-05, TASK-06 |
| Stack | React hooks, TypeScript |
| Skin Ref | n/a |

---

## TASK-08

| Field | Value |
|---|---|
| Title | App Shell Components + Claim Intake Screen |
| Scope | frontend |
| FR Ref | All FRD screen requirements, TechSpec §2 Screen Layout |
| Description | Part A — App Shell (shared, built once): AppShell.tsx wraps sidebar + main-content + page. Sidebar.tsx fetches dynamic menu from MongoDB, builds parent-child tree via buildTree(flat), renders parent items with sidebar-nav__link sidebar-nav__link--parent + chevron SVG, renders child items with sidebar-nav__child, resolveIcon() returns null for unknown icons, sidebar brand uses sidebar-logo. Topbar.tsx uses app-header, hamburger btn btn--ghost btn--sm, header-user, btn-logout. ErrorBoundary.tsx global React Error Boundary. Part B — Claim Intake Screen: wrapped in AppShell. Header card with page-header-card (title: "Claim Intake", subtitle: "Upload and review claim intake records"). Upload section with radio buttons for Upload Type. Conditional ExcelUploader / JsonEditor / XmlEditor. Upload Claim button. Uploaded Claims AG Grid in ag-theme-alpine ag-grid-in-card (columns: Upload ID, File Name, Upload Type, Claim Number, Uploaded By, Upload Date, Status; single-row selection). Uploaded Claim Details section (reads from selectedRow). Status badges: badge-green / badge-yellow / badge-red. styles.ts references skin CSS class names only — no inline styles, no page-specific CSS. |
| Input | TASK-07, skin/index.html, skin/css/main.css, skin/css/custom.css |
| Output | sourceCode/apps/frontEnd/components/layout/AppShell.tsx, sourceCode/apps/frontEnd/components/layout/Sidebar.tsx, sourceCode/apps/frontEnd/components/layout/Topbar.tsx, sourceCode/apps/frontEnd/components/layout/ErrorBoundary.tsx, sourceCode/apps/frontEnd/pages/ClaimIntake/claimIntakeMaster/index.tsx, sourceCode/apps/frontEnd/pages/ClaimIntake/claimIntakeMaster/styles.ts |
| Depends On | TASK-07 |
| Stack | React 18, TypeScript, AG Grid, Triage skin classes |
| Skin Ref | .page-header-card, .content-topbar, .ag-theme-alpine, .ag-grid-in-card, .sidebar-logo, .sidebar-nav__link, .sidebar-nav__link--parent, .sidebar-nav__child, .main-content, .page, .btn.btn--ghost.btn--sm, .badge-green, .badge-yellow, .badge-red |

---

## TASK-09

| Field | Value |
|---|---|
| Title | Unit Tests |
| Scope | test |
| FR Ref | All FRD requirements |
| Description | Unit tests: claimIntakeSchema validation (all rules — required, conditional, format, size limit), useClaimIntake hook (all state transitions, error paths), claimIntakeService frontend mock tests, backend excelParserService / jsonValidatorService / xmlValidatorService. |
| Input | TASK-06, TASK-07, TASK-05 |
| Output | sourceCode/apps/frontEnd/pages/ClaimIntake/claimIntakeMaster/unitTest/claimIntakeMaster.test.tsx |
| Depends On | TASK-07 |
| Stack | Vitest, React Testing Library, TypeScript |
| Skin Ref | n/a |

---

## TASK-10

| Field | Value |
|---|---|
| Title | E2E Spec |
| Scope | test |
| FR Ref | All FRD acceptance criteria |
| Description | Playwright E2E: page loads with correct title/subtitle; Upload Type radio switches inputs; Excel file upload (valid .xlsx, invalid format rejection); JSON upload (valid, invalid JSON error); XML upload (valid, invalid XML error); empty upload validation error; grid row selection opens claim details; status badge rendering; dynamic menu loads from MongoDB. |
| Input | TASK-08 (running screen) |
| Output | sourceCode/apps/frontEnd/pages/ClaimIntake/claimIntakeMaster/e2e/claimIntakeMaster.spec.ts |
| Depends On | TASK-08 |
| Stack | Playwright, TypeScript |
| Skin Ref | n/a |

---

## AMBIGUITIES

| # | Issue | Resolution |
|---|---|---|
| 1 | TechSpec §2 lists "Next.js" as frontend | RESOLVED — AGENTS.md is source of truth; React + Vite is used |
| 2 | skin/css/custom.css referenced in DEVELOPER SKILL but does not exist | RESOLVED — TASK-00 creates it as a new file (not a modification of existing files) |
| 3 | apps/ directory is empty — no scaffold exists | RESOLVED — TASK-00 scaffolds both frontend and backend from scratch |

---

## READY FOR DEVELOPER: YES
