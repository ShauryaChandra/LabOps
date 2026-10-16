# LabOps — Software Requirements Specification

## 1. Problem Statement

Laboratories use equipment such as oscilloscopes, multimeters, power supplies, and signal generators for practical sessions and academic projects. Informal reservations and maintenance reports can cause booking conflicts, unclear availability, and inconsistent fault tracking.

LabOps is a backend-first laboratory equipment booking and fault management prototype. Students can view equipment, make eligible bookings, cancel their own future bookings, and report faults. Technicians manage assigned faults and record maintenance progress. Administrators manage equipment status, assign fault reports, and cancel bookings when necessary.

The system aims to prevent overlapping bookings, restrict unauthorized actions, and maintain an auditable history of fault-status changes.

## 2. Project Objectives

- Provide a centralized equipment inventory.
- Allow users to check equipment availability.
- Prevent overlapping active bookings for the same equipment, including concurrent requests.
- Prevent new bookings for equipment marked Under Repair.
- Support fault reporting, assignment, and status tracking.
- Enforce role-based permissions.
- Maintain a history of fault-status changes.
- Provide automated tests and reproducible setup instructions.

## 3. Stakeholders

### 3.1 Students
Students need to view equipment availability, create eligible bookings, cancel their own bookings before they start, and report equipment faults.

### 3.2 Laboratory Technicians
Technicians need to view faults assigned to them, update the status of their assigned faults, and record maintenance progress.

### 3.3 Administrators
Administrators need to manage equipment status, assign fault reports to technicians, and cancel bookings when necessary.

### 3.4 Project Evaluator
The evaluator needs to verify the requirements, diagrams, implementation, tests, and documentation against the assignment rubric.

## 4. Project Scope

### 4.1 In Scope

- Authentication and role-based authorization for students, technicians, and administrators.
- Viewing the equipment inventory and equipment status.
- Viewing equipment bookings over a selected time range.
- Creating, viewing, and cancelling bookings subject to permission rules.
- Preventing overlapping active bookings for the same equipment.
- Blocking new bookings for equipment marked Under Repair.
- Reporting faults and assigning fault reports to technicians.
- Managing fault statuses and recording status-change history.
- Automated tests and a requirement-to-test traceability matrix.
- Documentation and reproducible setup instructions.

### 4.2 Out of Scope

- Integration with real university accounts.
- Administrator interface for creating or changing user roles; users will have predefined roles in the prototype.
- SMS or email notifications.
- Equipment procurement and payments.
- AI-based fault diagnosis or repair-time prediction.
- Native mobile applications.
- Integration with physical laboratory hardware.
- Priority-based booking in the initial version.
- Automatic cancellation or rescheduling of existing bookings when equipment is marked Under Repair.

All initial users, equipment, bookings, and fault reports will use fictional sample data. LabOps is an academic prototype, not an official university booking service.

## 5. Assumptions and Constraints

- Each equipment item has a unique identifier.
- Each booking is associated with one user and one equipment item.
- A booking has a start time, end time, and status.
- Booking times use timezone-aware timestamps stored in UTC.
- A booking start time must be earlier than its end time.
- New bookings must start in the future.
- Cancelled bookings do not block future bookings for the same time slot.
- Equipment marked Under Repair cannot receive new bookings.
- Reporting a fault does not automatically change the equipment status. An authorized technician or administrator must change the status to Under Repair.
- When equipment is marked Under Repair, existing bookings remain unchanged. The system does not automatically cancel or reschedule them; an administrator can review and cancel bookings when appropriate.
- Students can cancel their own bookings only before the booking start time. Administrators can cancel any booking.
- Only administrators can assign fault reports to technicians. Technicians can update statuses only for faults assigned to them; administrators may oversee fault status changes.
- The project will be developed by one student within approximately two weeks.
- The initial demo will use FastAPI's generated API documentation rather than a separate frontend.

## 6. Functional Requirements

### FR-01 — Authentication and Roles
The system shall authenticate registered users and restrict protected actions according to their assigned role: student, technician, or administrator.

### FR-02 — Equipment Inventory
The system shall allow authenticated users to view registered equipment, including its unique identifier, name, and current status.

### FR-03 — Booking Availability
The system shall display existing bookings for a selected equipment item over a requested time range.

### FR-04 — Booking Creation and Validation
The system shall allow an authenticated student to create a booking only when the start time is earlier than the end time, the start time is in the future, and the equipment is not marked Under Repair.

### FR-05 — Conflict Prevention
The system shall reject a booking when its time interval overlaps an active booking for the same equipment. Adjacent bookings are allowed, and cancelled bookings do not block a slot.

### FR-06 — Booking Cancellation
Students shall be able to cancel their own bookings only before the booking start time. Administrators shall be able to cancel any booking. Other users shall not be able to cancel a student's booking.

### FR-07 — Equipment Status Management
The system shall allow technicians and administrators to change equipment status between Available and Under Repair.

### FR-08 — Repair Booking Restriction
The system shall reject new bookings for equipment whose status is Under Repair. Existing bookings shall remain unchanged unless an administrator explicitly cancels them.

### FR-09 — Fault Reporting
The system shall allow a student to submit a fault report containing the affected equipment and a description of the problem. Submitting a fault report shall not automatically change the equipment status.

### FR-10 — Fault Status Transitions
The system shall support the fault statuses Submitted, Assigned, In Progress, and Resolved. A fault may move only through permitted transitions: Submitted → Assigned → In Progress → Resolved. Administrators may oversee status changes; technicians may update only faults assigned to them.

### FR-11 — Fault Assignment
The system shall allow an administrator to assign a submitted fault report to a technician. The system shall reject assignment attempts made by students or technicians.

### FR-12 — Fault Status History
Whenever a fault status changes, the system shall record the fault identifier, previous status, new status, responsible user, and timestamp.

## 7. Non-Functional Requirements

### NFR-01 — Maintainability
The application shall separate API routes, business logic, database models, and automated tests into distinct modules.

### NFR-02 — Security
Passwords shall be stored using a suitable password-hashing algorithm, never as plaintext. Automated tests shall verify that unauthenticated requests to protected endpoints are rejected and users cannot perform actions outside their permissions.

### NFR-03 — Booking Integrity Under Concurrency
Even when two booking requests for the same equipment and overlapping time intervals are submitted simultaneously, no more than one active booking shall be accepted. This shall be verified with an automated concurrency test or a documented concurrent-request test.

### NFR-04 — Performance
On the documented development environment and seeded dataset, at least 95% of 100 measured equipment-list requests shall complete within one second. The actual measurement shall be recorded during testing; the target shall not be claimed as achieved without evidence.

### NFR-05 — Reproducibility
A developer following the README shall be able to start the application and run the automated test suite using documented commands in a clean environment with the stated prerequisites.

## 8. User Stories and Acceptance Criteria

### US-01 — Find Equipment and Availability

**User story:** As a student, I want to view equipment and its booking availability so that I can choose a suitable time.

**Related requirements:** FR-02, FR-03.

**Acceptance criteria:**
- Given registered equipment, when an authenticated student requests the inventory, the system returns equipment identifiers, names, and statuses.
- Given an equipment item and a valid time range, when the student requests availability, the system returns the existing bookings in that range.

### US-02 — Make a Booking

**User story:** As a student, I want to reserve available equipment for a future time slot so that I can use it for a lab session.

**Related requirements:** FR-04, FR-05, FR-08.

**Acceptance criteria:**
- Given available equipment and a valid future interval, when the student submits a non-conflicting booking, the system accepts it.
- Given an overlapping active booking for the same equipment, when the student submits a booking, the system rejects it.
- Given equipment marked Under Repair, when the student submits a booking, the system rejects it.

### US-03 — Manage My Booking

**User story:** As a student, I want to view and cancel my eligible bookings so that I can manage my reservations.

**Related requirements:** FR-06.

**Acceptance criteria:**
- Given a student's booking that has not started, when the student cancels it, the system marks it cancelled.
- Given a booking that has already started, when the student attempts to cancel it, the system rejects the request.
- Given another student's booking, when a student attempts to cancel it, the system rejects the request.

### US-04 — Report and Track a Fault

**User story:** As a student, I want to report faulty equipment so that laboratory staff can investigate the problem.

**Related requirements:** FR-09, FR-10, FR-12.

**Acceptance criteria:**
- Given registered equipment and a non-empty fault description, when the student submits a report, the system creates a fault record.
- Creating a fault report does not automatically change the equipment status.
- When an authorized user changes a fault's status, the system records the old status, new status, user, and timestamp.

### US-05 — Assign and Resolve Faults

**User story:** As a laboratory technician, I want to see faults assigned to me and update their status so that maintenance progress is tracked.

**Related requirements:** FR-10, FR-11, FR-12.

**Acceptance criteria:**
- Given a submitted fault, when an administrator assigns it to a technician, the system records the assignment and the fault enters the Assigned status.
- Given a fault assigned to a technician, when that technician moves it through a permitted transition, the system accepts the change and records it in the history.
- Given a fault assigned to a different technician, when a technician attempts to update it, the system rejects the request.

## 9. Additional Acceptance Criteria by Functional Requirement

- **FR-01 — Authentication and roles:** Given an unauthenticated request to a protected endpoint, the system rejects it. Given an authenticated user, protected actions are limited to that user's role.
- **FR-02 — Equipment inventory:** Given registered equipment, when an authenticated user requests the inventory, each returned item includes its unique identifier, name, and status.
- **FR-03 — Booking availability:** Given an equipment item and a valid time range, when availability is requested, the response includes existing bookings that intersect the requested range.
- **FR-04 — Booking creation and validation:** Given a start time after the end time, or a start time in the past, when a booking is submitted, the system rejects it.
- **FR-05 — Conflict prevention:** Given a booking from 10:00 to 11:00, a request from 10:30 to 11:30 for the same equipment is rejected, while a request from 11:00 to 12:00 is allowed if all other rules are satisfied.
- **FR-06 — Booking cancellation:** A student can cancel their own booking before it starts; the system rejects student cancellation after the start time and rejects cancellation of another student's booking. An administrator can cancel any booking.
- **FR-07 — Equipment status management:** A student attempting to change equipment status is rejected; a technician or administrator may change it.
- **FR-08 — Repair restriction:** Given equipment marked Under Repair, a new booking is rejected. Existing bookings are not automatically changed.
- **FR-09 — Fault reporting:** Given registered equipment and a non-empty description, a student can create a fault report. Submitting the report alone does not change equipment status.
- **FR-10 — Fault status transitions:** A permitted status transition is accepted; a transition that skips or reverses a defined stage is rejected unless a later documented rule explicitly permits it.
- **FR-11 — Fault assignment:** An administrator can assign a fault to a technician; a student or technician cannot assign faults.
- **FR-12 — Fault status history:** Each successful fault-status change creates a history record containing the old and new statuses, actor, and timestamp.

## 10. Risks and Mitigations

### Risk 1 — Conflicting Bookings
**Risk:** Simultaneous requests could create overlapping reservations if the application only checks availability before saving.

**Mitigation:** Enforce the booking conflict rule at the database level and test overlapping and simultaneous booking attempts.

### Risk 2 — Scope Creep
**Risk:** Features such as priority booking or a separate frontend could delay completion.

**Mitigation:** Prioritize core booking and fault-management workflows and defer optional features until required work is complete.

### Risk 3 — Inconsistent Documentation
**Risk:** Requirements, diagrams, tests, and implementation could describe different behaviour.

**Mitigation:** Review diagrams against the requirements and maintain a requirement-to-test traceability matrix.

### Risk 4 — Unauthorized Actions
**Risk:** Users could access bookings or fault-management functions outside their permitted roles.

**Mitigation:** Implement role-based authorization and test both permitted and rejected actions.

## 11. Planned Technology Stack

- Python for application development.
- FastAPI for the API.
- PostgreSQL for persistent data storage.
- SQLAlchemy for database interaction.
- Alembic for database migrations.
- Pytest for automated testing.
- Docker Compose for reproducible local setup.
- Git and GitHub for version control.

These are planned choices. Their implementation and verification will be documented as development progresses.

## 12. Requirement Verification

Each functional and non-functional requirement will be mapped to one or more test cases in a separate requirement-to-test traceability matrix. Requirements will be considered verified only when supporting implementation and test evidence are available.

## 13. Limitations

LabOps is a small academic prototype. It will initially use fictional data and will not integrate with university identity systems, actual laboratory hardware, or external notification services. Its reliability and performance claims will be limited to the behaviour demonstrated by implemented features and recorded tests.