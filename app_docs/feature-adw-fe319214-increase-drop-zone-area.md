# Increase Drop Zone Surface Area

**ADW ID:** fe319214
**Date:** 2025-10-21
**Specification:** specs/issue-14-adw-fe319214-sdlc_planner-increase-drop-zone-area.md

## Overview

This feature expands the drag-and-drop upload area from the limited modal drop zone to the entire upper query-section and lower tables-section of the interface. Users can now directly drag and drop supported files (CSV, JSON, JSONL) onto these larger areas, which display a visual overlay prompting \"Drop to create table\" during the drag operation. Upon dropping, the existing file upload functionality is triggered, creating tables and reloading the schema, thereby improving the user experience by making data uploads more intuitive and accessible without requiring interaction with the upload modal.

## Screenshots

No screenshots directory was provided.

## What Was Built

- Extended drag-and-drop event handling to query-section (upper div) and tables-section (lower div)
- Dynamic overlay system for visual feedback during drag operations
- File type validation and single-file restriction in drop handlers
- E2E test suite for validating the expanded drop zone behavior
- Playwright automation screenshots for initial state and table creation
- Minor updates to package.json, mcp configs, and test data file

## Technical Implementation

### Files Modified

- `app/client/src/main.ts`: Added dragover, dragleave, and drop event listeners to query-section and tables-section elements; implemented createDropOverlay helper function; integrated drop handling with existing handleFileUpload, including file validation; positioned sections as relative for overlay placement.

- `app/client/src/style.css`: Added .dragover styles for sections with dashed borders and background highlights; defined .drop-overlay class for semi-transparent overlays with centered text and pointer-events: none to allow drops through the overlay; included transitions for smooth visual changes.

- `.claude/commands/e2e/test_increase-drop-zone-area.md`: New E2E test file outlining steps to validate drag-and-drop on expanded zones, UI feedback, file upload, table creation, and success messaging.

- `app/client/package.json`: Minor dependency or script updates.

- `playwright-mcp-config.json` and `.mcp.json`: Configuration adjustments for Playwright MCP integration.

- `test_users.csv`: Added sample test data for validation.

### Key Changes

- Overlay creation on dragover appends a full-coverage div with background, dashed border, and \"Drop to create table\" text, removed on dragleave or drop.
- Drop events prevent defaults, validate file count (single only) and extensions (.csv, .json, .jsonl), displaying errors for invalid cases before calling handleFileUpload.
- Dragleave logic checks if the target is outside the section to avoid premature removal during intra-element drags.
- Styles enhance UX with 0.3s transitions, rgba backgrounds for subtlety, and z-index: 10 for overlay prominence over content.
- E2E test ensures no regressions in existing modal upload while confirming new direct-drop functionality.

## How to Use

1. Launch the application in a browser and ensure the interface loads with query-section (upper) and tables-section (lower) visible.
2. Drag a supported file (e.g., CSV or JSON) from your file system over either the query-section or tables-section.
3. Observe the section highlight with a dashed border and the overlay displaying \"Drop to create table\" in the center.
4. Release the file to drop it; the overlay disappears, the file is processed, a new table is created, the schema reloads, and a success message appears.
5. Verify the table in the tables-section and test queries if applicable.

The original \"Upload Data\" modal button and its drop zone remain fully functional for alternative upload methods.

## Configuration

No new configuration options or environment variables are required. The feature leverages existing upload API endpoints and file handling logic in the backend. Ensure Playwright is configured for E2E testing if validating screenshots.

## Testing

- Execute the E2E test: Read `.claude/commands/test_e2e.md`, then read and run `.claude/commands/e2e/test_increase-drop-zone-area.md` to simulate drag-and-drop, confirm UI changes, upload success, and table rendering.
- Frontend type checking: `cd app/client && bun tsc --noEmit` to ensure no TypeScript errors from new event handlers.
- Frontend build: `cd app/client && bun run build` to verify compilation without issues.
- Backend tests: `cd app/server && uv run pytest` to confirm no regressions in file processing or table creation APIs.
- Manual testing: Drag invalid files (e.g., multiple or wrong type) to verify error messages; test on different sections to ensure consistent behavior.

## Notes

- Only single files are supported; attempting multiple drops triggers an error message.
- Invalid file types are blocked client-side before upload, reducing unnecessary API calls.
- The feature maintains full backward compatibility with the modal-based upload flow.
- Screenshots from Playwright (01_initial_state.png, 02_table_created.png) can be referenced for visual validation, located in .playwright-mcp/.
- Future enhancements could include touch support for mobile or visual indicators outside dragover states.
- Edge cases like dropping non-file data or interrupting drags are handled by native browser behavior and error checks.