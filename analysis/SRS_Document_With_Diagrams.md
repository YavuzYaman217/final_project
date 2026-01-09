# Software Requirements Specification (SRS)
## Exam Security System (Identity Verification + Seating Plan + Violation Logging)

**Document Version:** 2.0 (With Embedded Diagrams)  
**Date:** January 2026  
**Project Name:** Exam Security System  
**Course:** Software Testing & Validation  

---

## Table of Contents

1. [Introduction](#introduction)
2. [Overall Description](#overall-description)
3. [System Architecture & Diagrams](#system-architecture--diagrams)
    1. [Use Case Diagram](#use-case-diagram)
    2. [Entity Relationship Diagram (ERD)](#entity-relationship-diagram-erd)
    3. [Activity Diagram](#activity-diagram)
    4. [Sequence Diagrams](#sequence-diagrams)
4. [Functional Requirements](#functional-requirements)
5. [Non-Functional Requirements](#non-functional-requirements)
6. [Database Requirements](#database-requirements)
7. [Business Rules](#business-rules)
8. [User Interface Requirements](#user-interface-requirements)
9. [Testing & Validation Requirements](#testing--validation-requirements)
10. [Glossary](#glossary)

---

## 1. Introduction

### 1.1 Purpose

This document specifies the functional and non-functional requirements for the **Exam Security System**, a web-based application designed to manage exam-day security operations. The system ensures that only registered students enter the exam room, that they sit in assigned seats according to the seating plan, and that all violations are properly recorded and reported.

### 1.2 Scope

The Exam Security System is a web-based application that supports three core actors:
- **Students:** Individuals taking the exam
- **Proctors (Invigilators):** Personnel monitoring exam compliance
- **Exam Coordinators (Administrators):** Personnel managing exams, seating plans, and reports

The system integrates a simple machine learning/computer vision component for identity verification and provides comprehensive violation logging and reporting capabilities.

### 1.3 Document Conventions

- **Shall/Must:** Indicates a mandatory requirement
- **Should:** Indicates a recommended requirement
- **May:** Indicates an optional requirement
- **FR-X:** Functional Requirement identifier
- **NFR-X:** Non-Functional Requirement identifier

### 1.4 Intended Audience

- Development Team
- Quality Assurance Team
- Project Stakeholders
- Instructors and Evaluators

---

## 2. Overall Description

### 2.1 Product Perspective

The Exam Security System is a standalone web application that operates independently but may integrate with existing student information systems for roster import. It is designed to be deployed in a controlled exam environment with internet connectivity.

### 2.2 Product Functions

The system provides the following major functions:

1. **Authentication & Authorization:** Role-based access control for Proctors and Administrators
2. **Exam Management:** Create and configure exams with date, time, and room information
3. **Seating Plan Management:** Define and manage student seating assignments
4. **Student Roster Management:** Import or manually enter student information
5. **Identity Verification:** Capture and verify student identity using photo comparison
6. **Check-In Workflow:** Process student check-in with photo capture and verification
7. **Seat Compliance Verification:** Validate that students sit in assigned seats
8. **Violation Recording:** Log and document any exam violations
9. **Reporting:** Generate reports on check-ins, mismatches, and violations

### 2.3 User Classes and Characteristics

#### 2.3.1 Students
- **Characteristics:** Exam participants, may have limited technical experience
- **Responsibilities:** Provide identity verification, sit in assigned seat
- **Frequency of Use:** One-time per exam session

#### 2.3.2 Proctors (Invigilators)
- **Characteristics:** Trained exam monitors, moderate technical experience
- **Responsibilities:** Verify student identity, check seating compliance, record violations
- **Frequency of Use:** Throughout exam duration

#### 2.3.3 Exam Coordinators (Administrators)
- **Characteristics:** Exam management personnel, good technical experience
- **Responsibilities:** Create exams, manage seating plans, import rosters, generate reports
- **Frequency of Use:** Before and after exam sessions

### 2.4 Operating Environment

- **Platform:** Web-based application (browser-based)
- **Browsers:** Chrome, Firefox, Safari, Edge (latest versions)
- **Server:** Node.js/Express or Python/Flask backend
- **Database:** MySQL, PostgreSQL, or similar relational database
- **Hardware:** Standard desktop/laptop with camera for photo capture

### 2.5 Design and Implementation Constraints

- Simple ML/Computer Vision component (library-based, not custom-trained models)
- No deep learning model training required
- Grading focuses on integration, validation, and workflow correctness
- Role-based access control must be enforced
- System must handle concurrent user sessions

### 2.6 Assumptions and Dependencies

- Students have valid registered accounts with the system
- Photo capture devices (cameras) are available at check-in stations
- Network connectivity is stable during exam sessions
- Database is properly backed up and maintained
- ML/CV library (e.g., face_recognition, OpenCV) is available

---

## 3. System Architecture & Diagrams

This section provides a visual overview of the system architecture through a series of UML diagrams.

### 3.1 Use Case Diagram

**Figure 1: Use Case Diagram**

This diagram illustrates the interactions between system actors (Administrator, Proctor, Student) and the main use cases of the Exam Security System.

![Use Case Diagram](../diagrams/usecase.png)

### 3.2 Entity Relationship Diagram (ERD)

**Figure 2: Entity Relationship Diagram (ERD)**

This diagram shows the complete database schema, including all entities (tables), their attributes, and the relationships between them. It uses Crow's Foot notation.

![Entity Relationship Diagram](../diagrams/erd_updated.png)

### 3.3 Activity Diagram

**Figure 3: Activity Diagram**

This diagram provides a high-level overview of the exam day workflow, showing the flow of activities between the Administrator, Proctor, and the System.

![Activity Diagram](../diagrams/activity.png)

### 3.4 Sequence Diagrams

#### 3.4.1 Sequence Diagram: Student Check-In Workflow

**Figure 4: Student Check-In Sequence Diagram**

This diagram details the step-by-step interactions for the student check-in process, including identity verification, seat compliance, and automatic violation creation.

![Student Check-In Sequence Diagram](../diagrams/sequence_checkin_detailed.png)

#### 3.4.2 Sequence Diagram: Violation Recording Workflow

**Figure 5: Violation Recording Sequence Diagram**

This diagram shows the workflow for a proctor recording an exam violation with optional evidence attachment.

![Violation Recording Sequence Diagram](../diagrams/sequence_violation.png)

#### 3.4.3 Sequence Diagram: Report Generation Workflow

**Figure 6: Report Generation Sequence Diagram**

This diagram illustrates the process of an administrator generating and exporting exam reports.

![Report Generation Sequence Diagram](../diagrams/sequence_reporting.png)

---

## 4. Functional Requirements

### 4.1 Authentication & Authorization (FR-1 to FR-3)

#### FR-1: User Login
- **Description:** Users shall authenticate using username and password
- **Actors:** Proctor, Administrator
- **Preconditions:** User account exists in the system
- **Steps:**
  1. User navigates to login page
  2. User enters username and password
  3. System validates credentials against database
  4. System creates session and redirects to dashboard
- **Postconditions:** User is authenticated and session is active
- **Alternative Flows:** Invalid credentials trigger error message; account lockout after 5 failed attempts

#### FR-2: Role-Based Access Control
- **Description:** System shall enforce role-based access control for Proctor and Administrator roles
- **Actors:** System
- **Rules:**
  - Proctors can only access check-in, violation recording, and basic reporting features
  - Administrators can access all features including exam creation, roster management, and advanced reporting
  - Students do not require login; they are identified during check-in
- **Validation:** Unauthorized access attempts shall be logged and rejected

#### FR-3: Session Management
- **Description:** System shall manage user sessions with automatic timeout
- **Timeout Duration:** 30 minutes of inactivity
- **Actions:** Expired sessions redirect users to login page
- **Validation:** Session tokens are validated on each request

### 4.2 Exam Management (FR-4 to FR-6)

#### FR-4: Exam Creation
- **Description:** Administrators shall create exams with the following information:
  - Exam name/code
  - Date and time
  - Room/location
  - Duration
  - Maximum capacity
- **Validation Rules:**
  - Exam code must be unique
  - Date/time must be in the future
  - Capacity must be greater than 0
- **Postconditions:** Exam is created and available for roster and seating plan assignment

#### FR-5: Exam Configuration
- **Description:** Administrators shall configure exam parameters:
  - Enable/disable identity verification requirement
  - Set seating plan requirement (mandatory/optional)
  - Configure violation categories and severity levels
- **Validation:** Configuration changes are logged with timestamp and user ID

#### FR-6: Exam Status Management
- **Description:** System shall track exam status:
  - Draft: Exam created but not yet active
  - Active: Exam is ongoing
  - Completed: Exam has ended
  - Archived: Exam is closed and reports are final
- **Transitions:** Only authorized transitions are allowed (Draft → Active → Completed → Archived)

### 4.3 Student Roster Management (FR-7 to FR-9)

#### FR-7: Student Roster Import
- **Description:** Administrators shall import student roster via CSV file upload
- **File Format:** CSV with columns: StudentID, FirstName, LastName, Email, RegistrationNumber
- **Validation Rules:**
  - StudentID must be unique
  - Required fields must not be empty
  - Duplicate entries are flagged for review
- **Postconditions:** Students are added to the exam roster

#### FR-8: Manual Student Entry
- **Description:** Administrators shall manually add individual students to the roster
- **Fields Required:** StudentID, FirstName, LastName, Email, RegistrationNumber
- **Validation:** Same rules as FR-7
- **Postconditions:** Student is added to roster

#### FR-9: Roster Management
- **Description:** Administrators shall manage the student roster:
  - View all students in roster
  - Edit student information
  - Remove students from roster
  - Export roster to CSV
- **Validation:** Changes are logged with timestamp and user ID

### 4.4 Seating Plan Management (FR-10 to FR-12)

#### FR-10: Seating Plan Creation
- **Description:** Administrators shall create seating plans with the following options:
  - **Grid-Based:** Define rows and columns (e.g., 10 rows × 8 columns)
  - **Seat Code-Based:** Define individual seat codes (e.g., A1, A2, B1, etc.)
- **Validation Rules:**
  - Total seats must be ≥ number of students in roster
  - Seat identifiers must be unique
  - Plan must be associated with an exam
- **Postconditions:** Seating plan is created and ready for student assignment

#### FR-11: Student Seat Assignment
- **Description:** Administrators shall assign students to seats:
  - Manual assignment: Drag-and-drop or form-based
  - Automatic assignment: Random or sequential
- **Validation Rules:**
  - Each student assigned to exactly one seat
  - Each seat assigned to at most one student
  - Cannot assign students not in roster
- **Postconditions:** All students have assigned seats

#### FR-12: Seating Plan Visualization
- **Description:** System shall display seating plan with:
  - Visual grid or layout representation
  - Student names/IDs in assigned seats
  - Color coding for assigned/unassigned/occupied seats
  - Real-time updates during check-in
- **Actors:** Proctor, Administrator
- **Validation:** Display updates within 5 seconds of check-in

### 4.5 Identity Verification (FR-13 to FR-15)

#### FR-13: Photo Capture
- **Description:** System shall capture student photo during check-in
- **Process:**
  1. Student positions face in front of camera
  2. System captures image automatically or on manual trigger
  3. Image is stored with timestamp and student ID
- **Technical Requirements:**
  - Support multiple image formats (JPEG, PNG)
  - Image resolution ≥ 640×480 pixels
  - Automatic face detection to guide student positioning
- **Validation:** Image must contain a detectable face

#### FR-14: ML/Computer Vision Verification
- **Description:** System shall verify captured photo against registered student photo using ML/CV
- **Implementation Options:**
  - Face verification using face_recognition library (embedding-based comparison)
  - ID photo similarity comparison using OpenCV
  - Basic template matching with embeddings
- **Process:**
  1. Extract face embeddings from captured photo
  2. Compare with registered student photo embeddings
  3. Generate match confidence score (0-100%)
  4. Determine pass/fail based on threshold (e.g., 70%)
- **Output:** Match result (Match/No Match) with confidence score
- **Validation:**
  - Threshold must be configurable
  - Results must be logged with timestamp
  - Manual override available for Proctors

#### FR-15: Verification Decision
- **Description:** System shall present verification decision to Proctor
- **Display Information:**
  - Captured photo
  - Registered student photo
  - Match confidence score
  - Recommendation (Match/No Match)
- **Proctor Actions:**
  - Accept verification (proceed to seating check)
  - Reject verification (student not allowed to sit)
  - Override decision (manual approval despite mismatch)
- **Postconditions:** Verification result is recorded with timestamp and Proctor ID

### 4.6 Check-In Workflow (FR-16 to FR-20)

#### FR-16: Check-In Initiation
- **Description:** Proctor initiates check-in process for a student
- **Input:** Student ID or name search
- **Process:**
  1. System retrieves student information from roster
  2. System displays student details and assigned seat
  3. System prompts for photo capture
- **Validation:** Student must be in roster and not already checked in

#### FR-17: Photo Capture & Upload
- **Description:** System captures and uploads student photo during check-in
- **Process:**
  1. Camera interface is displayed
  2. Student positions face in frame
  3. Photo is captured (automatic or manual trigger)
  4. Photo is uploaded to server
  5. System confirms successful upload
- **Validation:** Photo must be valid and contain detectable face

#### FR-18: ML Verification Decision
- **Description:** System processes photo through ML/CV component
- **Process:**
  1. System extracts face embeddings from captured photo
  2. System compares with registered student photo
  3. System generates confidence score
  4. System presents result to Proctor
- **Output:** Match/No Match with confidence score
- **Validation:** Result is logged with timestamp

#### FR-19: Seat Compliance Check
- **Description:** System verifies that student is sitting in assigned seat
- **Process:**
  1. Proctor confirms student identity (after verification)
  2. Proctor verifies student is in correct seat (visual confirmation)
  3. System records seat assignment and check-in time
- **Validation:** Seat must match student's assigned seat
- **Alternative:** If student is in wrong seat, violation is recorded (see FR-24)

#### FR-20: Check-In Completion
- **Description:** System completes check-in process and records result
- **Recorded Information:**
  - Student ID
  - Check-in timestamp
  - Verification result (Match/No Match)
  - Assigned seat
  - Actual seat (if different)
  - Proctor ID
  - Any violations or notes
- **Postconditions:** Student is marked as checked-in; seating plan is updated

### 4.7 Violation Recording (FR-21 to FR-24)
#### FR-21: Violation Categories
- **Description:** System shall support the following violation categories:
  - Identity Mismatch: Captured photo does not match registered photo
  - Seat Mismatch: Student sitting in wrong seat
  - Unauthorized Materials: Student has prohibited materials
  - Disruptive Behavior: Student is disruptive
  - Late Arrival: Student arrives after exam start
  - Other: Custom violation reason
- **Validation:** Each violation must have a category

#### FR-22: Violation Recording
- **Description:** Proctor shall record violations with the following information:
  - Violation category
  - Student ID
  - Timestamp
  - Reason/notes (text description)
  - Evidence image (optional)
  - Severity level (Low/Medium/High)
- **Validation Rules:**
  - All required fields must be completed
  - Timestamp must be during exam session
  - Evidence image (if provided) must be valid image file
- **Postconditions:** Violation is recorded and associated with student

#### FR-23: Violation Evidence
- **Description:** Proctor may attach evidence image to violation record
- **Process:**
  1. Proctor captures or uploads image as evidence
  2. System stores image with violation record
  3. Image is linked to violation ID
- **Validation:** Image must be valid format (JPEG, PNG)
- **Optional:** Evidence images are optional but recommended

#### FR-24: Violation Status Tracking
- **Description:** System shall track violation status:
  - Recorded: Violation is logged
  - Reviewed: Violation has been reviewed by administrator
  - Resolved: Violation has been addressed
  - Dismissed: Violation is not substantiated
- **Transitions:** Only authorized transitions allowed
- **Validation:** Status changes are logged with timestamp and user ID

### 4.8 Reporting (FR-25 to FR-28)

#### FR-25: Check-In Report
- **Description:** System shall generate check-in report with:
  - List of all students checked in
  - Check-in timestamp
  - Verification result (Match/No Match)
  - Assigned vs. actual seat
  - Proctor who performed check-in
- **Format:** Exportable to CSV, PDF, or display in web interface
- **Filters:** By exam, by date range, by proctor

#### FR-26: Mismatch Report
- **Description:** System shall generate mismatch report with:
  - Students with identity mismatches
  - Students in wrong seats
  - Timestamp of mismatch detection
  - Proctor who recorded mismatch
- **Format:** Exportable to CSV, PDF
- **Filters:** By exam, by confidence score range

#### FR-27: Violation Report
- **Description:** System shall generate violation report with:
  - All violations recorded
  - Filterable by category, severity, status, student, proctor
  - Includes violation details and evidence links
- **Format:** Exportable to CSV, PDF
- **Filters:** By exam, by date, by violation type

#### FR-28: Summary Report
- **Description:** System shall generate summary report with:
  - Total students checked in
  - Total identity mismatches
  - Total seat mismatches
  - Total violations by category
  - Check-in compliance percentage
- **Format:** Dashboard view with charts and graphs
- **Filters:** By exam

---

## 5. Non-Functional Requirements

### 5.1 Performance (NFR-1 to NFR-3)

- **NFR-1: Response Time:** All API responses shall be < 1 second under normal load.
- **NFR-2: Verification Speed:** ML/CV verification shall complete in < 3 seconds.
- **NFR-3: Concurrent Users:** System shall support at least 10 concurrent proctors.

### 5.2 Security (NFR-4 to NFR-6)

- **NFR-4: Password Hashing:** All user passwords shall be hashed using bcrypt.
- **NFR-5: Data Encryption:** All data in transit shall be encrypted using TLS/SSL.
- **NFR-6: Audit Trail:** All critical actions shall be logged in an immutable audit trail.

### 5.3 Usability (NFR-7 to NFR-8)

- **NFR-7: User Interface:** UI shall be intuitive and require minimal training.
- **NFR-8: Accessibility:** System shall comply with WCAG 2.1 AA standards.

### 5.4 Reliability (NFR-9 to NFR-10)

- **NFR-9: Uptime:** System shall have 99.9% uptime during exam periods.
- **NFR-10: Data Integrity:** Database shall enforce referential integrity through foreign keys.

---

## 6. Database Requirements

### 6.1 Database Schema

The database schema is detailed in the Entity Relationship Diagram (ERD) in Section 3.2. It consists of 10 main tables:

1. **users**: System users (Admins and Proctors)
2. **rooms**: Exam room information
3. **exams**: Exam configurations
4. **students**: Student information and registered photos
5. **exam_rosters**: Student-to-exam assignments
6. **seating_plans**: Seating plan configurations
7. **seat_assignments**: Individual seat records
8. **check_ins**: Check-in records with verification results
9. **violations**: Violation records with evidence
10. **audit_logs**: System activity audit trail

### 6.2 Data Dictionary

*(A detailed data dictionary should be provided here, but for brevity, it is omitted. Refer to the ERD for attribute details.)*

---

## 7. Business Rules

This section outlines the key business rules that govern the behavior of the Exam Security System. These rules ensure data integrity, security, and consistent operational workflows.

### 7.1. BR-1: Exam Status Progression

- **Description**: An exam must follow a specific lifecycle: DRAFT → ACTIVE → COMPLETED → ARCHIVED.
- **Rationale**: Ensures that exams are properly configured before becoming active and are properly closed after completion.
- **Validation**: The system will only allow status transitions in the specified order. For example, an exam cannot be moved from DRAFT to COMPLETED.
- **Error Handling**: An error message will be displayed if an invalid status transition is attempted.
- **Related Requirements**: FR-4, FR-5

### 7.2. BR-2: Proctor-Exam Assignment

- **Description**: A proctor must be assigned to an exam to perform check-ins and record violations for that exam.
- **Rationale**: Enforces accountability and ensures that only authorized proctors can manage a specific exam session.
- **Validation**: The system will check for a valid proctor-exam assignment before allowing any operational actions.
- **Error Handling**: Access will be denied with a "Not authorized for this exam" message if a proctor attempts to access an unassigned exam.
- **Related Requirements**: FR-2, FR-15

### 7.3. BR-3: Identity Verification Threshold

- **Description**: A confidence score of 75% or higher from the ML/CV service is required for an automatic identity match.
- **Rationale**: Balances security with usability by setting a reasonable threshold for automated verification, reducing false positives while still flagging significant mismatches.
- **Validation**: The system will check if `confidence_score >= 0.75` to determine the verification result.
- **Error Handling**: Scores below 75% will be flagged as a "NO_MATCH", requiring manual review and override by the proctor.
- **Related Requirements**: FR-14, FR-19

### 7.4. BR-4: Account Lockout Policy

- **Description**: A user account will be temporarily locked after 5 consecutive failed login attempts.
- **Rationale**: Prevents brute-force attacks on user accounts.
- **Validation**: The system will track the number of failed login attempts for each user.
- **Error Handling**: After 5 failed attempts, the user account will be deactivated (`is_active = false`) and an administrator will need to reactivate it.
- **Related Requirements**: FR-1

### 7.5. BR-5: Violation-Check-In Association

- **Description**: All recorded violations must be linked to a valid check-in record.
- **Rationale**: Ensures traceability and context for every violation, linking it to a specific student and exam session.
- **Validation**: A foreign key constraint (`check_in_id`) will enforce this relationship at the database level.
- **Error Handling**: The system will prevent the creation of a violation if it is not associated with a valid check-in.
- **Related Requirements**: FR-21

### 7.6. BR-6: Seat Assignment Timing

- **Description**: Seat assignments for an exam can only be created or modified before the exam start time.
- **Rationale**: Prevents changes to the seating plan while an exam is in progress, ensuring stability and fairness.
- **Validation**: The system will check if `current_time < exam.start_time` before allowing any modifications to the seating plan.
- **Error Handling**: An error message will be displayed if a user attempts to modify the seating plan for an active or completed exam.
- **Related Requirements**: FR-11

### 7.7. BR-7: Photo Requirement for Verification

- **Description**: A registered student photo is mandatory for the ML/CV identity verification process to run.
- **Rationale**: The system cannot perform a comparison without a reference photo, which is the basis of the identity verification feature.
- **Validation**: The system will check if the `registered_photo_path` is not NULL or empty for a student before initiating the check-in process.
- **Error Handling**: If a student does not have a registered photo, the system will display a message indicating that automated verification is not possible and will require manual proctor approval.
- **Related Requirements**: FR-13, FR-16

### 7.8. BR-8: Role-Based Report Access

- **Description**: Proctors can only view reports for exams they are assigned to, while Administrators can view reports for all exams.
- **Rationale**: Enforces data privacy and the principle of least privilege, ensuring users can only access data relevant to their duties.
- **Validation**: The system will filter report data based on the user's role and their exam assignments.
- **Error Handling**: An HTTP 403 Forbidden error will be returned if a user attempts to access a report for which they are not authorized.
- **Related Requirements**: FR-2, FR-25

---

## 8. User Interface Requirements

*(This section would typically include wireframes or mockups, but for this project, a simple, intuitive UI is sufficient.)*

---

## 9. Testing & Validation Requirements

*(Refer to the `test-docs/test_cases.md` document for detailed test cases.)*

---

## 10. Glossary

- **Proctor:** An individual who monitors an exam (also known as an Invigilator).
- **Roster:** A list of students registered for an exam.
- **Seating Plan:** A diagram showing where each student is assigned to sit.
- **SSIM:** Structural Similarity Index, an algorithm for measuring image similarity.
- **Violation:** Any breach of exam rules.

---

**End of Document**
