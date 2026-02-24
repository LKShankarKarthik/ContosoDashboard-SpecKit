# ContosoDashboard Constitution

## Core Principles

### I. Spec-First Development (NON-NEGOTIABLE)
All features begin with stakeholder documentation that defines business requirements and user needs. A complete specification must be written and approved before any implementation begins. Specifications must include:
- Business context and problem statement
- Target users and their roles
- Core requirements with acceptance criteria
- Data models and validation rules
- Security and access control requirements

The specification is the source of truth. Code implements the spec, not the other way around.

### II. Service Layer Architecture
All business logic resides in service classes within the `Services/` folder. Services are:
- Self-contained with clear, single responsibilities
- Independently testable without UI dependencies
- Registered in the DI container
- Injected into Razor Pages/Components via constructor injection

Pages and components are thin presentation layers that delegate to services. No business logic in `.razor` or `.cshtml` files.

### III. Test-First Development (NON-NEGOTIABLE)
Test-Driven Development is mandatory for all features:
1. Write unit tests for service methods based on specification requirements
2. Present tests to stakeholder/instructor for approval
3. Verify tests fail (Red phase)
4. Implement minimum code to make tests pass (Green phase)
5. Refactor while keeping tests green

No implementation without failing tests first. Red-Green-Refactor cycle strictly enforced.

### IV. Training Clarity & Documentation
As a training application, code must be:
- **Educational**: Demonstrates best practices appropriate for learning context
- **Well-Commented**: Complex logic includes explanatory comments for students
- **Self-Documenting**: Clear naming conventions - methods and variables explain their purpose
- **Limitation-Aware**: Training simplifications (e.g., mock auth) are documented with comments explaining production requirements

Code reviews must verify educational value, not just functionality.

### V. Security by Design
Even in training context, demonstrate proper security patterns:
- **Authorization**: All protected pages/components require `[Authorize]` attribute
- **Service-Level Checks**: Services validate user permissions before data access
- **Data Isolation**: Users see only data they're authorized to access
- **Defense in Depth**: Security at multiple layers (middleware, page, service)
- **Training Transparency**: Mock security implementations include comments explaining production requirements (password hashing, MFA, OAuth, audit logging)

### VI. Data Integrity & Entity Framework Best Practices
Database operations follow EF Core best practices:
- **Migrations**: All schema changes via EF migrations, never manual SQL
- **Validation**: Model-level validation attributes enforced
- **Transactions**: Related changes wrapped in transactions
- **Eager/Explicit Loading**: Prevent N+1 queries with `.Include()` or explicit loading
- **Async Operations**: Use async/await for all database operations

### VII. Simplicity & YAGNI
Keep implementations simple and focused on current training objectives:
- Build only what the specification requires
- No premature optimization
- No speculative features "we might need later"
- External dependencies minimized to maintain offline training capability
- When choosing between simple and clever, choose simple

## Technology Stack Requirements

### Core Technologies (LOCKED)
- **.NET 9.0**: Target framework for all projects
- **Blazor Server**: UI framework (no client-side Blazor or MVC)
- **Entity Framework Core 9.0**: ORM for data access
- **SQL Server**: Database (LocalDB for training environments)
- **xUnit**: Unit testing framework (if/when tests are added)

### Dependency Management
- NuGet packages must be justified and approved before adding
- Avoid dependencies that require cloud services or internet connectivity
- Document any new dependencies in project documentation
- Keep dependencies up to date within major version bounds

### Project Structure
Maintain consistent folder organization:
```
ContosoDashboard/
├── Data/              # DbContext and database configuration
├── Models/            # Entity classes (DB models)
├── Services/          # Business logic layer
├── Pages/             # Razor Pages (login, logout)
├── Components/        # Blazor components (.razor files)
├── Shared/            # Shared layouts and components
├── wwwroot/           # Static files (CSS, images)
└── Program.cs         # Application configuration
```

## Development Workflow

### Spec-Driven Development Cycle
1. **Requirements Gathering**: Stakeholder provides feature requirements document
2. **Specification Writing**: Create detailed technical specification from requirements
3. **Specification Review**: Stakeholder approves specification before implementation
4. **Test Writing**: Write unit tests based on specification acceptance criteria
5. **Test Review**: Stakeholder/instructor approves tests
6. **Implementation**: TDD cycle - Red, Green, Refactor
7. **Integration**: Verify feature in running application
8. **Documentation**: Update README/docs with new feature details

### Quality Gates
Before considering any feature complete:
- ✅ All unit tests pass
- ✅ Service layer logic isolated and testable
- ✅ Authorization checks in place for protected operations
- ✅ EF migrations created and tested
- ✅ Code includes educational comments for training purposes
- ✅ Manual testing completed in running application
- ✅ README updated with feature description and usage

### Code Review Standards
All code changes require review for:
- **Spec Compliance**: Implementation matches approved specification
- **Architecture**: Service layer properly used, no business logic in UI
- **Security**: Authorization checks present and correct
- **Testing**: Unit tests exist and cover requirements
- **Educational Value**: Code is instructive for training purposes
- **Simplicity**: Solution is appropriately simple, not over-engineered

## Governance

This constitution supersedes all other development practices. All development activities must comply with these principles.

**Amendment Process**: Constitution changes require:
1. Documented rationale for the change
2. Stakeholder/instructor approval
3. Update to this document with new version number
4. Communication to all team members/students

**Compliance Verification**: All code reviews and feature approval must explicitly verify:
- Specification exists and was approved before implementation
- Tests were written and approved before implementation
- Service layer architecture is maintained
- Security patterns are demonstrated appropriately
- Code has educational clarity

**Complexity Justification**: Any departure from simplicity must be justified in writing and approved before implementation.

**Guidance Reference**: For detailed development guidance during feature implementation, refer to the SpecKit templates in `.specify/templates/` and scripts in `.specify/scripts/`.

**Version**: 1.0.0 | **Ratified**: 2026-02-25 | **Last Amended**: 2026-02-25
