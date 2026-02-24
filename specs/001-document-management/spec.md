# Feature Specification: Document Upload and Management

**Feature Branch**: `001-document-management`  
**Created**: 2026-02-25  
**Status**: Draft  
**Input**: Stakeholder Document: "StakeholderDocs/document-upload-and-management-feature.md"

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Upload Personal Document (Priority: P1)

As an employee, I want to upload a personal document so that I can store it securely within the dashboard application I use daily.

**Why this priority**: This is the foundational capability - document upload with metadata. Without this, no other document features are possible. Delivers immediate value by allowing users to centralize their work documents.

**Independent Test**: Can be fully tested by logging in as any employee, uploading a file with title and category, and verifying it appears in "My Documents" list. No project or sharing features required.

**Acceptance Scenarios**:

1. **Given** I am logged in as an employee, **When** I navigate to the Documents page and click "Upload Document", **Then** I see a file selection dialog and upload form
2. **Given** I have selected a PDF file under 25 MB, **When** I provide a title "Q4 Report" and select category "Reports", **Then** the document uploads successfully and appears in my documents list
3. **Given** I select a file larger than 25 MB, **When** I attempt to upload, **Then** I see an error message "File size exceeds 25 MB limit"
4. **Given** I select an unsupported file type (e.g., .exe), **When** I attempt to upload, **Then** I see an error message "Unsupported file type"
5. **Given** I have uploaded a document, **When** I view my documents list, **Then** I see the document title, category, upload date, file size, and my name as uploader

---

### User Story 2 - Download and View Own Documents (Priority: P1)

As an employee, I want to download documents I've uploaded so that I can access them when needed.

**Why this priority**: Upload without download capability is incomplete. Users need to retrieve their documents. This completes the basic document lifecycle.

**Independent Test**: Can be tested by uploading a document (Story 1), then clicking download and verifying the file downloads correctly with original filename and content intact.

**Acceptance Scenarios**:

1. **Given** I have uploaded a PDF document, **When** I click the download button next to it, **Then** the file downloads to my computer with the original filename
2. **Given** I have uploaded an image file (JPEG/PNG), **When** I click preview, **Then** I see the image displayed in the browser without downloading
3. **Given** I have uploaded a PDF file, **When** I click preview, **Then** I see the PDF displayed in the browser viewer

---

### User Story 3 - Upload Document to Project (Priority: P2)

As a project team member, I want to upload documents associated with specific projects so that all team members can access project-related documents.

**Why this priority**: Extends basic upload to project context. Enables team collaboration around project documents. Requires project association but builds on P1 upload functionality.

**Independent Test**: Can be tested by logging in as a project member, uploading a document with an associated project, and verifying other project members can see it in the project documents view.

**Acceptance Scenarios**:

1. **Given** I am a member of "Website Redesign" project, **When** I upload a document and select "Website Redesign" as the associated project, **Then** the document is uploaded and linked to that project
2. **Given** I have uploaded a document to a project, **When** other team members view that project, **Then** they see the document in the project documents list
3. **Given** I upload a document without selecting a project, **When** I set the category to "Personal Files", **Then** the document is uploaded as a personal document (no project association)

---

### User Story 4 - Search and Filter Documents (Priority: P2)

As an employee with many documents, I want to search and filter my documents so that I can quickly find specific documents.

**Why this priority**: As document count grows, findability becomes critical. Enhances usability without requiring new entities or complex integrations.

**Independent Test**: Can be tested by uploading multiple documents with different titles, categories, and dates, then verifying search and filter controls return correct results.

**Acceptance Scenarios**:

1. **Given** I have uploaded 10 documents, **When** I search for "Q4 Report" in the search box, **Then** I see only documents with "Q4 Report" in the title or description
2. **Given** I have documents in multiple categories, **When** I filter by category "Reports", **Then** I see only documents in the Reports category
3. **Given** I have uploaded documents over several months, **When** I filter by date range "Last 30 days", **Then** I see only documents uploaded in the last 30 days
4. **Given** I have documents from multiple projects, **When** I filter by project "Website Redesign", **Then** I see only documents associated with that project
5. **Given** I perform a search, **When** results are returned, **Then** they appear within 2 seconds

---

### User Story 5 - Edit Document Metadata (Priority: P3)

As a document owner, I want to edit the title, description, category, and tags of documents I've uploaded so that I can keep document information accurate.

**Why this priority**: Useful for maintaining accurate metadata but not critical for initial document management. Users can work around by deleting and re-uploading if necessary.

**Independent Test**: Can be tested by uploading a document, clicking edit, changing the title and category, and verifying the changes persist.

**Acceptance Scenarios**:

1. **Given** I uploaded a document with title "Draft Report", **When** I click "Edit" and change the title to "Final Report Q4", **Then** the document title updates and shows "Final Report Q4" in the list
2. **Given** I own a document, **When** I edit the category from "Personal Files" to "Reports", **Then** the category updates and the document appears correctly when filtered by "Reports"
3. **Given** I attempt to edit a document uploaded by another user, **When** I view that document, **Then** I do not see an "Edit" button (only owners can edit)

---

### User Story 6 - Delete Own Documents (Priority: P3)

As a document owner, I want to delete documents I've uploaded so that I can remove outdated or incorrect files.

**Why this priority**: Important for data hygiene but lower priority than upload/view functionality. Users can tolerate stale documents temporarily.

**Independent Test**: Can be tested by uploading a document, clicking delete, confirming the deletion, and verifying the document no longer appears in any lists.

**Acceptance Scenarios**:

1. **Given** I uploaded a document, **When** I click "Delete" and confirm the deletion, **Then** the document is permanently removed from my documents list
2. **Given** I attempt to delete a document, **When** I click "Delete", **Then** I see a confirmation dialog asking "Are you sure you want to delete this document?"
3. **Given** I attempt to delete a document uploaded by another user (not my document), **When** I view that document, **Then** I do not see a "Delete" button (only owners can delete)

---

### User Story 7 - View Project Documents (Priority: P2)

As a project team member, I want to view all documents associated with my projects so that I can access relevant project materials.

**Why this priority**: Complements P2 Story 3 (upload to project). Enables team collaboration by making project documents accessible to all members.

**Independent Test**: Can be tested by having one user upload documents to a project, then logging in as a different project member and verifying they can see and download those documents.

**Acceptance Scenarios**:

1. **Given** I am a member of "Website Redesign" project, **When** I view that project's details page, **Then** I see a "Documents" section listing all project documents
2. **Given** I am viewing project documents, **When** the list displays, **Then** I see document title, uploader name, upload date, category, and file size for each document
3. **Given** I am viewing project documents, **When** I click download on any document, **Then** the document downloads successfully (all project members have read access)
4. **Given** I am not a member of a project, **When** I attempt to view that project's documents, **Then** I receive an "Access Denied" error (authorization check)

---

### User Story 8 - Dashboard Integration (Priority: P3)

As a dashboard user, I want to see my recent documents on the home page so that I have quick access to files I'm actively working with.

**Why this priority**: Nice-to-have convenience feature that improves user experience but isn't critical to core document management functionality.

**Independent Test**: Can be tested by uploading several documents, then navigating to the dashboard home page and verifying the 5 most recent documents appear in a widget.

**Acceptance Scenarios**:

1. **Given** I have uploaded documents, **When** I view the dashboard home page, **Then** I see a "Recent Documents" widget showing my 5 most recently uploaded documents
2. **Given** I click on a document in the "Recent Documents" widget, **When** I click the document title, **Then** I am taken to the full documents page with that document highlighted
3. **Given** I have uploaded no documents, **When** I view the dashboard home page, **Then** the "Recent Documents" widget shows a message "No documents uploaded yet"

---

### Edge Cases

- **What happens when a user uploads a document with a duplicate title?** System allows duplicate titles (different documents can have same title). DocumentId (integer) is unique identifier.
- **How does the system handle special characters in filenames?** System generates GUID-based filenames for storage, preventing path traversal. Original filename stored in metadata for display only.
- **What happens if file upload fails partway through?** If file save to disk fails, no database record is created (prevents orphaned records). User sees error message and can retry.
- **How does system handle concurrent uploads by same user?** Each upload gets unique GUID filename and timestamp, so no conflicts occur. Multiple simultaneous uploads are supported.
- **What happens when a project is deleted?** [NEEDS CLARIFICATION: Should project documents be deleted, transferred to personal, or blocked from project deletion?]
- **How does system handle very long document titles?** [NEEDS CLARIFICATION: Maximum title length not specified in requirements. Suggest 200 character limit.]
- **What happens if a user's role changes?** [NEEDS CLARIFICATION: If user loses project membership, do they retain access to documents they uploaded to that project?]

## Requirements *(mandatory)*

### Functional Requirements

**Document Upload (P1)**

- **FR-001**: System MUST allow authenticated users to upload documents via file selection dialog
- **FR-002**: System MUST support file types: PDF, Microsoft Office (Word, Excel, PowerPoint), text files, images (JPEG, PNG)
- **FR-003**: System MUST enforce maximum file size limit of 25 MB per file
- **FR-004**: System MUST reject unsupported file types with clear error message
- **FR-005**: System MUST reject oversized files with clear error message
- **FR-006**: System MUST display upload progress indicator during file upload
- **FR-007**: System MUST require document title (required field)
- **FR-008**: System MUST require category selection from: Project Documents, Team Resources, Personal Files, Reports, Presentations, Other
- **FR-009**: System MUST allow optional description field (text, multi-line)
- **FR-010**: System MUST allow optional project association (dropdown of user's projects)
- **FR-011**: System MUST allow optional tags field (comma-separated custom tags)
- **FR-012**: System MUST automatically capture upload date/time (UTC)
- **FR-013**: System MUST automatically capture uploader user ID and name
- **FR-014**: System MUST automatically capture file size in bytes
- **FR-015**: System MUST automatically capture file MIME type (e.g., "application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document")
- **FR-016**: System MUST store files outside web-accessible directories (`wwwroot`) for security
- **FR-017**: System MUST generate unique GUID-based filenames to prevent path traversal attacks
- **FR-018**: System MUST use file path pattern: `{userId}/{projectId or "personal"}/{guid}.{extension}`
- **FR-019**: System MUST store file to disk BEFORE creating database record (prevents orphaned records)

**Document Storage (P1)**

- **FR-020**: System MUST implement `IFileStorageService` interface for storage abstraction
- **FR-021**: System MUST use `LocalFileStorageService` implementation for training environment
- **FR-022**: System MUST store files in dedicated directory outside `wwwroot` (e.g., `AppData/uploads`)
- **FR-023**: System MUST store metadata in `Document` entity: DocumentId (int), Title, Description, Category, FilePath, FileName, FileSize, FileType, UploadedDate, UploadedById, ProjectId (nullable), Tags

**Document Viewing and Browsing (P1)**

- **FR-024**: Users MUST be able to view list of all documents they have uploaded
- **FR-025**: Document list MUST display: title, category, upload date, file size, associated project
- **FR-026**: Users MUST be able to sort documents by: title, upload date, category, file size
- **FR-027**: Users MUST be able to filter documents by: category, associated project, date range
- **FR-028**: Document list page MUST load within 2 seconds for up to 500 documents

**Document Download and Preview (P1)**

- **FR-029**: Users MUST be able to download any document they have access to
- **FR-030**: Downloaded files MUST use original filename (stored in FileName field)
- **FR-031**: System MUST provide preview capability for PDF files (browser viewer)
- **FR-032**: System MUST provide preview capability for image files (JPEG, PNG)
- **FR-033**: Document preview MUST load within 3 seconds
- **FR-034**: Download endpoint MUST enforce authorization (prevent unauthorized access)

**Project Documents (P2)**

- **FR-035**: Project team members MUST be able to upload documents associated with their projects
- **FR-036**: All project team members MUST be able to view documents associated with their projects
- **FR-037**: Project details page MUST display "Documents" section with all project documents
- **FR-038**: Users who are not project members MUST NOT be able to view that project's documents
- **FR-039**: Project Managers MUST be able to upload documents to their projects

**Search and Filter (P2)**

- **FR-040**: Users MUST be able to search documents by: title, description, tags, uploader name
- **FR-041**: Search MUST return results within 2 seconds
- **FR-042**: Search results MUST respect authorization (users see only documents they can access)
- **FR-043**: Users MUST be able to filter documents by category from dropdown
- **FR-044**: Users MUST be able to filter documents by associated project from dropdown
- **FR-045**: Users MUST be able to filter documents by date range (last 7 days, 30 days, 90 days, custom)

**Document Editing (P3)**

- **FR-046**: Document owners MUST be able to edit document metadata: title, description, category, tags
- **FR-047**: Users MUST NOT be able to edit documents they did not upload (except Project Managers for project documents)
- **FR-048**: Edited metadata MUST persist to database immediately
- **FR-049**: Edited documents MUST retain original upload date and uploader

**Document Deletion (P3)**

- **FR-050**: Document owners MUST be able to delete documents they uploaded
- **FR-051**: System MUST prompt for confirmation before permanent deletion
- **FR-052**: Deleted documents MUST be permanently removed from database and filesystem
- **FR-053**: Project Managers MUST be able to delete any document in their projects
- **FR-054**: Administrators MUST be able to delete any document

**Dashboard Integration (P3)**

- **FR-055**: Dashboard home page MUST display "Recent Documents" widget
- **FR-056**: "Recent Documents" widget MUST show 5 most recently uploaded documents by current user
- **FR-057**: Widget MUST display document title, upload date, category
- **FR-058**: Clicking document title in widget MUST navigate to full documents page

**Authorization and Security**

- **FR-059**: All document operations MUST require authenticated user
- **FR-060**: Users MUST only see documents they uploaded OR documents from projects they are members of OR documents shared with them
- **FR-061**: Download endpoint MUST validate user has access before serving file
- **FR-062**: System MUST prevent Insecure Direct Object Reference (IDOR) attacks
- **FR-063**: File paths MUST never be exposed to client (use DocumentId for all client operations)
- **FR-064**: System MUST validate file extensions against whitelist before saving
- **FR-065**: System MUST never use user-supplied filenames directly in file system operations

### Key Entities

**Document Entity**

Represents an uploaded document and its metadata.

- **DocumentId**: Integer, primary key, auto-increment (consistent with User and Project keys)
- **Title**: String (200 max), required - user-provided document title
- **Description**: String (1000 max), optional - user-provided description
- **Category**: String (50 max), required - one of predefined categories (Project Documents, Team Resources, Personal Files, Reports, Presentations, Other)
- **FilePath**: String (500 max), required - relative path where file is stored on disk (e.g., "1/5/a3d2f4e1-b234-4c56-8d90-123456789abc.pdf")
- **FileName**: String (255 max), required - original filename for download (e.g., "Q4 Report.pdf")
- **FileSize**: Long, required - file size in bytes
- **FileType**: String (255 max), required - MIME type (e.g., "application/pdf", "application/vnd.openxmlformats-officedocument.wordprocessingml.document")
- **UploadedDate**: DateTime, required - UTC timestamp when document was uploaded
- **UploadedById**: Integer, required - foreign key to User entity
- **ProjectId**: Integer, nullable - foreign key to Project entity if document is associated with project
- **Tags**: String (500 max), nullable - comma-separated tags for search
- **Relationships**:
  - Many-to-One with User (UploadedBy navigation property)
  - Many-to-One with Project (optional, Project navigation property)

**IFileStorageService Interface**

Abstracts file storage operations for future cloud migration.

- **UploadAsync(Stream fileStream, string filePath, string contentType)**: Returns Task<bool> - saves file to storage
- **DeleteAsync(string filePath)**: Returns Task<bool> - deletes file from storage
- **DownloadAsync(string filePath)**: Returns Task<Stream> - retrieves file stream for download
- **GetFilePathAsync(int userId, int? projectId, string fileName)**: Returns Task<string> - generates unique file path following pattern `{userId}/{projectId or "personal"}/{guid}.{extension}`

**LocalFileStorageService Implementation**

Training implementation using local filesystem.

- Stores files in `AppData/uploads/` directory relative to application root
- Uses `System.IO.File` and `System.IO.Directory` for operations
- Creates user and project subdirectories as needed
- Validates directory traversal attacks

### Non-Functional Requirements

**Performance**

- **NFR-001**: Document upload must complete within 30 seconds for 25 MB file on typical network
- **NFR-002**: Document list pages must load within 2 seconds for up to 500 documents
- **NFR-003**: Document search must return results within 2 seconds
- **NFR-004**: Document preview must load within 3 seconds

**Scalability**

- **NFR-005**: System must support up to 100 concurrent users uploading documents
- **NFR-006**: System must support up to 10,000 total documents across all users

**Security**

- **NFR-007**: All document files must be stored outside web-accessible directories
- **NFR-008**: All document download endpoints must enforce authorization
- **NFR-009**: System must prevent path traversal attacks via GUID-based filenames
- **NFR-010**: System must validate file types against whitelist
- **NFR-011**: System must use claims-based authorization consistent with existing authentication

**Usability**

- **NFR-012**: Document upload must require no more than 3 clicks
- **NFR-013**: Error messages must clearly explain what went wrong and how to fix it
- **NFR-014**: Upload progress indicator must provide visual feedback

**Maintainability**

- **NFR-015**: Storage implementation must be swappable via dependency injection
- **NFR-016**: Business logic must reside in service layer (DocumentService)
- **NFR-017**: UI components must be thin presentation layer delegating to services
- **NFR-018**: Code must include educational comments explaining training simplifications

**Training Constraints**

- **NFR-019**: Feature must work offline without cloud services
- **NFR-020**: Feature must use local filesystem storage only
- **NFR-021**: Feature must work with existing mock authentication system
- **NFR-022**: Feature must not require external dependencies (virus scanning, etc.)

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: Users can upload a document with title and category in under 30 seconds (from clicking "Upload" to seeing success message)
- **SC-002**: Users can locate a specific document via search in under 10 seconds (from starting search to clicking result)
- **SC-003**: System successfully enforces authorization - users cannot access documents they don't have permission to view (verified via manual testing of unauthorized access attempts)
- **SC-004**: All unit tests pass for DocumentService methods (100% pass rate)
- **SC-005**: Document upload/download cycle preserves file integrity (uploaded file binary-identical to downloaded file for sample files)
- **SC-006**: System prevents IDOR attacks - users cannot download arbitrary documents by manipulating DocumentId parameter (verified via security testing)
- **SC-007**: Document list page loads in < 2 seconds with 500 documents (measured via browser developer tools)
- **SC-008**: Interface abstraction allows storage implementation swap via configuration change only (no code changes to controllers, services, or UI)

### Training Objectives Met

- **SC-009**: Students successfully implement TDD workflow: write tests → tests fail → implement → tests pass
- **SC-010**: Students demonstrate service layer architecture: business logic in DocumentService, thin UI layer in Blazor pages
- **SC-011**: Students implement proper authorization checks at multiple layers (page, controller, service)
- **SC-012**: Students use Entity Framework migrations for all database schema changes
- **SC-013**: Students implement interface abstraction preparing for cloud migration (IFileStorageService)
- **SC-014**: Code includes educational comments explaining production requirements (e.g., "In production, implement virus scanning before storage")

### Business Value Delivered

- **SC-015**: Users can upload, organize, and retrieve documents within centralized dashboard application
- **SC-016**: Project teams can share documents associated with specific projects
- **SC-017**: Document organization (categories, projects, tags, search) enables fast document retrieval
- **SC-018**: Authorization controls prevent unauthorized document access
- **SC-019**: Architecture supports future Azure migration without business logic changes

## Technical Notes

### Database Schema

**Document Table**
```sql
CREATE TABLE Documents (
    DocumentId INT PRIMARY KEY IDENTITY(1,1),
    Title NVARCHAR(200) NOT NULL,
    Description NVARCHAR(1000) NULL,
    Category NVARCHAR(50) NOT NULL,
    FilePath NVARCHAR(500) NOT NULL,
    FileName NVARCHAR(255) NOT NULL,
    FileSize BIGINT NOT NULL,
    FileType NVARCHAR(255) NOT NULL,
    UploadedDate DATETIME2 NOT NULL,
    UploadedById INT NOT NULL,
    ProjectId INT NULL,
    Tags NVARCHAR(500) NULL,
    CONSTRAINT FK_Documents_Users FOREIGN KEY (UploadedById) REFERENCES Users(UserId),
    CONSTRAINT FK_Documents_Projects FOREIGN KEY (ProjectId) REFERENCES Projects(ProjectId)
);

CREATE INDEX IX_Documents_UploadedById ON Documents(UploadedById);
CREATE INDEX IX_Documents_ProjectId ON Documents(ProjectId);
CREATE INDEX IX_Documents_Category ON Documents(Category);
CREATE INDEX IX_Documents_UploadedDate ON Documents(UploadedDate);
```

### Implementation Sequence

1. **Phase 1 - Foundation (P1 Stories 1-2)**
   - Create Document entity and DbContext migration
   - Implement IFileStorageService interface
   - Implement LocalFileStorageService
   - Implement DocumentService.UploadAsync() with validation
   - Create Documents.razor page with upload form
   - Implement DocumentService.GetUserDocumentsAsync()
   - Create document list view with download links
   - Write and pass unit tests for upload and retrieval

2. **Phase 2 - Project Integration (P2 Stories 3, 7)**
   - Add project association to upload form
   - Implement DocumentService.GetProjectDocumentsAsync()
   - Add Documents section to ProjectDetails.razor page
   - Implement authorization checks for project document access
   - Write and pass unit tests for project document scenarios

3. **Phase 3 - Search and Filter (P2 Story 4)**
   - Implement DocumentService.SearchDocumentsAsync()
   - Add search and filter controls to Documents.razor
   - Implement sorting functionality
   - Write and pass unit tests for search/filter logic

4. **Phase 4 - Document Management (P3 Stories 5-6)**
   - Implement DocumentService.UpdateMetadataAsync()
   - Implement DocumentService.DeleteAsync()
   - Add edit and delete buttons to document list
   - Add confirmation dialogs
   - Write and pass unit tests for edit/delete operations

5. **Phase 5 - Dashboard Integration (P3 Story 8)**
   - Implement DocumentService.GetRecentDocumentsAsync()
   - Create RecentDocuments.razor component
   - Add component to dashboard Index.razor
   - Write and pass unit tests for recent documents logic

### Blazor Implementation Notes

**File Upload Component Pattern**
```csharp
private IBrowserFile? SelectedFile;

private async Task HandleFileSelected(InputFileChangeEventArgs e)
{
    SelectedFile = e.File;
}

private async Task UploadDocument()
{
    if (SelectedFile == null) return;
    
    // Extract metadata BEFORE opening stream (prevents disposal issues)
    var fileName = SelectedFile.Name;
    var fileSize = SelectedFile.Size;
    var contentType = SelectedFile.ContentType;
    
    // Copy to MemoryStream to prevent disposal issues
    using var memoryStream = new MemoryStream();
    using (var fileStream = SelectedFile.OpenReadStream(maxAllowedSize: 26214400)) // 25 MB
    {
        await fileStream.CopyToAsync(memoryStream);
    }
    memoryStream.Position = 0;
    
    // Clear reference to prevent reuse
    SelectedFile = null;
    
    // Call service
    await DocumentService.UploadAsync(
        memoryStream, 
        fileName, 
        contentType, 
        title, 
        category, 
        description, 
        projectId, 
        tags);
    
    StateHasChanged();
}
```

**Authorization Claims Required**
- NameIdentifier (user ID)
- Name (display name)
- Email
- Role (Employee, Team Lead, Project Manager, Administrator)
- Department (for team-based features)

### Testing Strategy

**Unit Tests (DocumentService)**
- UploadAsync: valid file, oversized file, invalid type, unauthorized project
- GetUserDocumentsAsync: returns only user's documents
- GetProjectDocumentsAsync: returns only project member documents, rejects non-members
- SearchDocumentsAsync: searches title/description/tags, respects authorization
- UpdateMetadataAsync: owner can edit, non-owner cannot
- DeleteAsync: owner can delete, non-owner cannot, file removed from disk

**Integration Tests**
- End-to-end upload: form → service → database → filesystem
- End-to-end download: request → authorization → file stream → response
- Authorization enforcement: attempt unauthorized access returns 403

**Manual Testing**
- Upload various file types and sizes
- Verify preview works for PDF and images
- Test search and filter combinations
- Verify authorization boundaries
- Test concurrent uploads

### Out of Scope

- Real-time collaborative editing
- Document version history
- Document approval workflows
- Integration with external systems (SharePoint, OneDrive)
- Mobile app support
- Document templates
- Storage quotas
- Soft delete/trash functionality
- Virus/malware scanning (noted in comments for production)
- Azure Blob Storage implementation (interface prepared, implementation deferred)

---

**Next Steps**: Once this specification is approved, proceed with Phase 1 implementation using Test-Driven Development methodology per ContosoDashboard Constitution principles.
