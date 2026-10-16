# LabOps — Process and Planning

## 1. Development Process

I chose an incremental development process for LabOps because I am developing the project individually and have a two-week deadline.

The system consists of separate modules, including authentication, equipment management, booking management, and fault management. I will develop and test these modules in small increments before integrating them with the rest of the system.

Testing will take place throughout development so that defects can be identified and fixed early instead of being discovered only at the end of the project.

## 2. Project Schedule

The project is planned over 14 days, with approximately 2–3 hours of work per day.

| Days | Planned Work |
|---|---|
| 1–2 | Requirements, user stories, acceptance criteria, system design, and database planning |
| 3 | Repository setup, development environment, and database configuration |
| 4 | Authentication, roles, and equipment management |
| 5–6 | Booking creation, cancellation, availability, and booking conflict prevention |
| 7 | Booking validation and testing |
| 8–9 | Fault reporting, assignment, status transitions, and history |
| 10 | Integration and authorization testing |
| 11 | Test coverage, defect fixing, and requirements traceability |
| 12 | Docker setup, automated testing, and README documentation |
| 13 | Project report, screenshots, and demonstration preparation |
| 14 | Final testing, clean setup verification, and project demonstration |

This schedule is a plan; actual progress and any changes will be recorded in the progress log.

## 3. Testing Strategy

Testing will be performed throughout development rather than only after implementation.

The planned testing activities are:

- Unit tests for validation rules and business logic.
- API tests for equipment, booking, and fault-management endpoints.
- Authorization tests to verify that users cannot perform actions outside their roles.
- Booking tests for overlapping reservations, adjacent time slots, cancellation, and equipment under repair.
- Integration tests for interactions between the application and the database.
- Concurrency tests to check that simultaneous overlapping booking requests cannot both succeed.

Only tests that have actually been executed will be reported as completed.

## 4. Risk Management

| Risk | Planned Mitigation |
|---|---|
| Overlapping bookings caused by simultaneous requests | Enforce booking integrity at the database level and test concurrent requests |
| Scope becoming too large for the deadline | Prioritize the agreed requirements and keep optional features out of scope |
| Unauthorized users performing restricted actions | Implement role-based authorization and test rejected requests |
| Defects discovered near the deadline | Test each module as it is developed |
| Documentation not matching the implementation | Update documentation as decisions and features change |

## 5. Progress Tracking

I will maintain a separate `LabOps_Progress_Log.md` file to record work completed, time spent, test results, actual defects and fixes, and important design decisions.

The project schedule may be adjusted if implementation or testing takes longer than expected. Any significant changes to requirements or design will be documented.

## 6. Definition of Done

A feature will be considered complete when:

- Its agreed requirements have been implemented.
- Relevant tests have been executed and their results recorded.
- Access restrictions and validation rules have been checked where applicable.
- Any identified defects have been addressed or documented.
- The relevant documentation has been updated.