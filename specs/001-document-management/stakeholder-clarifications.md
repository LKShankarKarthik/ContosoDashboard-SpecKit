# Stakeholder Clarification Questions
**Feature**: Document Upload and Management  
**Spec**: 001-document-management  
**Date Submitted**: 2026-02-25  
**Status**: ✅ Approved and Incorporated into Spec

---

## Questions Requiring Clarification

### Q1: File Size Limit (25 MB) - Rationale and Constraints

**Question**: Why is the file size limit set to 25 MB? Is this based on:
- Technical constraints (server memory, timeout limits)?
- Typical document sizes in your business context?
- Arbitrary limit that can be adjusted?

**Context**: Understanding the rationale helps us determine if this should be:
- A hard-coded constant for training simplicity
- A configurable setting in appsettings.json
- Different limits for different user roles (e.g., Admins get higher limits)

**Related Requirements**: FR-003, NFR-001

**Suggested Answer for Training Context**:
> **STAKEHOLDER RESPONSE**: The 25 MB limit is based on typical business document sizes. Most PDFs, Office documents, and presentations are under 10 MB. The 25 MB limit provides headroom while preventing abuse. For training purposes, use a hard-coded constant in the service layer - no need for configuration. This simplifies the implementation and is sufficient for learning objectives.

---

### Q2: File Size Limit - Enforcement Strategy

**Question**: How should the 25 MB limit be enforced?
- Client-side only (Blazor InputFile `maxAllowedSize` parameter)?
- Server-side only (ASP.NET Core request size limits)?
- Both layers (defense in depth)?

**Context**: 
- **Client-side**: Prevents unnecessary uploads, better UX, but can be bypassed
- **Server-side**: Security boundary, cannot be bypassed, but worse UX if rejected after upload
- **Both**: Best practice but more complex

**Additional Considerations**:
- Is the limit on the file size before transmission or after?
- What if compression affects the size?

**Related Requirements**: FR-003, FR-005, NFR-007

**Suggested Answer for Training Context**:
> **STAKEHOLDER RESPONSE**: Enforce at **both layers** to demonstrate defense-in-depth security principle:
> - Client-side: Use `InputFile` with `maxAllowedSize: 26214400` (25 MB in bytes) for immediate feedback
> - Server-side: Validate stream length in `DocumentService.UploadAsync()` before saving
> - Limit applies to the file size as received (before any server-side processing)
> - Add educational comments explaining why both layers are important
>
> **Training Value**: Students learn that client-side validation is for UX, server-side validation is for security.

---

### Q3: Network Timeout and Upload Reliability

**Question**: What happens if network timeout occurs before a 25 MB upload completes?
- Should there be a configurable timeout separate from file size limit?
- What's the expected minimum upload speed?
- Should uploads be resumable for large files?

**Context**: A 25 MB file on a slow connection (1 Mbps) could take 3+ minutes to upload.

**Related Requirements**: NFR-001

**Suggested Answer for Training Context**:
> **STAKEHOLDER RESPONSE**: For training purposes, keep it simple:
> - Use default ASP.NET Core timeout (no custom configuration needed)
> - No resumable uploads (out of scope - adds significant complexity)
> - Assume training environment has adequate network (LAN or local)
> - If upload fails, user sees error message and can retry
> - Document in code comments: "Production would implement resumable uploads for files >10 MB"
>
> **Training Value**: Students understand the limitation and know what production would need, without implementing complex chunked upload logic.

---

### Q4: Project Deletion and Document Fate

**Question**: What happens to documents when a project is deleted?

**Options**:
1. **Cascade delete**: All project documents deleted permanently
2. **Transfer to personal**: Documents become personal files of original uploader
3. **Block deletion**: Cannot delete project if it has documents (must delete docs first)
4. **Soft delete**: Project and documents marked as deleted but retained

**Context**: This affects the database foreign key relationship and business logic.

**Related Requirements**: FR-052, Document entity design

**Suggested Answer for Training Context**:
> **STAKEHOLDER RESPONSE**: For training, use **Option 3: Block deletion**.
> - When attempting to delete a project, check if it has associated documents
> - If documents exist, show error: "Cannot delete project with documents. Delete all project documents first."
> - This is the simplest approach and teaches validation logic
> - Add FK constraint with `ON DELETE NO ACTION` (SQL Server default)
> - Document in code: "Production might implement soft delete or transfer to personal"
>
> **Training Value**: Students implement validation checks and understand referential integrity.

---

### Q5: Document Title Length Limit

**Question**: What is the maximum length for document titles?

**Context**: Original requirements don't specify. Common limits:
- 100 chars: Short, Twitter-like
- 200 chars: Moderate, allows descriptive titles
- 500 chars: Very long, rarely needed

**Current Spec Suggestion**: 200 characters

**Related Requirements**: FR-007, Document entity

**Suggested Answer for Training Context**:
> **STAKEHOLDER RESPONSE**: Use **200 character limit** for document titles.
> - Sufficient for descriptive titles like "Q4 2025 Financial Report for Website Redesign Project - Final Version"
> - Add validation attribute: `[MaxLength(200)]` on entity
> - Add client-side validation on Blazor form: `maxlength="200"`
> - Display character counter: "180/200" to help users
>
> **Training Value**: Students implement validation at multiple layers (entity, form).

---

### Q6: Role Changes and Document Access

**Question**: What happens if a user's role or project membership changes?

**Scenarios**:
1. User uploads document to Project A, then leaves Project A - can they still access their uploaded document?
2. User is demoted from Project Manager to Employee - do they lose ability to delete project documents?
3. User account is deactivated - what happens to their documents?

**Context**: Affects authorization logic in DocumentService.

**Related Requirements**: FR-060, FR-061, Authorization requirements

**Suggested Answer for Training Context**:
> **STAKEHOLDER RESPONSE**: For training, use these rules:
>
> **Scenario 1 - Project Membership Loss**:
> - Users who uploaded a document to a project retain NO access after leaving the project
> - Authorization is based on current project membership, not historical ownership
> - Rational: Project documents belong to the project, not individuals
> - Example: Employee uploads doc to Project A, leaves project, can no longer view/download it
>
> **Scenario 2 - Role Change**:
> - Role permissions are evaluated in real-time based on current role
> - If demoted from Project Manager to Employee, lose delete privileges immediately
> - This follows principle of least privilege
>
> **Scenario 3 - Account Deactivation**:
> - Out of scope for this feature (no user deactivation in training app)
> - Document in code: "Production would need to handle deactivated user scenarios"
>
> **Training Value**: Students implement authorization that checks current state, not historical state. They learn to think about access control dynamically.

---

## Summary of Stakeholder Decisions

| Question | Decision | Impact on Implementation |
|----------|----------|--------------------------|
| Q1: 25 MB Rationale | Hard-coded constant, typical business doc sizes | Use `const int MaxFileSizeBytes = 26214400;` |
| Q2: Enforcement | Both client and server (defense in depth) | Validate in InputFile AND DocumentService |
| Q3: Timeout/Resumable | Use defaults, no resumable uploads | No custom timeout logic, document limitation |
| Q4: Project Deletion | Block deletion if documents exist | Add validation check, FK with NO ACTION |
| Q5: Title Length | 200 character limit | `[MaxLength(200)]`, client validation, counter |
| Q6: Role Changes | Current state determines access, no historical | Authorization checks current membership/role |

---

## Next Steps

1. ✅ **Stakeholder**: Review and approve/modify these answers - **COMPLETED**
2. ✅ **Developer**: Update spec.md with approved answers, remove [NEEDS CLARIFICATION] tags - **COMPLETED**
3. ✅ **Developer**: Update FR requirements and entity definitions based on decisions - **COMPLETED**
4. ⏭️ **Developer**: Proceed to TDD implementation Phase 1 - **READY TO BEGIN**

---

## Approval

**Stakeholder Name**: _LKS________________________  
**Date**: _25 feb 2026________________________  
**Signature**: _________________________  

**Notes/Changes**: 
```
[Space for stakeholder to note any modifications to suggested answers]
```
