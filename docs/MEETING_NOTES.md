# BudgetWise Meeting Notes

## Purpose

This document records the major architectural and design decisions made during the planning of BudgetWise Version 1.

It serves as an Architectural Decision Log (ADL), documenting what was decided, why it was chosen, and its current status. The goal is to preserve the rationale behind key design decisions for future development, maintenance, and project discussions.

---

# Meeting 1 – Project Architecture

## Decision

Adopt a layered architecture consisting of:

* React Frontend
* Spring Boot Backend
* MySQL Database

## Reason

* Clear separation of responsibilities.
* Easy maintenance and scalability.
* Follows industry-standard application architecture.

## Status

**Approved**

---

# Meeting 2 – Database Design

## Decision

Separate Monthly Budget and Category Budget into two entities.

## Reason

* Supports both overall monthly budgets and optional category budgets.
* Simplifies business logic.
* Improves database normalization.

## Status

**Approved**

---

# Meeting 3 – Category Design

## Decision

Maintain both system-defined default categories and user-created custom categories.

## Reason

* Users receive useful categories immediately after registration.
* Users can customize categories without affecting system defaults.

## Status

**Approved**

---

# Meeting 4 – Category Deletion

## Decision

Deleting a custom category shall never delete associated financial records.

Before deletion, all associated records shall be reassigned to the default **Others** category with the same **Category Type**.

## Reason

Preserves historical financial records while allowing users to manage custom categories safely.

## Status

**Approved**

---

# Meeting 5 – Income and Expense Design

## Decision

Maintain separate Income and Expense entities.

## Reason

* Different business rules.
* Different reporting requirements.
* Cleaner domain model.
* Simpler validation and analytics.

## Status

**Approved**

---

# Meeting 6 – Notification Design

## Decision

Store notifications as historical events instead of generating them dynamically.

## Reason

* Preserves notification history.
* Prevents duplicate notifications.
* Improves traceability and user experience.

## Status

**Approved**

---

# Meeting 7 – Budget Utilization

## Decision

Budget utilization shall be calculated only by the Budget Service.

The Notification Service shall request utilization data from the Budget Service instead of calculating it independently.

## Reason

Ensures a single canonical implementation of budget calculations.

## Status

**Approved**

---

# Meeting 8 – Business Rule Ownership

## Decision

Every business rule shall have exactly one owner.

## Reason

Avoid duplicate implementations across services and maintain consistent business behavior.

## Status

**Approved**

---

# Meeting 9 – Backend Responsibilities

## Decision

The frontend shall never perform business calculations.

All financial calculations and business rules shall be executed only by backend services.

## Reason

Ensures consistency, accuracy, and maintainability across all clients.

## Status

**Approved**

---

# Meeting 10 – User Account Deletion

## Decision

Deleting a user account permanently removes the account and all associated financial data.

## Reason

Supports the project's permanent deletion requirement and simplifies data lifecycle management.

## Status

**Approved**

---

# Meeting 11 – Password Security

## Decision

* Passwords shall never be stored in plain text.
* Passwords shall always be securely hashed using BCrypt before storage.
* Password changes require the user to provide the current password before setting a new password.

## Reason

Improves account security and follows authentication best practices.

## Status

**Approved**

---

# Meeting 12 – API Design

## Decision

The API shall follow RESTful principles using JWT-based authentication.

Each endpoint represents a business capability while keeping business logic within backend services.

## Reason

Provides a secure, consistent, and maintainable API architecture.

## Status

**Approved**

---

# Meeting 13 – Documentation Standards

## Decision

All project documentation shall remain consistent across:

* README
* Software Requirements Specification (SRS)
* Database Design
* API Documentation
* UI Documentation
* Source Code

Whenever a design decision changes, every affected document shall be updated before the change is considered complete.

## Reason

Prevents documentation drift and maintains a single, reliable understanding of the system.

## Status

**Approved**

---

# Meeting 14 – Development Workflow

## Decision

Implementation shall begin only after planning and documentation reach an acceptable level of completeness.

Development order:

1. Database Schema
2. Backend Development
3. Frontend Development
4. Testing
5. Deployment

## Reason

Reduces redesign during implementation and improves development efficiency.

## Status

**Approved**

---

# Meeting 15 – Documentation Lock Policy

## Decision

Documentation shall be considered locked once reviewed and approved.

Locked documentation shall only be modified when:

* A genuine design flaw is discovered.
* A Software Requirements Specification (SRS) requirement changes.
* An implementation constraint requires a documented design update.

Preference changes alone shall not justify redesign.

## Reason

Prevents unnecessary redesign while allowing justified improvements.

## Status

**Approved**

---

# Current Project Status

## Documentation

Completed:

* README.md
* SRS.md
* DATABASE.md
* API.md
* UI.md
* ROADMAP.md
* MEETING_NOTES.md

Overall Status:

**In Review**

---

# Next Milestones

1. Final Cross-Document Consistency Review
2. ER Diagram
3. Database Schema (`schema.sql`)
4. Seed Data (`seed_data.sql`)
5. Spring Boot Backend Development
6. React Frontend Development
7. Testing and Quality Assurance
8. Production Deployment
