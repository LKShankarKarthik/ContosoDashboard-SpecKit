# Tasks: Document Upload and Management

**Input**: [spec.md](./spec.md) | [plan.md](./plan.md) | [stakeholder-clarifications.md](./stakeholder-clarifications.md)  
**Branch**: `001-document-management`  
**Status**: Ready for Implementation

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: User story this task belongs to (US1-US8)
- **Constitution Requirement**: Tests MUST be written and FAIL before implementation

## Phase 1: Setup (Project Initialization)

**Purpose**: Create test project and configure dependencies

- [ ] T001 Create xUnit test project `ContosoDashboard.Tests/ContosoDashboard.Tests.csproj`
- [ ] T002 Add project reference from Tests to ContosoDashboard project
- [ ] T003 Install test dependencies: xUnit, xUnit.runner.visualstudio, Microsoft.NET.Test.Sdk
- [ ] T004 Install Moq for mocking IFileStorageService in tests
- [ ] T005 Add AppData/ to .gitignore for local file uploads
- [ ] T006 Create test data directory structure for test fixtures

**Checkpoint**: Test infrastructure ready

---

## Phase 2: Foundational (Core Entities & Services)

**Purpose**: Database schema and core abstractions that ALL user stories depend on

**⚠️ CRITICAL**: No user story work can begin until this phase is complete

- [ ] T007 [P] Create Document entity in `ContosoDashboard/Models/Document.cs` with all properties and validation attributes
- [ ] T008 [P] Create IFileStorageService interface in `ContosoDashboard/Services/IFileStorageService.cs`
- [ ] T009 Add DbSet<Document> to ApplicationDbContext in `ContosoDashboard/Data/ApplicationDbContext.cs`
- [ ] T010 Run EF migration: `Add-Migration AddDocumentManagement` in Package Manager Console
- [ ] T011 Review migration script for correctness (FK constraints, indexes)
- [ ] T012 Apply migration: `Update-Database` to create Documents table

**Checkpoint**: Foundation ready - database schema created, user story implementation can now begin

---

## Phase 3: User Story 1 - Upload Personal Document (Priority: P1) 🎯 MVP

**Goal**: Enable employees to upload documents with metadata to centralized storage

**Independent Test**: Log in, upload a PDF with title "Test Document" and category "Personal Files", verify it appears in document list

### Tests for User Story 1 (TDD - Write FIRST) ⚠️

> **CONSTITUTION REQUIREMENT: Write these tests FIRST, ensure they FAIL before implementation**

- [ ] T013 [P] [US1] Write unit test: LocalFileStorageService can save file to disk in `Tests/Services/LocalFileStorageServiceTests.cs`
- [ ] T014 [P] [US1] Write unit test: LocalFileStorageService creates directories if they don't exist
- [ ] T015 [P] [US1] Write unit test: LocalFileStorageService generates correct file path pattern (userId/personal/guid.ext)
- [ ] T016 [P] [US1] Write unit test: DocumentService.UploadAsync succeeds with valid file in `Tests/Services/DocumentServiceTests.cs`
- [ ] T017 [P] [US1] Write unit test: DocumentService.UploadAsync rejects oversized file (>25 MB)
- [ ] T018 [P] [US1] Write unit test: DocumentService.UploadAsync rejects invalid file type
- [ ] T019 [P] [US1] Write unit test: DocumentService.UploadAsync validates title length (<=200 chars)
- [ ] T020 [P] [US1] Write unit test: DocumentService.UploadAsync saves file BEFORE creating DB record
- [ ] T021 [P] [US1] Write unit test: DocumentService.GetUserDocumentsAsync returns only current user's documents

**Checkpoint**: All US1 tests written and FAILING (Red phase)

### Implementation for User Story 1

- [ ] T022 [US1] Implement LocalFileStorageService in `ContosoDashboard/Services/LocalFileStorageService.cs` until tests pass
- [ ] T023 [US1] Implement DocumentService constructor with DI in `ContosoDashboard/Services/DocumentService.cs`
- [ ] T024 [US1] Implement DocumentService.UploadAsync() method until tests pass (Green phase)
- [ ] T025 [US1] Implement DocumentService.GetUserDocumentsAsync() method until tests pass
- [ ] T026 [US1] Register services in Program.cs: AddScoped<IFileStorageService, LocalFileStorageService> and AddScoped<DocumentService>
- [ ] T027 [US1] Configure request size limits in Program.cs for 25 MB uploads
- [ ] T028 [US1] Create Documents.razor page in `ContosoDashboard/Pages/Documents.razor` with basic structure
- [ ] T029 [US1] Implement upload form: title input with maxlength=200 and character counter
- [ ] T030 [US1] Implement upload form: category dropdown with predefined options
- [ ] T031 [US1] Implement upload form: description textarea (optional)
- [ ] T032 [US1] Implement upload form: InputFile with maxAllowedSize=26214400
- [ ] T033 [US1] Implement upload form: submit button and progress indicator
- [ ] T034 [US1] Implement HandleFileSelected event handler in Documents.razor.cs
- [ ] T035 [US1] Implement UploadDocument method with client-side validation and error handling
- [ ] T036 [US1] Implement document list table displaying title, category, upload date, file size
- [ ] T037 [US1] Style upload form and document list with Bootstrap classes
- [ ] T038 [US1] Add error/success message display area
- [ ] T039 [US1] Refactor DocumentService if needed while keeping tests green (Refactor phase)

**Checkpoint**: User Story 1 complete and independently testable. User can upload and view personal documents.

---

## Phase 4: User Story 2 - Download and View Own Documents (Priority: P1) 🎯 MVP

**Goal**: Enable users to download their uploaded documents

**Independent Test**: Upload a document, click download button, verify file downloads with original filename

### Tests for User Story 2 (TDD - Write FIRST) ⚠️

- [ ] T040 [P] [US2] Write unit test: LocalFileStorageService.DownloadAsync returns correct file stream
- [ ] T041 [P] [US2] Write unit test: DocumentService.DownloadAsync succeeds for authorized user
- [ ] T042 [P] [US2] Write unit test: DocumentService.DownloadAsync throws exception for unauthorized user
- [ ] T043 [P] [US2] Write unit test: DocumentsController.Download returns 200 with file for authorized request
- [ ] T044 [P] [US2] Write unit test: DocumentsController.Download returns 403 for unauthorized request
- [ ] T045 [P] [US2] Write unit test: DocumentsController.Download returns 404 for non-existent document

**Checkpoint**: All US2 tests written and FAILING (Red phase)

### Implementation for User Story 2

- [ ] T046 [US2] Implement LocalFileStorageService.DownloadAsync() method until test passes
- [ ] T047 [US2] Implement DocumentService.DownloadAsync() with authorization check until tests pass
- [ ] T048 [US2] Create Controllers folder in ContosoDashboard project
- [ ] T049 [US2] Create DocumentsController in `ContosoDashboard/Controllers/DocumentsController.cs`
- [ ] T050 [US2] Implement GET /api/documents/{id}/download endpoint with authorization
- [ ] T051 [US2] Add [Authorize] attribute to DocumentsController
- [ ] T052 [US2] Implement controller action to return FileStreamResult with correct content-type
- [ ] T053 [US2] Add download button/link in Documents.razor for each document
- [ ] T054 [US2] Implement download link: href="/api/documents/{documentId}/download"
- [ ] T055 [US2] Test download with various file types: PDF, Word, Excel, images
- [ ] T056 [US2] Refactor if needed while keeping tests green

**Checkpoint**: User Story 2 complete. Users can download their documents with proper authorization.

---

## Phase 5: User Story 3 - Upload Document to Project (Priority: P2)

**Goal**: Allow project members to upload documents associated with projects

**Independent Test**: Upload document as project member, select project from dropdown, verify other project members see it

### Tests for User Story 3 (TDD - Write FIRST) ⚠️

- [ ] T057 [P] [US3] Write unit test: DocumentService.UploadAsync succeeds when user is project member
- [ ] T058 [P] [US3] Write unit test: DocumentService.UploadAsync fails when user is NOT project member
- [ ] T059 [P] [US3] Write unit test: File path includes projectId for project documents
- [ ] T060 [P] [US3] Write unit test: File path includes "personal" for non-project documents

**Checkpoint**: All US3 tests written and FAILING

### Implementation for User Story 3

- [ ] T061 [US3] Update DocumentService.UploadAsync() to accept optional projectId parameter until tests pass
- [ ] T062 [US3] Implement project membership authorization check in UploadAsync
- [ ] T063 [US3] Update LocalFileStorageService.GetFilePathAsync() to handle projectId in path
- [ ] T064 [US3] Add project dropdown to upload form in Documents.razor (populated with user's projects)
- [ ] T065 [US3] Update HandleUploadDocument to pass projectId to service
- [ ] T066 [US3] Refactor if needed

**Checkpoint**: User Story 3 complete. Project documents can be uploaded by members.

---

## Phase 6: User Story 7 - View Project Documents (Priority: P2)

**Goal**: Project members can view all documents associated with their projects

**Independent Test**: Have User A upload doc to project, login as User B (also project member), verify User B sees the document

### Tests for User Story 7 (TDD - Write FIRST) ⚠️

- [ ] T067 [P] [US7] Write unit test: DocumentService.GetProjectDocumentsAsync returns docs for project members
- [ ] T068 [P] [US7] Write unit test: DocumentService.GetProjectDocumentsAsync returns empty for non-members
- [ ] T069 [P] [US7] Write unit test: Authorization checks CURRENT membership (not historical)

**Checkpoint**: All US7 tests written and FAILING

### Implementation for User Story 7

- [ ] T070 [US7] Implement DocumentService.GetProjectDocumentsAsync() with authorization check until tests pass
- [ ] T071 [US7] Update Documents.razor to add filter by project
- [ ] T072 [US7] Update ProjectDetails.razor to add Documents section
- [ ] T073 [US7] Display project documents list in ProjectDetails page
- [ ] T074 [US7] Add authorization check: hide documents section for non-members
- [ ] T075 [US7] Refactor if needed

**Checkpoint**: User Story 7 complete. Project documents visible to team members.

---

## Phase 7: Project Deletion Validation (P2 Requirement)

**Goal**: Prevent deletion of projects that have associated documents

### Tests (TDD - Write FIRST) ⚠️

- [ ] T076 [P] Write unit test: ProjectService.DeleteAsync fails when project has documents
- [ ] T077 [P] Write unit test: Error message "Cannot delete project with documents" shown correctly

**Checkpoint**: Tests written and FAILING

### Implementation

- [ ] T078 Update ProjectService.DeleteAsync() to check for documents before deletion until test passes
- [ ] T079 Update Project deletion UI to display validation error message
- [ ] T080 Refactor if needed

**Checkpoint**: Project deletion validation complete.

---

## Phase 8: User Story 4 - Search and Filter Documents (Priority: P2)

**Goal**: Users can quickly find documents via search and filters

**Independent Test**: Upload 10 documents with different titles/categories, use search to find specific one

### Tests for User Story 4 (TDD - Write FIRST) ⚠️

- [ ] T081 [P] [US4] Write unit test: DocumentService.SearchDocumentsAsync finds by title (partial match)
- [ ] T082 [P] [US4] Write unit test: Search finds by description
- [ ] T083 [P] [US4] Write unit test: Search finds by tags
- [ ] T084 [P] [US4] Write unit test: Search respects authorization (only user's + project docs)
- [ ] T085 [P] [US4] Write unit test: Filter by category works correctly
- [ ] T086 [P] [US4] Write unit test: Filter by project works correctly  
- [ ] T087 [P] [US4] Write unit test: Filter by date range works correctly
- [ ] T088 [P] [US4] Write unit test: Combined filters work together
- [ ] T089 [P] [US4] Write unit test: Sort by title (asc/desc) works
- [ ] T090 [P] [US4] Write unit test: Sort by upload date works
- [ ] T091 [P] [US4] Write performance test: Search returns results < 2 seconds

**Checkpoint**: All US4 tests written and FAILING

### Implementation for User Story 4

- [ ] T092 [US4] Implement DocumentService.SearchDocumentsAsync() method until tests pass
- [ ] T093 [US4] Implement filter parameters in GetUserDocumentsAsync
- [ ] T094 [US4] Implement sorting logic in GetUserDocumentsAsync
- [ ] T095 [US4] Add search bar to Documents.razor page
- [ ] T096 [US4] Add category filter dropdown to Documents.razor
- [ ] T097 [US4] Add project filter dropdown to Documents.razor
- [ ] T098 [US4] Add date range filter controls to Documents.razor
- [ ] T099 [US4] Implement sort column headers (clickable) in document table
- [ ] T100 [US4] Wire up search/filter/sort to call DocumentService methods
- [ ] T101 [US4] Add loading indicator during search
- [ ] T102 [US4] Refactor if needed

**Checkpoint**: User Story 4 complete. Full search and filter functionality working.

---

## Phase 9: User Story 5 - Edit Document Metadata (Priority: P3)

**Goal**: Document owners can update metadata (title, description, category, tags)

**Independent Test**: Upload document, click edit, change title, verify change persists

### Tests for User Story 5 (TDD - Write FIRST) ⚠️

- [ ] T103 [P] [US5] Write unit test: DocumentService.UpdateMetadataAsync succeeds for owner
- [ ] T104 [P] [US5] Write unit test: UpdateMetadataAsync fails for non-owner
- [ ] T105 [P] [US5] Write unit test: UpdateMetadataAsync preserves original upload date
- [ ] T106 [P] [US5] Write unit test: UpdateMetadataAsync validates title length
- [ ] T107 [P] [US5] Write unit test: Project Manager can edit project documents

**Checkpoint**: All US5 tests written and FAILING

### Implementation for User Story 5

- [ ] T108 [US5] Implement DocumentService.UpdateMetadataAsync() with authorization until tests pass
- [ ] T109 [US5] Add Edit button to Documents.razor (conditional: only for owners)
- [ ] T110 [US5] Create edit modal dialog component in Documents.razor
- [ ] T111 [US5] Implement edit form with title, description, category, tags fields
- [ ] T112 [US5] Implement HandleEditDocument method to call service
- [ ] T113 [US5] Add success/error message display for edit operations
- [ ] T114 [US5] Refactor if needed

**Checkpoint**: User Story 5 complete. Metadata editing functional.

---

## Phase 10: User Story 6 - Delete Own Documents (Priority: P3)

**Goal**: Document owners can delete files they've uploaded

**Independent Test**: Upload document, click delete, confirm, verify document removed from list and filesystem

### Tests for User Story 6 (TDD - Write FIRST) ⚠️

- [ ] T115 [P] [US6] Write unit test: DocumentService.DeleteAsync succeeds for owner
- [ ] T116 [P] [US6] Write unit test: DeleteAsync fails for non-owner
- [ ] T117 [P] [US6] Write unit test: DeleteAsync removes database record
- [ ] T118 [P] [US6] Write unit test: LocalFileStorageService.DeleteAsync removes file from filesystem
- [ ] T119 [P] [US6] Write unit test: Project Manager can delete project documents
- [ ] T120 [P] [US6] Write unit test: Administrator can delete any document

**Checkpoint**: All US6 tests written and FAILING

### Implementation for User Story 6

- [ ] T121 [US6] Implement LocalFileStorageService.DeleteAsync() until test passes
- [ ] T122 [US6] Implement DocumentService.DeleteAsync() with authorization and file deletion until tests pass
- [ ] T123 [US6] Add Delete button to Documents.razor (conditional: only for authorized users)
- [ ] T124 [US6] Create delete confirmation dialog
- [ ] T125 [US6] Implement HandleDeleteDocument method to call service
- [ ] T126 [US6] Add success/error message display for delete operations
- [ ] T127 [US6] Refactor if needed

**Checkpoint**: User Story 6 complete. Deletion functionality with proper authorization.

---

## Phase 11: User Story 8 - Dashboard Integration (Priority: P3)

**Goal**: Recent documents widget on dashboard home page

**Independent Test**: Upload 5+ documents, navigate to dashboard, verify 5 most recent appear in widget

### Tests for User Story 8 (TDD - Write FIRST) ⚠️

- [ ] T128 [P] [US8] Write unit test: DocumentService.GetRecentDocumentsAsync returns 5 most recent
- [ ] T129 [P] [US8] Write unit test: Recent documents ordered by upload date descending
- [ ] T130 [P] [US8] Write unit test: Recent documents includes only user's accessible documents

**Checkpoint**: All US8 tests written and FAILING

### Implementation for User Story 8

- [ ] T131 [US8] Implement DocumentService.GetRecentDocumentsAsync() until tests pass
- [ ] T132 [US8] Create RecentDocumentsWidget component in `ContosoDashboard/Shared/RecentDocumentsWidget.razor`
- [ ] T133 [US8] Implement widget display logic (5 docs, "No documents" empty state)
- [ ] T134 [US8] Add click handler to navigate to Documents page
- [ ] T135 [US8] Update Index.razor (dashboard) to include RecentDocumentsWidget
- [ ] T136 [US8] Style widget to match dashboard layout
- [ ] T137 [US8] Refactor if needed

**Checkpoint**: User Story 8 complete. Dashboard integration functional.

---

## Phase 12: Polish & Cross-Cutting Concerns

**Purpose**: Final improvements affecting multiple features

- [ ] T138 [P] Add XML documentation comments to all public methods in DocumentService
- [ ] T139 [P] Add XML documentation comments to IFileStorageService interface
- [ ] T140 [P] Add educational comments in DocumentService explaining production requirements (virus scanning, Azure migration, etc.)
- [ ] T141 [P] Verify all error messages are user-friendly and actionable
- [ ] T142 [P] Add logging statements at key operations (upload, delete, authorization failures)
- [ ] T143 [P] Update README.md with document management feature description
- [ ] T144 Run all unit tests and verify 100% pass rate
- [ ] T145 Run manual testing checklist from plan.md
- [ ] T146 Verify performance targets (2s list, 2s search, 3s preview)
- [ ] T147 Security testing: attempt IDOR attacks, verify blocked
- [ ] T148 Test file integrity: upload/download cycle preserves binary data
- [ ] T149 Code review: verify Constitution compliance (spec-first, TDD, service layer, security)
- [ ] T150 Final refactoring: eliminate code duplication, improve naming

**Checkpoint**: Feature complete, polished, and production-ready (or training-ready)

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies - start immediately
- **Foundational (Phase 2)**: Depends on Setup completion - BLOCKS all user stories
- **User Stories (Phases 3-11)**: All depend on Foundational phase
  - **P1 Stories (US1, US2)**: Can start after Foundational
  - **P2 Stories (US3, US4, US7)**: Can start after Foundational, should deliver after P1
  - **P3 Stories (US5, US6, US8)**: Can start after Foundational, should deliver after P2
- **Polish (Phase 12)**: Depends on all implemented user stories

### Constitution TDD Workflow (CRITICAL)

For EVERY user story:
1. ✅ Write tests FIRST (Red phase)
2. ✅ Verify tests FAIL
3. ✅ Implement minimum code to pass tests (Green phase)
4. ✅ Refactor while keeping tests green (Refactor phase)
5. ✅ Move to next user story

**DO NOT** implement before tests are written and failing.

### Parallel Opportunities

**Within Setup phase**: All tasks can run in parallel  
**Within Foundational phase**: T007, T008 can run in parallel  
**Within each User Story**: All test tasks marked [P] can run in parallel  
**User Stories**: Different stories can be worked by different team members simultaneously after Foundational complete

**Example - US1 Test Parallelism**:
- T013-T021 (all US1 tests) can be written simultaneously by different developers
- Review together before any implementation begins

### Implementation Strategy

#### MVP First (Recommended)
1. Phase 1: Setup → Test infrastructure ready
2. Phase 2: Foundational → Database ready
3. Phase 3: US1 → Upload working
4. Phase 4: US2 → Download working
5. **STOP & VALIDATE**: Core upload/download functional - could ship this as minimal viable product
6. Continue with P2/P3 features incrementally

#### Parallel Team Execution
With 3 developers after Foundational complete:
- Developer A: US1 + US2 (P1 upload/download)
- Developer B: US3 + US7 (P2 project integration)
- Developer C: US4 (P2 search/filter)
- Then regroup for P3 features: US5, US6, US8

---

## Task Statistics

- **Total Tasks**: 150
- **Test Tasks**: 54 (TDD compliance - 36% of total)
- **Implementation Tasks**: 96 (64% of total)
- **Parallel Opportunities**: 45 tasks marked [P]

**Test Coverage**:
- User Story 1: 9 tests
- User Story 2: 6 tests
- User Story 3: 4 tests
- User Story 4: 11 tests
- User Story 5: 5 tests
- User Story 6: 6 tests
- User Story 7: 3 tests
- User Story 8: 3 tests
- Project Deletion: 2 tests
- Polish: 5 additional tests

**Estimated Timeline** (based on plan.md):
- Setup: 2 hours
- Foundational: 2 hours
- User Stories: 24-34 hours (depends on parallelization)
- Polish: 2-4 hours
- **Total**: 30-42 hours

---

## Progress Tracking

### Completed Phases
- [ ] Phase 1: Setup
- [ ] Phase 2: Foundational

### Completed User Stories
- [ ] US1: Upload Personal Document (P1) 🎯
- [ ] US2: Download and View (P1) 🎯
- [ ] US3: Upload to Project (P2)
- [ ] US7: View Project Documents (P2)
- [ ] Project Deletion Validation (P2)
- [ ] US4: Search and Filter (P2)
- [ ] US5: Edit Metadata (P3)
- [ ] US6: Delete Documents (P3)
- [ ] US8: Dashboard Integration (P3)

### Completed Checkpoints
- [ ] Test infrastructure ready
- [ ] Foundation ready (DB schema created)
- [ ] US1: Upload working independently
- [ ] US2: Download working independently
- [ ] US1+US2: MVP delivery possible
- [ ] US3: Project upload working
- [ ] US7: Project viewing working
- [ ] Project deletion validation complete
- [ ] US4: Search and filter complete
- [ ] US5: Edit metadata complete
- [ ] US6: Delete complete
- [ ] US8: Dashboard integration complete
- [ ] All features complete and tested

---

## Next Steps

1. ✅ Specification approved
2. ✅ Plan created
3. ✅ Tasks created
4. ⏭️ **START Phase 1**: Create test project (T001)
5. ⏭️ **Complete Foundational**: Create entities and migrations (T007-T012)
6. ⏭️ **Write US1 Tests**: T013-T021 (verify they FAIL)
7. ⏭️ **Implement US1**: T022-T039 (make tests PASS)
8. ⏭️ Continue with remaining user stories in priority order

**Ready to begin TDD implementation following Constitution principles.**

---

**Tasks Version**: 1.0  
**Last Updated**: 2026-02-25  
**Next Review**: After Phase 3 (US1) completion
