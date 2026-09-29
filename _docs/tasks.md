# Product Defect Management Platform — Task Backlog

## 1. Project Scaffolding and Baseline Test Setup
Goal: Initialize the Spring Boot backend and Angular frontend project structure with an automated baseline test that passes.
Description: Scaffold a Spring Boot application using Maven with Java and an initial context load unit test, alongside an Angular standalone application with an initial component test. Configure the build scripts and directory layout so that both applications can be built and tested independently from the command line. Verify that running the test suites in both projects succeeds cleanly without errors.

## 2. Domain Model Entities and H2 Persistence Configuration
Goal: Define JPA entities for User, Defect, Attachment, and Activity with file-persisted H2 database configuration.
Description: Implement the JPA domain model covering User, Defect, Attachment, and Activity classes according to the defect data model, including status and priority enums. Configure Spring Data JPA to connect to a file-backed H2 database so that data is preserved across application restarts. Create the Spring Data repository interfaces and write an integration test verifying that defect entities can be saved and queried.

## 3. Demo Data Seeder and Persona Fixtures
Goal: Automatically populate the database on startup with realistic BMW defect records and simulated users.
Description: Create a database initialization component (`ApplicationRunner`) that populates the H2 database with realistic sample users (Employees and Team Leads/Managers) and defect records across each workflow status (Reported, Investigating, Resolved, Closed). Populate rich sample details including vehicle models, affected components, symptom descriptions, and pre-existing activity entries. Ensure seed data execution can be toggled via application configuration so it only runs when needed.

## 4. Defect CRUD and Search/Filter REST API Endpoints
Goal: Implement RESTful API endpoints for creating, reading, updating, and filtering defect records.
Description: Develop Spring `@RestController` endpoints exposing operations to create a defect, retrieve a defect by ID, update defect details, and list defects with query filters (status, priority, category, assigned investigator). Define clean DTOs and input validation annotations for request payloads, with OpenAPI annotations for interactive documentation. Write automated Spring MockMvc tests verifying that endpoints return expected status codes and filtered JSON responses.

## 5. Defect Workflow State Transition Service and Business Rules
Goal: Enforce the 4-step defect lifecycle (Reported → Investigating → Resolved → Closed) and role-based state validations.
Description: Build a dedicated workflow service that validates allowed status transitions and restricts actions based on the actor's role (such as requiring Team Lead permissions to close a defect). Update defect resolution timestamps automatically when moving to Resolved and Closed states, rejecting invalid state skips with descriptive error responses. Provide unit tests covering all valid transitions and asserting that forbidden state changes are properly blocked.

## 6. Automated Defect Activity History Audit Logger
Goal: Automatically record an immutable activity history entry whenever a defect is created, reassigned, or changes state.
Description: Implement an auditing listener or service interceptor that automatically records an `Activity` log record whenever a defect's status, assignee, priority, or investigation notes change. Expose a REST endpoint (`GET /api/defects/{id}/activities`) returning the chronological activity history for a specific defect. Write an integration test to ensure that updating a defect status automatically writes an audit record with timestamp and user information.

## 7. Evidence File Attachment REST Service
Goal: Enable uploading, storing, and downloading defect evidence files via REST endpoints.
Description: Build a local filesystem storage service and multipart REST controllers (`POST /api/defects/{id}/attachments` and `GET /api/attachments/{id}`) that store files on disk and save file metadata in the database. Include basic file size and MIME-type validation, mapping uploaded files directly to their associated defect record. Write integration tests verifying that a file can be uploaded, linked to a defect, and subsequently downloaded intact.

## 8. Dashboard Metrics and Aggregation REST Endpoint
Goal: Provide an aggregated statistics endpoint that delivers KPI counts and recent defects for the dashboard view.
Description: Implement a REST endpoint (`GET /api/dashboard/stats`) that calculates total defect counts, counts grouped by status (Open, Investigating, Resolved, Closed), and total high-priority defects. Include a query returning the five most recently reported defects for quick dashboard preview. Write tests validating that the calculated metrics accurately reflect the current database contents.

## 9. Angular App Shell, PrimeNG Integration, and Base Layout
Goal: Build the responsive Angular application shell with PrimeNG component library setup and BMW-themed styling.
Description: Initialize PrimeNG, PrimeIcons, and theme styles within the standalone Angular application, applying a clean enterprise styling palette suitable for BMW internal tools. Implement the main application layout containing a top header bar, navigation links (Dashboard, Defect List, Create Defect), and a main router container. Write a component unit test verifying that the application shell renders the header and navigation links properly.

## 10. Simulated User Context and Role Switcher Component
Goal: Provide an interactive role and user switcher in the Angular header to simulate Employee and Team Lead personas.
Description: Create an Angular `UserService` using reactive state/Signals to manage the active simulated user persona and their assigned role (Employee vs. Team Lead / Manager). Build a header dropdown selector component allowing the user to switch personas on the fly during a demonstration. Implement an HTTP interceptor that automatically injects the active user ID and role into outgoing HTTP request headers for backend verification.

## 11. Angular Defect and Dashboard HTTP Data Services
Goal: Implement Angular data services to communicate with all backend REST API endpoints.
Description: Create an Angular `DefectService` with methods for fetching dashboard metrics, querying defects with filter parameters, creating defects, transitioning defect states, and managing attachments. Define TypeScript interfaces mirroring the backend DTOs and handle HTTP error responses by notifying the user with PrimeNG toast messages. Add unit tests using Angular's `HttpTestingController` to verify request construction and response parsing.

## 12. Dashboard View Component with KPI Cards and Recent Defects
Goal: Build the dashboard view displaying metric summary cards, recent defects, and high-priority alerts.
Description: Develop the Dashboard screen using PrimeNG cards to showcase key metric counters: total defects, open defects, defects under investigation, resolved defects, and high-priority items. Embed a summary table listing the most recently reported defects with quick-action links to navigate directly to their detail views. Connect the component to the dashboard service and write a component test checking that metric counters render correctly.

## 13. Defect List View with Search, Filtering, and Sorting Table
Goal: Build an interactive defect table screen featuring full-text search, column filters, and status badges.
Description: Implement the Defect List screen using PrimeNG `p-table` with sortable columns, pagination, and filter inputs for status, priority, category, and assigned owner. Render color-coded status badges and priority tags to provide immediate visual clarity across defect records. Add a button linking to the defect creation form and enable row clicking to navigate to the individual defect details view.

## 14. Create Defect Reactive Form Component
Goal: Build a reactive form component for submitting new product defects with field validations.
Description: Create the Defect Creation screen using Angular Reactive Forms with inputs for title, description, category, severity, priority, and affected vehicle/component. Pre-populate the reporter field from the active simulated user context and validate required fields before enabling submission. Wire the form to submit to the backend API and navigate to the newly created defect's detail page upon successful creation.

## 15. Defect Details View with Investigation Information and History Timeline
Goal: Build the detailed view displaying defect specifications, investigation notes, resolution details, and activity timeline.
Description: Create the Defect Details screen showing comprehensive defect information, assigned owner, root cause notes, and resolution status. Integrate the PrimeNG `p-timeline` component to display the chronological audit log of all status changes, assignments, and notes associated with the defect. Conditionally display action buttons (e.g., "Assign Investigator", "Close Defect") based on the current user's role from `UserService`.

## 16. Workflow Action Modals and Status Transition Dialogs
Goal: Enable users to assign owners, record investigation findings, submit resolutions, and close defects via modal dialogs.
Description: Build dialog modals for workflow actions: assigning/reassigning an investigator, updating investigation and root-cause notes, submitting resolution details, and closing the defect. Wire each dialog to the workflow transition endpoints, enforcing role permissions so that only Team Leads can close or assign defects. Refresh the details view and the activity timeline upon completing any workflow transition.

## 17. Evidence Attachment Upload Widget and File Viewer
Goal: Implement an evidence upload and attachment list component on the defect details screen.
Description: Add a file upload section to the Defect Details screen using PrimeNG `p-fileUpload` to allow uploading supporting documents and images. Render a list of attached files displaying file names, upload timestamps, and direct download links. Handle file upload progress, validate allowed file types, and refresh the attachment list upon successful upload.

## 18. End-to-End Demonstration Scenario and Smoke Verification
Goal: Verify the complete 12-step defect lifecycle scenario from creation to closure as outlined in the POC plan.
Description: Execute a comprehensive smoke test of the complete end-to-end lifecycle: Employee login → Dashboard → Create Defect → Manager assignment → Investigator sets to Investigating → Investigator records resolution → Manager closes → Review activity timeline. Verify that permissions, state transitions, attachments, and audit history behave consistently across both frontend and backend. Document any setup steps or seed accounts needed to run the presentation smoothly.
