# Feature: Increase Drop Zone Surface Area

## Metadata

issue_number: `14`
adw_id: `fe319214`
issue_json: `{\"number\":14,\"title\":\"Increase drop zone surface area\",\"body\":\"/feature - adw_sdlc_iso - lets increase the drop zone suface area. instead of having to click \\\"upload data\\\", let the user drag and drop right on the upper div or lower div and the ui will update to a 'drop to create table' text. This runs the same usual functionality but enhances the ui to be more user friendly.\"}`

## Feature Description

This feature enhances the user interface by increasing the surface area for drag-and-drop file uploads. Currently, users must click the \"Upload Data\" button to open a modal with a limited drop zone. The new implementation allows users to drag and drop files directly onto larger areas of the interface (upper and lower divs), updating the UI to display 'Drop to create table' text during the drag operation. Upon dropping a file, the existing upload functionality is triggered, creating a table from the uploaded CSV or JSON data. This improves user experience by making file uploads more intuitive and accessible without requiring button clicks.

## User Story

As a user
I want to drag and drop files directly onto larger areas of the interface
So that I can upload data more easily without navigating to a modal

## Problem Statement

The current upload process requires users to click the \"Upload Data\" button to access a modal with a small drop zone, which can feel cumbersome and less intuitive for users expecting direct drag-and-drop support across the main interface.

## Solution Statement

Expand the drag-and-drop functionality to cover larger sections of the UI (e.g., the upper query section and lower tables section) by adding drag event listeners to these divs. During dragover, update the text or add overlay prompts like 'Drop to create table'. On drop, handle the file upload using the existing API call, ensuring the same processing and table creation occurs. This maintains backward compatibility while enhancing usability.

## Relevant Files

Use these files to implement the feature:

- `app/client/src/main.ts` - Handles file upload logic, drag-and-drop events on the current drop zone, and UI updates. Relevant for extending drag events to larger divs and integrating with existing upload handling.
- `app/client/index.html` - Contains the HTML structure including the query-section (upper div) and tables-section (lower div). Relevant for identifying target elements to add droppable behavior if needed, though primarily modifications will be in JS.
- `app/client/src/style.css` - Defines styles for the drop zone and other UI elements. Relevant for adding new styles for dragover states on expanded areas and text overlays.
- `README.md` - Project overview, confirming drag-and-drop supports .csv and .json files, ensuring new implementation aligns with existing features.
- `.claude/commands/test_e2e.md` and `.claude/commands/e2e/test_basic_query.md` - Examples for creating E2E tests to validate the new drag-and-drop behavior.

### New Files

- `.claude/commands/e2e/test_increase-drop-zone-area.md` - E2E test file to validate the expanded drop zone functionality.

## Implementation Plan

### Phase 1: Foundation

Review and understand the current drag-and-drop implementation in the upload modal's drop zone. Identify the upper div (query-section) and lower div (tables-section) as target areas for expansion. Ensure no conflicts with existing modal-based upload.

### Phase 2: Core Implementation

Add drag event listeners (dragover, dragleave, drop) to the upper and lower divs. Implement visual feedback like text changes to 'Drop to create table' during dragover. On drop, extract the file and call the existing handleFileUpload function.

### Phase 3: Integration

Integrate with the existing upload success flow, reloading schema and displaying tables. Update styles for new droppable areas. Test for edge cases like multiple drops or invalid files.

## Step by Step Tasks

### Task 1: Create E2E Test File

- Read `.claude/commands/test_e2e.md` and `.claude/commands/e2e/test_basic_query.md` to understand E2E test structure.
- Create `.claude/commands/e2e/test_increase-drop-zone-area.md` with steps to validate dragging a file over upper/lower divs, seeing 'Drop to create table' text, dropping the file, and confirming table creation and success message.

### Task 2: Extend Drag-and-Drop to Upper Div

- In `app/client/src/main.ts`, select the query-section element.
- Add dragover event listener to show 'Drop to create table' text (update inner text or add overlay).
- Add dragleave to revert UI.
- Add drop event to handle file extraction and call `handleFileUpload(file)`.

### Task 3: Extend Drag-and-Drop to Lower Div

- Select the tables-section element.
- Implement similar dragover, dragleave, and drop events as in Task 2, ensuring UI feedback like text change in the no-tables message or overlay.

### Task 4: Update Styles for Expanded Drop Zones

- In `app/client/src/style.css`, add classes for dragover states on query-section and tables-section (e.g., border changes, background highlights).
- Style the 'Drop to create table' text or overlay for visibility.

### Task 5: Handle Edge Cases in Upload Logic

- Modify `handleFileUpload` if needed to close any open modal or ensure compatibility.
- Add checks for valid file types (.csv, .json, .jsonl) directly in drop handlers.

### Task 6: Test Integration

- Manually test drag-and-drop on upper and lower divs to ensure table creation and schema reload.
- Verify no interference with existing modal upload.

### Task 7: Run Validation Commands

- Read `.claude/commands/test_e2e.md`, then read and execute `.claude/commands/e2e/test_increase-drop-zone-area.md` to validate this functionality works.
- `cd app/server && uv run pytest` - Run server tests to validate the feature works with zero regressions
- `cd app/client && bun tsc --noEmit` - Run frontend tests to validate the feature works with zero regressions
- `cd app/client && bun run build` - Run frontend build to validate the feature works with zero regressions

## Testing Strategy

### Unit Tests

Add unit tests in frontend (if using a testing framework) to mock drag events on divs and verify `handleFileUpload` is called. Test visual feedback by simulating dragover/dragleave.

### Edge Cases

- Dragging non-file items (e.g., text) over divs – should ignore or show error.
- Dropping multiple files – handle only the first or show warning.
- Dropping invalid file types – display error message.
- Interrupting drop with modal open – ensure consistent behavior.
- Mobile/touch drag support (if applicable, though focus on desktop).

## Acceptance Criteria

- Users can drag files over upper (query-section) and lower (tables-section) divs without opening the modal.
- During dragover, UI updates to show 'Drop to create table' text or overlay in the targeted area.
- On drop, file is uploaded via existing API, table is created, schema reloads, and success message appears.
- Existing modal upload remains functional.
- No regressions in query execution or table management.
- E2E test passes, validating the new drag-and-drop flow.

## Validation Commands

Execute every command to validate the feature works correctly with zero regressions.

- Read `.claude/commands/test_e2e.md`, then read and execute `.claude/commands/e2e/test_increase-drop-zone-area.md` to validate this functionality works.
- `cd app/server && uv run pytest` - Run server tests to validate the feature works with zero regressions
- `cd app/client && bun tsc --noEmit` - Run frontend tests to validate the feature works with zero regressions
- `cd app/client && bun run build` - Run frontend build to validate the feature works with zero regressions

## Notes

- Ensure drag events prevent default behavior to avoid browser defaults.
- Consider adding a subtle border highlight on dragover for better UX.
- No new libraries needed; use native File API and existing upload logic.
- Future: Could extend to entire page except query input to maximize area.