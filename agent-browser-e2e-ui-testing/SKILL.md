---
name: agent-browser-e2e-ui-testing
description: Exhaustively test a web application's UI using the locally installed agent-browser CLI, covering CRUD, data integrity, relationships, calculations, and cross-module workflows without reading source code or accessing APIs or databases directly.
---

# Intensive End-to-End UI Testing

## Purpose

Act as a senior QA engineer. Test the entire application **exclusively through its UI** using the locally installed `agent-browser` package, check with `which agent-browser` and `agent-browser --help`. Verify functionality, persistence, data integrity, relationships, calculations, and complete user workflows.

## Non-negotiable rules

- Use `agent-browser` for all application interactions. Inspect its usage instructions as needed.
- **Do not** modify implementation files.
- Discover behavior through browser-visible UI only; never infer success from appearance or notifications alone.
- Use isolated, clearly identifiable test data. Avoid destructive actions on real user data.
- Record actual results and evidence. Never mark an unexecuted test as passed.

## Workflow

### 1. Discover and plan

Navigate every accessible module, page, tab, resource, form, dialog, and workflow. Create a coverage checklist of features, CRUD actions, relationships, calculations, and cross-module journeys. Track each scenario as **PASS**, **FAIL**, **BLOCKED**, **SKIPPED**, or **N/A**.

### 2. Test every resource

- **Create:** Validate required and optional fields, defaults, invalid inputs, duplicates, and persistence.
- **Read:** Check lists, details, search, filters, sorting, pagination, and empty states.
- **Update:** Exercise every editing interface (dialog, page, inline). Verify saved values and preservation of untouched fields.
- **Delete:** Test cancellation, confirmation, removal, and effects on related resources.

**After every create, update, or delete, navigate away or refresh, then re-read the affected resources to verify persisted state.**

### 3. Verify untouched fields (critical)

For each update, record all accessible original field values, change only selected fields, save, and reopen. Compare every field: intended changes must persist; untouched fields must remain unchanged unless a documented, visibly justified derived change applies. Check for cleared fields, unintended defaults, changed relationships, and lost values. Repeat across different fields and editing interfaces.

### 4. Verify relationships and movements

Create, change, move, and remove discoverable associations. Confirm correct membership in old and new parent/group views, dependent records, counts, balances, and totals. Revisit affected pages after refresh to detect stale, duplicate, or incorrectly linked records.

### 5. Independently verify calculations

Use controlled UI-created data to calculate expected totals, balances, discounts, taxes, percentages, averages, dashboard metrics, charts, and reports. Recalculate after CRUD and relationship changes. Cover zero, decimal, rounding, boundary, and empty-data cases where applicable. Check consistency across views.

### 6. Test complete workflows and UI behavior

Run end-to-end journeys across modules, including successful, invalid, canceled, and interrupted paths. Exercise forms, navigation, dialogs, controls, validation messages, loading/error/empty states, and desktop/mobile layouts where feasible. Confirm state remains correct after navigation, refresh, and repeat actions.

### 7. Log defects and run final regression

For each defect, capture severity, affected feature, exact browser reproduction steps, expected versus actual results, and screenshots or other browser-visible evidence. Do not fix code.

After module-level testing, perform a second intensive browser regression covering critical CRUD, cross-module workflows, relationship movements, untouched-field preservation, calculations, and known defects.

## Required final report

Produce a Markdown report with:

1. **Executive summary:** Overall health and passed/failed/blocked/skipped totals.
2. **Coverage matrix:** Resource/module × CRUD, relations, calculations, and status.
3. **Defects:** Severity, reproduction, expected/actual behavior, and evidence.
4. **Data integrity:** Persistence, untouched fields, movements, and statistics.
5. **Remaining risks:** Untested or blocked scenarios and limitations.
6. **Verdict:** **PASS**, **PASS WITH WARNINGS**, **FAIL**, or **INCOMPLETE**.

## Completion criteria

Begin with UI discovery, then systematically execute the checklist and finish with regression. Do not stop at smoke testing or silently skip features. Claim full coverage only if every identified applicable scenario was actually executed and verified; otherwise document gaps explicitly.
