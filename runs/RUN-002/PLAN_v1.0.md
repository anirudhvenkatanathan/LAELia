# PLAN v1.0

| Field | Value |
|---|---|
| Feature | FieldMapping |
| Screen | FieldMappingMaster + FieldMappingDetails |
| FRD | docs/FieldMapping/FieldMappingMaster-frd.md |
| TechSpec | docs/FieldMapping/FieldMappingMaster-techspec.md |
| Plan Date | 2026-05-31 |

---

## TASK-00 — Menu Seed

| Field | Value |
|---|---|
| Title | Add Field Mapping to sidebar menu |
| Scope | backend |
| Description | Update sourceCode/apps/api/scripts/seedMenu.ts to add a "Field Mapping" menu item under "Main" group (parentId: menu-group-main, path: /field-mapping, icon: map, sortOrder: 3). Run the seed script. |
| Output | MongoDB triage_menu updated |
| Depends On | none |

---

## TASK-01 — GraphQL Entity Helper

| Field | Value |
|---|---|
| Title | GraphQL Entity — Field Mapping |
| Scope | backend |
| Description | Mongoose schemas + GraphQL typeDefs + resolvers for all 5 collections: field_mapping_upload, field_mapping_records, field_mapping_configuration, template_master, field_mapping_audit. Mutations: uploadFieldMappingFile(excelBase64, fileName, uploadedBy), saveFieldMapping(uploadId, mappings), downloadTemplate(uploadId). Queries: getUploadedFiles, getFieldMappingDetails(uploadId). No $skip. |
| Output | sourceCode/apps/api/graphQL/fieldMappingEntity.ts |
| Depends On | none |

---

## TASK-02 — Backend Services

| Field | Value |
|---|---|
| Title | Backend Services — Field Mapping |
| Scope | backend |
| Description | fieldMappingService.ts: orchestrate upload (parse Excel, extract headers, save upload record + records), retrieve mapping details, save mappings, audit logging. excelParserService reuse (already exists — import parseExcelFile). templateGeneratorService.ts: generate Excel template from stored headers using xlsx package, return base64. All errors via errorLogService. |
| Output | sourceCode/apps/api/services/fieldMappingService.ts, sourceCode/apps/api/services/templateGeneratorService.ts |
| Depends On | TASK-01 |

---

## TASK-03 — Backend Controller + Routes

| Field | Value |
|---|---|
| Title | Controller + Routes — Field Mapping |
| Scope | backend |
| Description | fieldMappingMasterController.ts — request/response only. fieldMappingMasterRoutes.ts — POST /upload, GET /files, GET /details/:uploadId, POST /save-mapping, GET /download-template. All errors via errorLogService. |
| Output | sourceCode/apps/api/controllers/fieldMappingMasterController.ts, sourceCode/apps/api/routes/fieldMappingMasterRoutes.ts |
| Depends On | TASK-02 |

---

## TASK-04 — Frontend Model + Types

| Field | Value |
|---|---|
| Title | Frontend Model + Types |
| Scope | frontend |
| Description | UploadedFileRow (grid row), FieldMappingDetails (header + records + mappings), FieldMappingRecord (individual Excel row, dynamic), MappingConfiguration (sourceField, targetField, dataType, isMandatory, mappingId), UploadFieldMappingInput, UploadFieldMappingResponse. |
| Output | sourceCode/apps/frontEnd/src/models/FieldMappingMaster/fieldMappingModel.ts, sourceCode/apps/frontEnd/src/pages/FieldMapping/FieldMappingMaster/types.ts |
| Depends On | none |

---

## TASK-05 — Frontend Service

| Field | Value |
|---|---|
| Title | Frontend Service — GraphQL Calls |
| Scope | frontend |
| Description | uploadFieldMappingFile(input), getUploadedFiles(), getFieldMappingDetails(uploadId), saveFieldMapping(uploadId, mappings), downloadTemplate(uploadId). Apollo Client. logError() in all catch. |
| Output | sourceCode/apps/frontEnd/src/services/FieldMappingMaster/fieldMappingService.ts |
| Depends On | TASK-04 |

---

## TASK-06 — Frontend Validation Schema

| Field | Value |
|---|---|
| Title | Validation Schema |
| Scope | frontend |
| Description | validateFieldMappingUpload(file): file required, .xlsx/.xls only, max 10MB, not empty. validateMappingRow(row): sourceField required, targetField required, no duplicate sourceField in existing mappings. |
| Output | sourceCode/apps/frontEnd/src/validations/FieldMappingMaster/fieldMappingSchema.ts |
| Depends On | TASK-04 |

---

## TASK-07 — Frontend Hooks

| Field | Value |
|---|---|
| Title | Frontend Hooks |
| Scope | frontend |
| Description | useFieldMapping.ts: state (uploadedFiles, isLoading, errors). Handlers: fetchUploadedFiles (auto on mount), handleUploadSuccess (refreshes grid). useFieldMappingDetails.ts: state (detail, mappings, editingRow, isLoading, errors). Handlers: loadDetails(uploadId), handleAddRow, handleEditRow, handleSaveRow, handleCancelEdit, handleSaveAll, handleDeleteRow. logError() in all async handlers. |
| Output | sourceCode/apps/frontEnd/src/hooks/FieldMappingMaster/useFieldMapping.ts, sourceCode/apps/frontEnd/src/hooks/FieldMappingMaster/useFieldMappingDetails.ts |
| Depends On | TASK-05, TASK-06 |

---

## TASK-08 — Screens

| Field | Value |
|---|---|
| Title | FieldMappingMaster + FieldMappingDetails Screens |
| Scope | frontend |
| Description | FieldMappingMaster (index.tsx): Same pattern as Claim Intake — page-card, page-header--with-action, "Upload File" button (btn--primary with upload icon) top-right, "Download Template" button (btn--ghost) beside it. AG Grid Uploaded Files (same theme: ag-theme-alpine ag-grid-in-card). Grid toolbar with search + refresh + record count. Row click navigates to /field-mapping/details/:uploadId. Upload modal (UploadFieldMappingModal.tsx) — Excel only, same design as UploadClaimModal. FieldMappingDetails (index.tsx): page-header--with-action with "Back to Field Mapping" btn--ghost button top-right. Info strip: Upload ID | File Name | Upload Type | Upload Date | Status. KPI cards: Total Records | Processed | Failed. Section 1 — Uploaded Records Grid (read-only, dynamic columns from file headers, ag-theme-alpine ag-grid-in-card, search + export CSV + export Excel). Section 2 — Mapping Configuration Grid (editable inline — Source Field, Target Field, Data Type, Mandatory, Action column with Edit/Delete buttons). Toolbar: Add Row button. On Add: inserts blank editable row. On Edit: row becomes editable inline. On Save: persists via saveFieldMapping mutation. On Cancel: reverts row. No breadcrumbs anywhere. All skin CSS classes only. |
| Output | sourceCode/apps/frontEnd/src/pages/FieldMapping/FieldMappingMaster/index.tsx, sourceCode/apps/frontEnd/src/pages/FieldMapping/FieldMappingMaster/styles.ts, sourceCode/apps/frontEnd/src/pages/FieldMapping/FieldMappingDetails/index.tsx, sourceCode/apps/frontEnd/src/components/FieldMappingMaster/UploadFieldMappingModal.tsx |
| Depends On | TASK-07 |
| Skin Ref | .page-card, .page-header, .page-header--with-action, .page-title, .page-subtitle, .section-block, .section-title, .btn.btn--primary, .btn.btn--ghost, .ag-theme-alpine, .ag-grid-in-card, .grid-toolbar, .grid-search, .grid-row-count, .kpi-section, .kpi-grid, .kpi-card, .detail-info-strip, .detail-info-field, .badge-green, .badge-yellow, .badge-red |

---

## TASK-09 — Unit Tests

| Field | Value |
|---|---|
| Title | Unit Tests |
| Scope | test |
| Description | Validation schema tests (file required, extension check, size limit). useFieldMapping hook tests (fetch on mount, upload success refreshes grid). MappingConfiguration row validation (sourceField required, targetField required, duplicate check). FieldMappingMaster component render tests (title, grid, buttons). |
| Output | sourceCode/apps/frontEnd/src/pages/FieldMapping/FieldMappingMaster/unitTest/FieldMappingMaster.test.tsx |
| Depends On | TASK-07 |

---

## TASK-10 — E2E Spec

| Field | Value |
|---|---|
| Title | E2E Spec |
| Scope | test |
| Description | Page loads with title "Field Mapping". Upload button visible. Download Template button visible. Upload modal opens on button click. Excel file upload. Grid shows uploaded file. Row click navigates to details. Details screen shows header strip + KPI cards + two grids. Add mapping row. Edit mapping row. Save mapping. Cancel edit. No breadcrumbs. |
| Output | sourceCode/apps/frontEnd/src/pages/FieldMapping/FieldMappingMaster/e2e/FieldMappingMaster.spec.ts |
| Depends On | TASK-08 |

---

## AMBIGUITIES

| # | Issue | Resolution |
|---|---|---|
| 1 | TechSpec says "Next.js" | AGENTS.md is source of truth: React + Vite |
| 2 | TechSpec says "AG Grid Enterprise" | Community edition used throughout project |
| 3 | FRD: "template pick from local system" vs TechSpec: "headers from DB" | Server generates Excel from template_master headers; client downloads the generated file |
| 4 | "Editable AG Grid" scope | Two grids: Uploaded Records (read-only) + Mapping Configuration (editable inline) |

---

## READY FOR DEVELOPER: YES
