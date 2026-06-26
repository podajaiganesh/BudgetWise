# Database Design

## 1. Overview

The BudgetWise database is responsible for storing and managing all persistent application data required by the system.

It supports user authentication, income tracking, expense management, budget planning, financial analytics, transaction history, and application notifications.

The database is designed to ensure data integrity, maintain user privacy, minimize redundancy through normalization, and provide a scalable foundation for future enhancements.

---

## 2. Database Architecture

### 2.1 Design Principles

The BudgetWise database is designed according to the following principles:

* Every financial record belongs to exactly one user.
* The database stores facts, while business calculations are performed by the application services.
* Historical records are preserved whenever possible to maintain financial consistency.
* The database is normalized to reduce redundancy and improve maintainability.
* Referential integrity is enforced through primary and foreign key relationships.

---

### 2.2 Database Entities

BudgetWise Version 1 consists of the following entities:

* User
* Category
* Expense
* Income
* MonthlyBudget
* CategoryBudget
* Notification

These entities are sufficient to implement all Version 1 functional requirements defined in the Software Requirements Specification (SRS).

Each entity represents a distinct business concept within the application and has a clearly defined responsibility.

---

### 2.3 Entity Relationships

The User entity is the root entity of the database model.

Every expense, income record, monthly budget, category budget, notification, and custom category belongs to exactly one user.

Expenses are associated with a single category.

Category budgets are associated with both a monthly budget and a category.

Notifications represent historical events generated from changes in financial data and remain associated with the user for future reference.

These relationships ensure data integrity, user isolation, and efficient retrieval of financial information.

---

## 3. Entity Specifications

### 3.1 User Entity

#### 3.1.1 Purpose

The User entity represents a registered user of the BudgetWise application.

It is the root entity of the database model and establishes ownership of all user-specific financial information, including expenses, income records, budgets, notifications, and custom categories.

Every financial record stored by the system belongs to exactly one user, ensuring data isolation, privacy, and secure access control throughout the application.

#### 3.1.2 Attributes

*To be completed.*

#### 3.1.3 Validation Rules

*To be completed.*

#### 3.1.4 Relationships

*To be completed.*

#### 3.1.5 Business Rules

*To be completed.*

#### 3.1.6 Lifecycle

*To be completed.*

#### 3.1.7 Constraints

*To be completed.*

#### 3.1.8 Indexes

*To be completed.*

---

### 3.2 Category Entity

*To be completed.*

---

### 3.3 Expense Entity

*To be completed.*

---

### 3.4 Income Entity

*To be completed.*

---

### 3.5 Monthly Budget Entity

*To be completed.*

---

### 3.6 Category Budget Entity

*To be completed.*

---

### 3.7 Notification Entity

*To be completed.*
