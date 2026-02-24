# Implementation Plan: Document Upload and Management

**Branch**: `001-document-management` | **Date**: 2026-02-25 | **Spec**: [spec.md](./spec.md)  
**Input**: Feature specification from `/specs/001-document-management/spec.md`  
**Status**: Ready for Phase 1 TDD Implementation

## Summary

Add document upload and management capabilities to ContosoDashboard, enabling employees to upload, organize, download, and share work-related documents. Documents can be categorized, associated with projects, and searched. The feature uses a service layer architecture with local file storage (abstracted via `IFileStorageService` interface for future Azure migration). All implementation follows Test-Driven Development with proper authorization checks at multiple layers.

**Primary Requirements**:
- Upload documents with metadata (title, category, description, project, tags)
- Store files securely outside wwwroot with GUID-based filenames
- Download and preview documents (PDF, images)
- Associate documents with projects for team collaboration
- Search and filter documents by multiple criteria
- Edit metadata and delete documents (owner-based authorization)
- Dashboard integration showing recent documents

**Technical Approach**:
- Blazor Server pages for UI
- Service layer (DocumentService) for business logic
- Entity Framework Core for data persistence
- Local filesystem storage (LocalFileStorageService)
- Claims-based authorization consistent with mock auth
- Defense-in-depth validation (client + server)

## Technical Context

**Language/Version**: C# 12 / .NET 9.0  
**Framework**: Blazor Server (ASP.NET Core 9.0)  
**Primary Dependencies**: 
- Microsoft.EntityFrameworkCore.SqlServer 9.0.0
- Microsoft.AspNetCore.Components.Authorization
- Microsoft.AspNetCore.Authentication.Cookies

**Storage**: 
- Database: SQL Server (LocalDB for training)
- Files: Local filesystem (`AppData/uploads/` directory)

**Testing**: xUnit (when tests are added per Constitution)  
**Target Platform**: Windows (training), Cross-platform capable  
**Project Type**: Web application (Blazor Server)  

**Performance Goals**:
- Document upload: <30s for 25 MB files
- Document list page: <2s load time (500 documents)
- Search results: <2s
- Preview: <3s

**Constraints**:
- Offline-capable (no cloud dependencies for training)
- Works with mock authentication system
- Files stored outside wwwroot for security
- Maximum file size: 25 MB
- Supported file types: PDF, Office docs, text, images

**Scale/Scope**: 
- Training scope: 100 concurrent users, 10,000 total documents
- 5 phases of implementation
- 8 user stories (3 P1, 3 P2, 2 P3)
- 67 functional requirements

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

### ✅ Compliance Status: PASSING

| Constitution Principle | Status | Evidence |
|------------------------|--------|----------|
| **I. Spec-First Development** | ✅ PASS | Spec approved (stakeholder-clarifications.md) before implementation |
| **II. Service Layer Architecture** | ✅ PASS | DocumentService contains business logic, thin Blazor UI |
| **III. Test-First (TDD)** | ✅ READY | Tests will be written before implementation per phase plan |
| **IV. Training Clarity** | ✅ PASS | Spec includes educational comments, production notes |
| **V. Security by Design** | ✅ PASS | Authorization at page/service layers, IDOR protection, defense in depth |
| **VI. Data Integrity & EF** | ✅ PASS | EF migrations, validation attributes, async operations |
| **VII. Simplicity & YAGNI** | ✅ PASS | Spec only includes required features, no speculative additions |

### Technology Stack Compliance

| Requirement | Specified | Compliant |
|-------------|-----------|-----------|
| Target Framework | .NET 9.0 | ✅ Yes |
| UI Framework | Blazor Server | ✅ Yes |
| ORM | EF Core 9.0 | ✅ Yes |
| Database | SQL Server | ✅ Yes |
| Testing | xUnit | ✅ Ready |

### No Complexity Violations

All implementation aligns with Constitution principles. No justifications required.

## Project Structure

### Documentation (this feature)

```text
specs/001-document-management/
├── plan.md                          # This file - implementation plan
├── spec.md                          # Approved specification
├── stakeholder-clarifications.md    # Resolved Q&A
└── tasks.md                         # [Will be created with /speckit.tasks]
```

### Source Code (repository root)

```text
ContosoDashboard/
├── Models/
│   └── Document.cs                  # NEW - Document entity
│
├── Data/
│   └── ApplicationDbContext.cs      # MODIFY - Add DbSet<Document>
│
├── Services/
│   ├── IFileStorageService.cs       # NEW - File storage abstraction
│   ├── LocalFileStorageService.cs   # NEW - Local filesystem implementation
│   └── DocumentService.cs           # NEW - Document business logic
│
├── Pages/
│   ├── Documents.razor              # NEW - Main documents page (list, upload, search)
│   ├── Documents.razor.cs           # NEW - Code-behind for documents page
│   ├── Index.razor                  # MODIFY - Add recent documents widget
│   └── ProjectDetails.razor         # MODIFY - Add documents section
│
├── Shared/
│   └── RecentDocumentsWidget.razor  # NEW - Dashboard widget component
│
├── Controllers/                     # NEW folder
│   └── DocumentsController.cs       # NEW - File download endpoint
│
├── AppData/                         # NEW folder (gitignored)
│   └── uploads/                     # NEW - Local file storage
│       └── {userId}/
│           ├── personal/
│           └── {projectId}/
│
├── Migrations/                      # EF migrations folder
│   └── [timestamp]_AddDocuments.cs  # NEW - Document table migration
│
└── Program.cs                       # MODIFY - Register new services

Tests/ (to be created)
├── ContosoDashboard.Tests.csproj    # NEW - Test project
└── Services/
    ├── DocumentServiceTests.cs      # NEW - DocumentService unit tests
    └── LocalFileStorageServiceTests.cs  # NEW - Storage service tests
```

**Structure Decision**: Single Blazor Server project following existing ContosoDashboard architecture. All new code follows established patterns (Models, Services, Pages structure). File storage abstraction (IFileStorageService) enables future cloud migration without architectural changes.

## Implementation Phases

### Phase 0: Prerequisites ✅ (COMPLETED)
- ✅ Specification approved with clarifications
- ✅ Constitution compliance verified
- ✅ Plan created

### Phase 1: Foundation (P1 - Stories 1-2)
**Goal**: Basic document upload and download functionality

**Test-Driven Sequence**:

1. **Create Document Entity**
   - Write: `Document.cs` with all properties and validation attributes
   - Test: Entity instantiation, validation attribute enforcement
   
2. **Add EF Migration**
   - Write: `Add-Migration AddDocuments`
   - Test: Migration script correctness, FK constraints
   - Execute: `Update-Database`

3. **Create IFileStorageService Interface (TDD)**
   - Test: Mock interface behavior expectations
   - Write: Interface definition with XML docs
   
4. **Implement LocalFileStorageService (TDD)**
   - Test: File upload to local path
   - Test: File download from local path
   - Test: File deletion
   - Test: Path generation (userId/projectId pattern)
   - Test: Directory creation if not exists
   - Implement: LocalFileStorageService passing all tests

5. **Create DocumentService.UploadAsync() (TDD)**
   - Test: Valid file upload succeeds, creates DB record
   - Test: Oversized file (>25 MB) rejected
   - Test: Invalid file type rejected
   - Test: User authorization for project uploads
   - Test: File saved before DB record (no orphans)
   - Test: Title length validation (<=200 chars)
   - Implement: DocumentService.UploadAsync() passing all tests

6. **Create DocumentService.GetUserDocumentsAsync() (TDD)**
   - Test: Returns only current user's documents
   - Test: Excludes other users' documents
   - Test: Includes documents with/without projects
   - Implement: GetUserDocumentsAsync() passing all tests

7. **Create DocumentService.DownloadAsync() (TDD)**
   - Test: Authorized user can download own document
   - Test: Unauthorized user cannot download (throws exception)
   - Test: Returns correct file stream
   - Implement: DownloadAsync() passing all tests

8. **Create Documents.razor Page (TDD-informed)**
   - Test: Upload form validation (client-side via Bunit if time)
   - Implement: Upload form with title, category, file input
   - Implement: Character counter for title (180/200)
   - Implement: Document list table with download buttons
   - Implement: Error/success message display

9. **Create DocumentsController (TDD)**
   - Test: Download endpoint requires authorization
   - Test: Returns file with correct content-type
   - Test: Returns 404 for non-existent document
   - Test: Returns 403 for unauthorized access
   - Implement: GET /api/documents/{id}/download endpoint

10. **Register Services in Program.cs**
    - Add: `services.AddScoped<IFileStorageService, LocalFileStorageService>()`
    - Add: `services.AddScoped<DocumentService>()`
    - Configure: Request size limits for 25 MB uploads

**Deliverables**:
- Document entity with validation
- Database migration applied
- IFileStorageService + LocalFileStorageService (tested)
- DocumentService with Upload, Get, Download methods (tested)
- Documents.razor page with upload form and list
- DocumentsController for downloads
- Unit tests passing (Red-Green-Refactor completed)

**Acceptance Criteria**:
- User can upload a document with title and category
- Upload validates file size (25 MB limit) at both client and server
- User sees their uploaded documents in a list
- User can download their uploaded documents
- Files stored outside wwwroot with GUID filenames
- All FR-001 through FR-034 implemented and tested

---

### Phase 2: Project Integration (P2 - Stories 3, 7)
**Goal**: Associate documents with projects, enable team access

**Test-Driven Sequence**:

1. **Extend DocumentService for Project Documents (TDD)**
   - Test: User can upload to project they're a member of
   - Test: User cannot upload to project they're not a member of
   - Test: GetProjectDocumentsAsync returns only for members
   - Test: Non-members get empty result (or exception)
   - Test: Authorization checks current membership (not historical)
   - Implement: Project-aware upload and retrieval methods

2. **Update Documents.razor Page**
   - Test: Project dropdown shows only user's projects
   - Implement: Project selection in upload form
   - Implement: Filter documents by project

3. **Update ProjectDetails.razor Page**
   - Test: Documents section appears for project members
   - Test: Documents section hidden for non-members
   - Implement: Documents section with project document list
   - Implement: Upload button for project context

4. **Project Deletion Validation (TDD)**
   - Test: Cannot delete project with documents
   - Test: Error message shown correctly
   - Implement: Validation in ProjectService.DeleteAsync()

**Deliverables**:
- Project association in upload form
- Project documents visible to all team members
- Project deletion blocked if documents exist
- FR-035 through FR-039b implemented and tested

**Acceptance Criteria**:
- User can upload documents to projects they belong to
- All project members see project documents
- Cannot delete project with documents
- Authorization enforced (non-members cannot access)

---

### Phase 3: Search and Filter (P2 - Story 4)
**Goal**: Enable users to find documents quickly

**Test-Driven Sequence**:

1. **Implement DocumentService.SearchDocumentsAsync() (TDD)**
   - Test: Search by title (partial match)
   - Test: Search by description
   - Test: Search by tags
   - Test: Search respects authorization (user's + project docs only)
   - Test: Returns results under 2 seconds (performance test)
   - Implement: SearchDocumentsAsync()

2. **Implement Filtering (TDD)**
   - Test: Filter by category
   - Test: Filter by project
   - Test: Filter by date range
   - Test: Combined filters work correctly
   - Implement: Filter parameters in GetUserDocumentsAsync()

3. **Implement Sorting (TDD)**
   - Test: Sort by title (asc/desc)
   - Test: Sort by upload date (asc/desc)
   - Test: Sort by file size (asc/desc)
   - Test: Sort by category
   - Implement: Sorting logic

4. **Update Documents.razor UI**
   - Implement: Search bar with real-time search
   - Implement: Category filter dropdown
   - Implement: Project filter dropdown
   - Implement: Date range filter
   - Implement: Sort column headers (clickable)

**Deliverables**:
- Full-text search across title, description, tags
- Filter by category, project, date range
- Sort by multiple columns
- FR-040 through FR-045 implemented and tested

**Acceptance Criteria**:
- Search returns results within 2 seconds
- Filters work independently and in combination
- Only authorized documents appear in results
- UI provides clear feedback

---

### Phase 4: Document Management (P3 - Stories 5-6)
**Goal**: Allow users to edit and delete documents

**Test-Driven Sequence**:

1. **Implement DocumentService.UpdateMetadataAsync() (TDD)**
   - Test: Owner can edit their document metadata
   - Test: Non-owner cannot edit (throws exception)
   - Test: Project Manager can edit project documents
   - Test: Original upload date preserved
   - Test: Title length validation applied
   - Implement: UpdateMetadataAsync()

2. **Implement DocumentService.DeleteAsync() (TDD)**
   - Test: Owner can delete their document
   - Test: Non-owner cannot delete (throws exception)
   - Test: Project Manager can delete project documents
   - Test: Administrator can delete any document
   - Test: File removed from filesystem
   - Test: Database record removed
   - Implement: DeleteAsync()

3. **Update Documents.razor UI**
   - Implement: Edit button (conditional - only for owners)
   - Implement: Edit modal with form
   - Implement: Delete button (conditional - only for owners)
   - Implement: Delete confirmation dialog
   - Implement: Success/error feedback

**Deliverables**:
- Edit metadata functionality with authorization
- Delete functionality with confirmation and authorization
- FR-046 through FR-054 implemented and tested

**Acceptance Criteria**:
- Only authorized users see edit/delete buttons
- Edit preserves original upload info
- Delete removes both DB record and file
- Confirmation prevents accidental deletion

---

### Phase 5: Dashboard Integration (P3 - Story 8)
**Goal**: Display recent documents on dashboard

**Test-Driven Sequence**:

1. **Implement DocumentService.GetRecentDocumentsAsync() (TDD)**
   - Test: Returns 5 most recent documents for user
   - Test: Ordered by upload date descending
   - Test: Only includes user's accessible documents
   - Implement: GetRecentDocumentsAsync()

2. **Create RecentDocumentsWidget Component**
   - Test: Displays documents correctly
   - Test: Shows "No documents" message when empty
   - Implement: RecentDocumentsWidget.razor
   - Implement: Click handling to navigate to Documents page

3. **Update Index.razor (Dashboard)**
   - Implement: Add RecentDocumentsWidget component
   - Implement: Layout/styling to fit dashboard

**Deliverables**:
- Recent documents widget on dashboard
- FR-055 through FR-058 implemented and tested

**Acceptance Criteria**:
- Dashboard shows 5 most recent documents
- Widget handles empty state gracefully
- Clicking document navigates to full page

---

## Testing Strategy

### Unit Tests (Required per Constitution)
All service methods have comprehensive unit tests covering:
- ✅ Happy path scenarios
- ✅ Validation failures
- ✅ Authorization checks
- ✅ Edge cases
- ✅ Error handling

**Test Coverage Goals**:
- DocumentService: 100% method coverage
- LocalFileStorageService: 100% method coverage
- Authorization logic: All scenarios tested

### Integration Tests (Recommended)
- End-to-end upload workflow
- End-to-end download workflow
- Authorization boundary testing
- File system persistence verification

### Manual Testing Checklist
- [ ] Upload various file types (PDF, Word, Excel, images, text)
- [ ] Upload files at size limits (1 MB, 24 MB, 25 MB, 26 MB)
- [ ] Verify IDOR protection (attempt to access other user's documents)
- [ ] Test all search and filter combinations
- [ ] Verify project access controls
- [ ] Test edit and delete with different user roles
- [ ] Verify preview works for PDF and images
- [ ] Test performance with 500 documents
- [ ] Verify navigation and UI responsiveness

## Configuration Changes

### appsettings.json
```json
{
  "FileStorage": {
    "LocalPath": "AppData/uploads",
    "MaxFileSizeBytes": 26214400
  }
}
```

### .gitignore additions
```
# Document uploads (local storage)
AppData/
```

### Database Connection String
No changes - uses existing DefaultConnection for LocalDB

## Security Considerations

### Implemented Controls
1. **IDOR Protection**: DocumentId not exposed in URLs, authorization checks on all operations
2. **Path Traversal Prevention**: GUID-based filenames, no user input in paths
3. **File Type Validation**: Whitelist of allowed extensions
4. **Size Limits**: 25 MB enforced at client and server
5. **Authorization Layers**: Page-level [Authorize], service-level checks
6. **Claims-Based**: Uses existing mock authentication claims
7. **Files Outside wwwroot**: Served via authorized controller endpoint

### Production Requirements (Documented in Code)
- Virus/malware scanning before storage
- Azure Blob Storage for scalability
- Audit logging for compliance
- Rate limiting on uploads
- MFA for sensitive documents

## Migration Path to Azure

### Phase 6 (Future): Azure Blob Storage Migration

**No Code Changes Required** (by design):
1. Implement `AzureBlobStorageService : IFileStorageService`
2. Update `Program.cs` DI registration based on config
3. Update `appsettings.json` with Azure connection string
4. Deploy - all business logic unchanged

**Zero Impact**:
- Controllers: No changes (use IFileStorageService)
- Services: No changes (depend on interface)
- UI: No changes (unaware of storage implementation)
- Database: No schema changes (FilePath works for blob names)

## Risk Assessment

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| File system fills up | Low (training) | Medium | Document in code: Production uses blob storage with quotas |
| Large file upload timeout | Low | Low | Default timeout adequate for LAN, document limitation |
| Concurrent upload conflicts | Very Low | Low | GUID filenames prevent collisions |
| Orphaned files if DB insert fails | Medium | Low | Save file AFTER DB record succeeds (reversed order from common pattern) |
| User confusion with 200 char limit | Low | Low | Character counter provides feedback |
| Performance with 10k documents | Medium | Medium | Pagination (future enhancement if needed) |

## Success Metrics

Track these to validate implementation:

- [ ] All 67 functional requirements implemented
- [ ] All unit tests passing (100% pass rate)
- [ ] Upload/download preserves file integrity (binary identical)
- [ ] Authorization prevents unauthorized access (verified via security testing)
- [ ] Performance targets met (2s list, 2s search, 3s preview)
- [ ] No IDOR vulnerabilities (verified via penetration testing)
- [ ] Code includes educational comments per Constitution
- [ ] Interface abstraction verified (can swap storage via config)

## Timeline Estimate

| Phase | Effort | Dependencies | Deliverable |
|-------|--------|--------------|-------------|
| Phase 1 | 12-16 hours | None | Upload/download working |
| Phase 2 | 6-8 hours | Phase 1 | Project integration |
| Phase 3 | 6-8 hours | Phase 1 | Search/filter |
| Phase 4 | 4-6 hours | Phase 1 | Edit/delete |
| Phase 5 | 2-4 hours | Phase 1 | Dashboard widget |
| **Total** | **30-42 hours** | | **Full feature** |

*Note: Timeline assumes Test-Driven Development workflow (Red-Green-Refactor). Writing tests first adds ~40% time but ensures quality and maintainability.*

## Next Steps

1. ✅ Specification approved
2. ✅ Plan created
3. ⏭️ **BEGIN Phase 1**: Create Document entity
4. ⏭️ Write unit tests for DocumentService.UploadAsync()
5. ⏭️ Get tests approved (Red phase confirmation)
6. ⏭️ Implement to make tests pass (Green phase)

**Ready to begin TDD implementation per Constitution principles.**

---

**Plan Version**: 1.0  
**Last Updated**: 2026-02-25  
**Next Review**: After Phase 1 completion
