# Database Design

## 1. Overview

The BudgetWise database is responsible for storing and managing all persistent application data required by the system.

It supports user authentication, income tracking, expense management, budget planning, financial analytics, transaction history, and application notifications.

The database is designed to ensure data integrity, maintain user privacy, minimize redundancy through normalization, and provide a scalable foundation for future enhancements.

---

## 2. Database Architecture

### 2.1 Design Principles

The BudgetWise database is designed according to the following principles:

- Every financial record belongs to exactly one user.
- The database stores facts, while business calculations are performed by the application services.
- Historical records are preserved whenever possible to maintain financial consistency.
- The database is normalized to reduce redundancy and improve maintainability.
- Referential integrity is enforced through primary and foreign key relationships.

---

### 2.2 Database Entities

BudgetWise Version 1 consists of the following entities:

- User
- Category
- Expense
- Income
- MonthlyBudget
- CategoryBudget
- Notification

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


| Attribute       | Description                       | Required | Unique |
| --------------- | --------------------------------- | -------- | ------ |
| User ID         | Unique identifier for each user   | Yes      | Yes    |
| Full Name       | User's full name                  | Yes      | No     |
| Email Address   | User's login email address        | Yes      | Yes    |
| Password        | Hashed account password           | Yes      | No     |
| Profile Picture | Optional profile image            | No       | No     |
| Created At      | Account creation timestamp        | Yes      | No     |
| Updated At      | Last profile update timestamp     | Yes      | No     |

#### 3.1.3 Validation Rules


- Full Name shall not be empty.
- Full Name shall contain between 2 and 100 characters.
- Email Address shall follow a valid email format.
- Email Address shall be unique across all registered users.
- Password shall be securely stored using BCrypt hashing.
- Password shall never be stored in plain text.
- Profile Picture is optional.

#### 3.1.4 Relationships



A User may own zero or more Expense records.

A User may own zero or more Income records.

A User may own zero or more Monthly Budget records.

A User may own zero or more Category Budget records.

A User may own zero or more Notification records.

A User may own zero or more custom Category records.

#### 3.1.5 Business Rules



Every registered user shall have a unique account.

Every financial record stored within the application shall belong to exactly one user.

Users shall only access their own financial information.

Deleting a user account shall permanently remove all associated financial records.

User passwords shall always remain encrypted.


#### 3.1.6 Lifecycle

1. User registers a new account.

2. Account information is securely stored.

3. User authenticates using email and password.

4. User updates profile information when required.

5. User may permanently delete the account, removing all associated financial data.

#### 3.1.7 Constraints

- User ID shall be the Primary Key.
- Email Address shall have a Unique Constraint.
- Password shall always contain the hashed password value.
- A user cannot exist without an email address.
- A user cannot exist without a password.

#### 3.1.8 Indexes

- Primary Index on User ID.
- Unique Index on Email Address.

---

### 3.2 Category Entity

#### 3.2.1 Purpose

The Category entity represents the classification assigned to financial transactions within BudgetWise.

Categories organize income and expense records into meaningful groups, enabling users to analyze spending patterns, generate reports, and monitor budget utilization.

The system provides a predefined set of default categories while allowing users to create custom categories that meet their personal financial needs.

---

#### 3.2.2 Attributes

| Attribute        | Description                                             | Required | Unique |
| --------------- | ------------------------------------------------------- | -------- | ------ |
| Category ID      | Unique identifier for each category                     | Yes      | Yes    |
| Category Name    | Name of the category                                    | Yes      | No*    |
| Category Type    | Indicates whether the category is for Income or Expense | Yes      | No     |
| Default Category | Indicates whether the category is system-defined        | Yes      | No     |
| User ID          | Owner of the custom category (null for default categories) | No    | No     |
| Created At       | Category creation timestamp                             | Yes      | No     |
| Updated At       | Last modification timestamp                             | Yes      | No     |

Category names must be unique within their owner and type. A user cannot create two custom expense categories with the same name, but different users may use the same category name.

---

#### 3.2.3 Validation Rules

- Category Name shall not be empty.
- Category Name shall contain between 2 and 50 characters.
- Category Type shall be either **Income** or **Expense**.
- Default categories shall be created by the system.
- Custom categories shall belong to exactly one user.
- A user shall not create duplicate category names within the same category type.
- A user shall not create multiple categories with the same name within the same Category Type.
- The same category name may exist across different Category Types.
- Different users may create custom categories with identical names.


---

#### 3.2.4 Relationships

- A Category may be referenced by zero or more Expense records.
- A Category may be referenced by zero or more Category Budget records.
- A custom Category belongs to exactly one User.
- Default categories do not belong to any individual user.

---

#### 3.2.5 Business Rules

- BudgetWise shall provide a predefined set of default categories.
- Users may create custom categories.
- Users may edit their own custom categories.
- Users shall not modify default categories.
- Users shall not delete default categories.
When a custom category is deleted, all associated financial records shall be reassigned to the system-defined **"Others"** category that has the same **Category Type** as the deleted category.
- Expense categories shall be reassigned to the default **Others (Expense)** category.
- Income categories shall be reassigned to the default **Others (Income)** category.
The reassignment shall occur before the custom category is permanently removed.


---

#### 3.2.6 Lifecycle

- The system creates default categories during application initialization.
- A user may create a custom category.
- A user may update a custom category.
- A user may delete a custom category.
- Before deleting a custom category, the system reassigns all associated financial records to the corresponding default **Others** category of the same Category Type.


---

#### 3.2.7 Constraints

- Category ID shall be the Primary Key.
- Category Type shall be mandatory.
- Every custom category shall reference exactly one User.
- Default categories shall not reference a User.
- Category names shall be unique within the same owner and category type.

---

#### 3.2.8 Indexes

- Primary Index on Category ID.
- Index on User ID.
- Composite Index on (User ID, Category Type, Category Name).

---

#### 3.2.9 Excluded Attributes

The following attributes were considered but intentionally excluded from Version 1 because they are not required by the Software Requirements Specification (SRS):

- Category Icon
- Category Color
- Category Description
- Display Order

These attributes may be introduced in future versions if new functional requirements justify their inclusion.

---

#### 3.2.10 Scope Assumptions

BudgetWise Version 1 provides a predefined set of system categories together with user-defined custom categories.

Categories are intended for financial organization only and do not support visual customization such as icons or colors in Version 1.

The system maintains separate default **Others** categories for **Income** and **Expense** transactions to preserve category type integrity during automatic reassignment.

