# E2E Test: Increase Drop Zone Surface Area

Test the expanded drag-and-drop functionality across upper and lower sections of the interface.

## User Story

As a user
I want to drag and drop files directly onto larger areas of the interface (query-section and tables-section)
So that I can upload data more easily without navigating to a modal

## Test Steps

1. Navigate to the `Application URL`
2. Take a screenshot of the initial state
3. **Verify** the page title is "Natural Language SQL Interface"
4. **Verify** no tables are loaded (no-tables message visible in tables-section)
5. **Test Upper Div (query-section):**
   6. Create a test CSV file with sample data (e.g., simple users data)
   7. Drag the file over the query-section div
   8. **Verify** the UI updates to show 'Drop to create table' text or overlay in query-section
   9. Take a screenshot of the dragover state on upper div
   10. Drop the file onto the query-section
   11. **Verify** a success message appears indicating table creation
   12. **Verify** the tables-section now shows the new table
   13. Take a screenshot of the success state with table loaded
14. **Test Lower Div (tables-section):**
   15. Create another test CSV file with different sample data
   16. Drag the file over the tables-section div
   17. **Verify** the UI updates to show 'Drop to create table' text or overlay in tables-section
   18. Take a screenshot of the dragover state on lower div
   19. Drop the file onto the tables-section
   20. **Verify** a success message appears indicating table creation
   21. **Verify** the tables-section now shows both tables
   22. Take a screenshot of the success state with multiple tables
23. **Verify** the existing "Upload Data" modal still works (click button, select file, verify upload)
24. **Verify** invalid file types (e.g., .txt) show an error when dropped

## Success Criteria

- Dragover on query-section and tables-section shows 'Drop to create table' feedback
- Dropping valid files (.csv, .json, .jsonl) on both sections creates tables and shows success
- Tables appear in tables-section after upload
- Existing modal upload remains functional
- Invalid files show appropriate error handling
- 5 screenshots are taken (initial, 2 dragover states, 2 success states)
- No interference with query functionality or other UI elements