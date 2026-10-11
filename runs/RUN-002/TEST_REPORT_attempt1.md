# TEST REPORT — RUN-002 — Attempt 1
# Screen: FieldMappingMaster + FieldMappingDetails
# Date: 2026-05-31
# Method: Static Analysis (no live server)

---

## Legend

| Symbol | Meaning |
|---|---|
| COVERED | At least one test assertion exists for this requirement |
| MISSING | No test assertion found for this requirement |
| CRITICAL | Blocking FRD requirement — causes OVERALL FAIL if MISSING |

---

## Area 1 — Upload Field Mapping

| ID | Requirement | Source | Status | Notes |
|---|---|---|---|---|
| TC-01 | File is required (null file rejected) | Unit: validateUpload — "returns invalid when file is null" | COVERED | errors['file'] contains 'choose' |
| TC-02 | .xlsx extension accepted | Unit: validateUpload — "accepts a valid .xlsx file" | COVERED | valid = true |
| TC-03 | .xls extension accepted | Unit: validateUpload — "accepts a valid .xls file" | COVERED | valid = true |
| TC-04 | Non-.xlsx/.xls extension rejected | Unit: validateUpload — "rejects non-xlsx/xls extension" | COVERED | errors['file'] contains '.xlsx' |
| TC-05 | Max 10 MB file size enforced | Unit: validateUpload — "rejects a file exceeding 10 MB" | COVERED | errors['file'] contains '10 MB' |
| TC-06 | Empty file (0 bytes) rejected | Unit: validateUpload — "rejects an empty file (0 bytes)" | COVERED | errors['file'] contains 'empty' |
| TC-07 | Excel only — no copy/paste allowed | Neither unit nor E2E | MISSING | No assertion that paste event is blocked or file input lacks paste handler |

## Area 2 — Upload Modal Buttons

| ID | Requirement | Source | Status | Notes |
|---|---|---|---|---|
| TC-08 | Upload modal opens on button click | E2E SPEC 4 | COVERED | dialog role + "Upload Field Mapping File" text visible |
| TC-09 | Modal contains file input with accept=".xlsx,.xls" | E2E SPEC 5 | COVERED | #fm-file-input accept attribute asserted |
| TC-10 | Cancel button closes the modal | E2E SPEC 6 | COVERED | dialog not visible after cancel click |
| TC-11 | Upload (submit) button present inside modal | Not asserted | MISSING | SPEC 4 checks dialog but never asserts modal's upload/submit button |

## Area 3 — Uploaded Mapping Files Grid

| ID | Requirement | Source | Status | Notes |
|---|---|---|---|---|
| TC-12 | Grid column "Upload ID" visible | E2E SPEC 7 | COVERED | .ag-header-cell-text contents asserted |
| TC-13 | Grid column "File Name" visible | E2E SPEC 7 | COVERED | |
| TC-14 | Grid column "Upload Type" visible | E2E SPEC 7 | COVERED | |
| TC-15 | Grid column "Total Records" visible | E2E SPEC 7 | COVERED | |
| TC-16 | Grid column "Uploaded By" visible | E2E SPEC 7 | COVERED | |
| TC-17 | Grid column "Upload Date" visible | E2E SPEC 7 | COVERED | |
| TC-18 | Grid column "Status" visible | E2E SPEC 7 | COVERED | |
| TC-19 | Grid supports sorting | Not tested | MISSING | No click-column-header sort assertion |
| TC-20 | Grid supports filtering | Not tested | MISSING | No filter input interaction |
| TC-21 | Grid supports pagination | Not tested | MISSING | No pagination control assertion |
| TC-22 | Grid supports search | Not tested | MISSING | No search toolbar interaction |

## Area 4 — Row Click Navigation

| ID | Requirement | Source | Status | Notes |
|---|---|---|---|---|
| TC-23 | Row click navigates to /field-mapping/details/:uploadId | E2E SPEC 8 | COVERED | URL regex /\/field-mapping\/details\/.+/ asserted; skip guard present when no rows available |

## Area 5 — Field Mapping Details — Header Info Strip

| ID | Requirement | Source | Status | Notes |
|---|---|---|---|---|
| TC-24 | Detail info strip element is rendered | E2E SPEC 9 | COVERED | .detail-info-strip visible |
| TC-25 | Strip field: Upload ID | Not individually asserted | MISSING | Only the container is checked, not individual field labels/values |
| TC-26 | Strip field: File Name | Not individually asserted | MISSING | |
| TC-27 | Strip field: Upload Type | Not individually asserted | MISSING | |
| TC-28 | Strip field: Upload Date | Not individually asserted | MISSING | |
| TC-29 | Strip field: Status | Not individually asserted | MISSING | |

## Area 6 — Field Mapping Details — KPI Cards

| ID | Requirement | Source | Status | Notes |
|---|---|---|---|---|
| TC-30 | KPI card "Total Records" visible | Not tested | MISSING | No assertion in unit or E2E |
| TC-31 | KPI card "Processed" visible | Not tested | MISSING | |
| TC-32 | KPI card "Failed" visible | Not tested | MISSING | |

## Area 7 — Uploaded Records Grid (Details)

| ID | Requirement | Source | Status | Notes |
|---|---|---|---|---|
| TC-33 | "Uploaded Records" section heading visible | E2E SPEC 10 | COVERED | getByText('Uploaded Records') asserted |
| TC-34 | Grid columns are dynamic (derived from file headers) | Not tested | MISSING | No assertion that column headers match uploaded file content |
| TC-35 | Grid is read-only (cells not editable) | Not tested | MISSING | No edit attempt or readonly attribute check |
| TC-36 | Export CSV button functional | Not tested | MISSING | |
| TC-37 | Export Excel button functional | Not tested | MISSING | |

## Area 8 — Mapping Configuration Section (Add, Edit, Save, Cancel)

| ID | Requirement | Source | Status | Notes |
|---|---|---|---|---|
| TC-38 | "Mapping Configuration" section heading visible | E2E SPEC 11 | COVERED | getByText('Mapping Configuration') asserted |
| TC-39 | "Add Mapping" / Add Row button visible | E2E SPEC 12 | COVERED | getByRole('button', {name: /add mapping/i}) asserted |
| TC-40 | "Save All" button visible | E2E SPEC 13 | COVERED | getByRole('button', {name: /save all/i}) asserted |
| TC-41 | Add Row inserts a new blank editable row | Not tested | MISSING | No click + new row count assertion |
| TC-42 | Edit button makes row inline-editable | Not tested | MISSING | |
| TC-43 | Save persists mapping via saveFieldMapping | Not tested | MISSING | |
| TC-44 | Cancel reverts unsaved changes | Not tested | MISSING | |

## Area 9 — Validation Rules

| ID | Requirement | Source | Status | Notes |
|---|---|---|---|---|
| TC-45 | Source Field required | Unit: validateMappingRow — "returns invalid when sourceField is missing" | COVERED | errors['sourceField'] defined |
| TC-46 | Target Field required | Unit: validateMappingRow — "returns invalid when targetField is missing" | COVERED | errors['targetField'] defined |
| TC-47 | Both fields missing simultaneously | Unit: validateMappingRow — "returns invalid when both fields are missing" | COVERED | |
| TC-48 | Valid row with no duplicate | Unit: validateMappingRow — "returns valid when both fields provided and no duplicate" | COVERED | |
| TC-49 | Duplicate sourceField rejected | Unit: validateMappingRow — "returns invalid when sourceField duplicates" | COVERED | errors['sourceField'] contains 'already mapped' |
| TC-50 | Same-row edit allowed (same mappingId) | Unit: validateMappingRow — "allows a row to match its own sourceField when editing" | COVERED | |
| TC-51 | Duplicate check is case-insensitive | Unit: validateMappingRow — "is case-insensitive when checking for duplicates" | COVERED | |

## Area 10 — Download Template

| ID | Requirement | Source | Status | Notes |
|---|---|---|---|---|
| TC-52 | Download Template button visible on master screen | Unit SECTION 3 + E2E SPEC 3 | COVERED | Both unit and E2E assert button renders |
| TC-53 | Download Template triggers file download (functional) | Not tested | MISSING | downloadTemplate service is mocked in unit; no actual download trigger asserted in E2E |

## Area 11 — App Shell (Sidebar + Topbar)

| ID | Requirement | Source | Status | Notes |
|---|---|---|---|---|
| TC-54 | Sidebar is present on Field Mapping Master screen | Not tested | MISSING | No assertion for sidebar element |
| TC-55 | Topbar is present on Field Mapping Master screen | Not tested | MISSING | |
| TC-56 | Sidebar is present on Field Mapping Details screen | Not tested | MISSING | |
| TC-57 | Topbar is present on Field Mapping Details screen | Not tested | MISSING | |

## Area 12 — No Breadcrumbs

| ID | Requirement | Source | Status | Notes |
|---|---|---|---|---|
| TC-58 | No breadcrumbs on Master screen (unit) | Unit SECTION 3 — "does not render breadcrumbs" | COVERED | queryByRole('navigation', {name:/breadcrumb/i}) not in document |
| TC-59 | No breadcrumbs on Master screen (E2E) | E2E — "No breadcrumbs on Field Mapping screen" | COVERED | [aria-label="breadcrumb"],.breadcrumb,.breadcrumbs count = 0 |
| TC-60 | No breadcrumbs on Details screen (E2E) | E2E — "No breadcrumbs on Field Mapping Details screen" | COVERED | count = 0 |

## Area 13 — Responsive Behavior

| ID | Requirement | Source | Status | Notes |
|---|---|---|---|---|
| TC-61 | Page is responsive (layout adapts to narrow viewport) | Not tested | MISSING | No viewport resize test in unit or E2E |

## Area 14 — Success / Error Handling

| ID | Requirement | Source | Status | Notes |
|---|---|---|---|---|
| TC-62 | Upload success toast/notification shown | Not tested | MISSING | No E2E assertion for success state after file upload |
| TC-63 | Upload failure message shown | Not tested | MISSING | |
| TC-64 | Fetch error sets error state in hook | Unit: useFieldMapping — "sets error when fetch fails" | COVERED | result.current.errors['grid'] defined |
| TC-65 | Save success notification shown | Not tested | MISSING | |
| TC-66 | Save failure notification shown | Not tested | MISSING | |
| TC-67 | Validation error inline message shown in UI | Not tested | MISSING | Unit only covers schema function, not UI display of error |

---

## Hook Tests (Additional Coverage)

| ID | Requirement | Source | Status | Notes |
|---|---|---|---|---|
| TC-H01 | Hook fetches uploaded files on mount | Unit: useFieldMapping — "fetches uploaded files on mount" | COVERED | getUploadedFiles called once; result has 1 row |
| TC-H02 | Hook re-fetches after upload success | Unit: useFieldMapping — "re-fetches when handleUploadSuccess is called" | COVERED | getUploadedFiles called twice |
| TC-H03 | Hook initial state is empty before fetch completes | Unit: useFieldMapping — "initialises with empty uploadedFiles before fetch completes" | COVERED | length = 0 |

---

## Component Render Tests (Additional Coverage)

| ID | Requirement | Source | Status | Notes |
|---|---|---|---|---|
| TC-C01 | Page title "Field Mapping" renders | Unit SECTION 3 | COVERED | |
| TC-C02 | Upload File button renders | Unit SECTION 3 | COVERED | |
| TC-C03 | Download Template button renders | Unit SECTION 3 | COVERED | |
| TC-C04 | Page subtitle renders | Unit SECTION 3 | COVERED | "Upload and configure field mappings" |

---

## Critical Classification

The following MISSING tests are classified as CRITICAL (directly blocking FRD acceptance criteria):

| TC ID | Requirement | Criticality Rationale |
|---|---|---|
| TC-30 | KPI card Total Records | FRD Section 5 explicitly names KPI cards |
| TC-31 | KPI card Processed | FRD Section 5 explicitly names KPI cards |
| TC-32 | KPI card Failed | FRD Section 5 explicitly names KPI cards |
| TC-41 | Add Row inserts editable row | FRD Section 6 — Add is a named grid action |
| TC-42 | Edit makes row editable | FRD Section 6 — Edit is a named grid action |
| TC-43 | Save persists mapping | FRD Section 6 — Save is a named grid action |
| TC-44 | Cancel reverts changes | FRD Section 6 — Cancel is a named grid action |

Non-critical MISSING (nice-to-have / lower risk):
TC-07 (no-paste), TC-11 (modal upload button), TC-19–TC-22 (sort/filter/page/search), TC-25–TC-29 (strip field labels), TC-34–TC-37 (dynamic cols, read-only, exports), TC-53 (download functional), TC-54–TC-57 (app shell), TC-61 (responsive), TC-62–TC-63, TC-65–TC-67 (toast/error UI).

---

```
SUMMARY
  Total Test Cases : 67
  Covered          : 34
  Missing          : 33
  Critical Failures:  7
OVERALL : FAIL
```
