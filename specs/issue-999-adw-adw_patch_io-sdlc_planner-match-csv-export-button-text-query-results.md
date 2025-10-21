# Bug: Match CSV Export Button Text in Query Results

## Metadata

issue_number: `999`
adw_id: `adw_patch_io`
issue_json: `{\"title\": \"Match CSV export button text in query results section\", \"body\": \"Under available tables section the csv export button has the correct text \\\"CSV\\\" with an ICON to the left of the text. Update the query result section csv export button text should match this.\"}`

## Bug Description

The CSV export button in the query results section displays '📊 CSV Export' while the corresponding button in the available tables section correctly shows '📊 CSV'. This inconsistency in button text reduces UI uniformity and may confuse users expecting consistent labeling across export functionalities.

## Problem Statement

The export button text in the query results header uses `${getDownloadIcon()} Export` which appends ' Export' to the base icon+text, whereas the table export button uses only `getDownloadIcon()` which provides the icon and 'CSV' text. The query results button should match the tables button text exactly: '📊 CSV'.

## Solution Statement

Update the innerHTML assignment for the query results export button to use `getDownloadIcon()` directly, removing the ' Export' suffix to achieve text consistency with the tables export button. No changes to functionality, API calls, or styling are required; this is purely a text update to ensure UI consistency.

## Steps to Reproduce

1. Start the application (`./scripts/start.sh`).
2. Upload a sample CSV file (e.g., products.csv) via the upload modal.
3. Verify the table appears in the 'Available Tables' section with an export button displaying '📊 CSV'.
4. Enter a simple query like 'SELECT \* FROM products LIMIT 5' and execute it.
5. Observe the query results section where the export button displays '📊 CSV Export'.
6. Note the text difference: tables shows 'CSV', query results shows 'CSV Export'.

## Root Cause Analysis

The inconsistency stems from differing innerHTML assignments in `app/client/src/main.ts`:

- Tables export (line ~340): `exportButton.innerHTML = getDownloadIcon();` → '📊 CSV'
- Query results export (line ~242): `exportButton.innerHTML = \`${getDownloadIcon()} Export\`;`→ '📊 CSV Export'
The`getDownloadIcon()` function returns a standardized icon+text string, but the query implementation incorrectly appends ' Export'. This was likely an oversight during feature implementation to differentiate sections, but it violates UI consistency principles.

## Relevant Files

Use these files to fix the bug:

- `app/client/src/main.ts`: Primary file containing UI logic for both export buttons. Update the innerHTML for the query results export button on line ~242 to match the tables button implementation. Relevant because it handles dynamic button creation and text assignment.
- `app/client/src/style.css`: Contains styling for `.export-button` and `.table-export-button` classes used by both buttons. No changes needed, but verify consistency in button appearance post-text update.
- `app/client/src/api/client.ts`: Handles the export API calls (`exportTable` and `exportQueryResults`). No changes required as the bug is UI-only, but confirm the onclick handlers remain unchanged.

### New Files

- `.claude/commands/e2e/test_csv-export-button-text-consistency.md`: New E2E test file to validate button text matching after the fix.

Read `.claude/commands/e2e/test_basic_query.md` and `.claude/commands/e2e/test_complex_query.md` to understand E2E test structure, then create the new file above.

## Step by Step Tasks

### Analyze Current Implementation

- Read `app/client/src/main.ts` lines 225-261 (query results button creation) and lines 337-356 (tables button creation) to confirm the text discrepancy.
- Verify `getDownloadIcon()` at line 17 returns '📊 CSV' consistently.

### Update Query Results Button Text

- In `app/client/src/main.ts`, locate the query results export button creation around line 242.
- Change `exportButton.innerHTML = \`${getDownloadIcon()} Export\`;`to`exportButton.innerHTML = getDownloadIcon();`.
- Ensure the title attribute ('Export results as CSV') remains for accessibility.
- Confirm the className ('export-button secondary-button') and onclick handler (`api.exportQueryResults`) are unchanged.

### Verify Styling Consistency

- Read `app/client/src/style.css` sections for `.export-button` (lines 343-361) and `.table-export-button` (lines 363-372) to ensure text shortening doesn't affect layout or hover states.
- No CSS changes needed unless truncation occurs, but test visually.

### Create E2E Test for UI Consistency

- Read `.claude/commands/e2e/test_basic_query.md` and `.claude/commands/e2e/test_complex_query.md` to understand E2E test structure and screenshot usage.
- Create a new E2E test file `.claude/commands/e2e/test_csv-export-button-text-consistency.md` that validates the bug is fixed:
  - Navigate to application and upload sample data.
  - Verify tables export button text is '📊 CSV' via element textContent assertion.
  - Execute a query with results.
  - Verify query results export button text is '📊 CSV' via element textContent assertion.
  - Take screenshots of both buttons before/after interaction.
  - Include steps to prove text matching and no regressions in functionality (e.g., successful exports).

### Validation and Testing

- Run `cd app/client && bun tsc --noEmit` to check TypeScript compilation post-change.
- Manually test: Upload data, export table (verify download), query and export results (verify download and text consistency).
- Run `cd app/client && bun run build` to ensure no build errors.
- Read `.claude/commands/test_e2e.md`, then read and execute `.claude/commands/e2e/test_csv-export-button-text-consistency.md` to validate button text consistency.
- Run `cd app/server && uv run pytest` to ensure no backend regressions (though unrelated).
- Run `cd app/client && bun tsc --noEmit` again for final TS check.
- Run `cd app/client && bun run build` for final build validation.

## Validation Commands

Execute every command to validate the bug is fixed with zero regressions.

- `./scripts/start.sh` - Start application to test UI.
- Upload sample CSV via UI and inspect tables export button element textContent; verify equals '📊 CSV'.
- Execute query 'SELECT \* FROM [table] LIMIT 5'; inspect query results export button element textContent; verify equals '📊 CSV' (before fix it was '📊 CSV Export').
- Click both export buttons; verify CSV files download successfully with correct data (no functionality regression).
- Read `.claude/commands/test_e2e.md`, then read and execute your new E2E `.claude/commands/e2e/test_csv-export-button-text-consistency.md` test file to validate this functionality works.
- `cd app/server && uv run pytest` - Run server tests to validate the bug is fixed with zero regressions
- `cd app/client && bun tsc --noEmit` - Run frontend tests to validate the bug is fixed with zero regressions
- `cd app/client && bun run build` - Run frontend build to validate the bug is fixed with zero regressions
- `./scripts/stop_apps.sh` - Clean shutdown.

## Notes

- This is a minimal UI text fix; no new libraries or major refactors needed.
- The `getDownloadIcon()` function provides the standardized icon+text, ensuring future consistency if updated.
- E2E test focuses on text assertion and screenshots for visual verification, minimal steps to prove fix without broad regression.
