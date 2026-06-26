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

| Attribute     | Description                    | Required | Unique |
| ------------- | ------------------------------- | -------- | ------ |
| User ID       | Unique identifier for each user | Yes      | Yes    |
| Full Name     | User's full name                | Yes      | No     |
| Email Address | User's login email address      | Yes      | Yes    |
| Password Hash | Hashed account password         | Yes      | No     |
| Created At    | Account creation timestamp      | Yes      | No     |
| Updated At    | Last profile update timestamp   | Yes      | No     |

#### 3.1.3 Validation Rules

- Full Name shall not be empty.
- Full Name shall contain between 2 and 100 characters.
- Email Address shall follow a valid email format.
- Email Address shall be unique across all registered users.
- Password Hash shall be securely generated using BCrypt hashing.
- The plain-text password shall never be stored or logged.

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

Permanently deleting a user account removes the associated email address from the system, allowing the same email address to be used for a future registration.

#### 3.1.6 Lifecycle

1. User registers a new account.
2. Account information is securely stored.
3. User authenticates using email and password.
4. User updates profile information when required.
5. User may permanently delete the account, removing all associated financial data.

#### 3.1.7 Constraints

- User ID shall be the Primary Key.
- Email Address shall have a Unique Constraint.
- Password Hash shall always contain the hashed password value, never plain text.
- A user cannot exist without an email address.
- A user cannot exist without a password hash.

#### 3.1.8 Indexes

- Primary Index on User ID.
- Unique Index on Email Address.

#### 3.1.9 Excluded Attributes

The following attributes were considered but intentionally excluded from Version 1 because they are not required by the Software Requirements Specification (SRS):

- Profile Picture
- Phone Number
- Date of Birth
- Address
- Gender
- Time Zone

These attributes may be introduced in future versions if new functional requirements justify their inclusion.

#### 3.1.10 Scope Assumptions

BudgetWise Version 1 is designed for a single-currency environment. All monetary values are assumed to use Indian Rupees (INR). Multi-currency support and user-specific currency preferences are intentionally deferred to a future version of the application.

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

### 3.3 Expense Entity

#### 3.3.1 Purpose

The Expense entity represents a financial transaction where money is spent by a user.

It records all expense-related information required for budgeting, financial analysis, reporting, notification generation, and transaction history.

Every expense belongs to exactly one user and is classified under exactly one expense category.

---

#### 3.3.2 Attributes

| Attribute | Description | Required | Unique |
|-----------|-------------|----------|--------|
| Expense ID | Unique identifier for each expense | Yes | Yes |
| User ID | Owner of the expense | Yes | No |
| Category ID | Expense category | Yes | No |
| Amount | Monetary value of the expense | Yes | No |
| Title | Short description of the expense | Yes | No |
| Description | Detailed notes about the expense | No | No |
| Payment Method | Method used for payment | Yes | No |
| Expense Date | Date the expense occurred | Yes | No |
| Created At | Record creation timestamp | Yes | No |
| Updated At | Last modification timestamp | Yes | No |

---

#### 3.3.3 Validation Rules

- Amount shall be greater than zero.
- Title shall not be empty.
- Title shall contain between 2 and 100 characters.
- Description is optional.
- Payment Method shall be selected from the supported payment methods.
- Expense Date shall be a valid calendar date.
- Every expense shall belong to exactly one user.
- Every expense shall belong to exactly one expense category.

---

#### 3.3.4 Relationships

- Every Expense belongs to exactly one User.
- Every Expense belongs to exactly one Category.
- An Expense may contribute to one Monthly Budget.
- An Expense may trigger zero or more Notifications through business rules.

---

#### 3.3.5 Business Rules

- Users shall only create expenses for their own account.
- Expense amounts shall always be positive.
- Editing an expense shall automatically update budget utilization.
- Editing an expense may trigger new budget notifications.
- Deleting an expense shall automatically recalculate budget utilization.
- Every expense shall be included in financial analytics and reporting.

---

#### 3.3.6 Lifecycle

1. User creates an expense.
2. The system validates the expense.
3. The expense is stored.
4. Budget Service updates budget utilization.
5. Notification Service evaluates threshold rules.
6. Analytics Service updates financial summaries.
7. The expense may later be updated or deleted.

---

#### 3.3.7 Constraints

- Expense ID shall be the Primary Key.
- User ID shall be a mandatory foreign key.
- Category ID shall be a mandatory foreign key.
- Amount shall be greater than zero.
- Expense Date shall be mandatory.

---

#### 3.3.8 Indexes

- Primary Index on Expense ID.
- Index on User ID.
- Index on Category ID.
- Index on Expense Date.
- Composite Index on (User ID, Expense Date).

---

#### 3.3.9 Excluded Attributes

The following attributes were considered but intentionally excluded from Version 1 because they are not required by the Software Requirements Specification (SRS):

- Expense Receipt Image
- Merchant Location
- GPS Coordinates
- Tags
- Attachments
- Recurring Expense Settings

These attributes may be introduced in future versions if new functional requirements justify their inclusion.

---

#### 3.3.10 Scope Assumptions

BudgetWise Version 1 records manual expense entries only.

Recurring expenses, receipt scanning, OCR, geolocation, and attachment management are outside the scope of Version 1.



## 3.4 Income Entity

### 3.4.1 Purpose

The Income entity represents a financial transaction where money is received by a user.

It records all income-related information required for financial tracking, savings analysis, reporting, and transaction history.

Every income record belongs to exactly one user and is classified under exactly one income category.

---

### 3.4.2 Attributes

| Attribute   | Description                              | Required | Unique |
| ----------- | ---------------------------------------- | -------- | ------ |
| Income ID   | Unique identifier for each income record | Yes      | Yes    |
| User ID     | Owner of the income record               | Yes      | No     |
| Category ID | Income category                          | Yes      | No     |
| Amount      | Monetary value of the income             | Yes      | No     |
| Title       | Short description of the income          | Yes      | No     |
| Description | Detailed notes about the income          | No       | No     |
| Income Date | Date the income was received             | Yes      | No     |
| Created At  | Record creation timestamp                | Yes      | No     |
| Updated At  | Last modification timestamp              | Yes      | No     |

---

### 3.4.3 Validation Rules

* Amount shall be greater than zero.
* Title shall not be empty.
* Title shall contain between 2 and 100 characters.
* Description is optional.
* Income Date shall be a valid calendar date.
* Every income record shall belong to exactly one user.
* Every income record shall belong to exactly one income category.

---

### 3.4.4 Relationships

* Every Income belongs to exactly one User.
* Every Income belongs to exactly one Category.
* Every Income contributes to financial analytics and savings calculations.

---

### 3.4.5 Business Rules

* Users shall only create income records for their own account.
* Income amounts shall always be positive.
* Editing an income record shall automatically update financial analytics.
* Deleting an income record shall automatically recalculate financial summaries.
* Every income record shall be included in savings and reporting calculations.
* Income records do not directly affect budget utilization or budget threshold notifications.

---

### 3.4.6 Lifecycle

1. User creates an income record.
2. The system validates the income record.
3. The income record is stored.
4. Analytics Service updates financial summaries.
5. The income record may later be updated or deleted.

---

### 3.4.7 Constraints

* Income ID shall be the Primary Key.
* User ID shall be a mandatory foreign key.
* Category ID shall be a mandatory foreign key.
* Amount shall be greater than zero.
* Income Date shall be mandatory.

---

### 3.4.8 Indexes

* Primary Index on Income ID.
* Index on User ID.
* Index on Category ID.
* Index on Income Date.
* Composite Index on (User ID, Income Date).

---

### 3.4.9 Excluded Attributes

The following attributes were considered but intentionally excluded from Version 1 because they are not required by the Software Requirements Specification (SRS):

* Recurring Income Settings
* Income Attachment
* Employer Details
* Tax Information
* Currency Conversion
* External Payment References

These attributes may be introduced in future versions if new functional requirements justify their inclusion.

---

### 3.4.10 Scope Assumptions

BudgetWise Version 1 records manual income entries only.

Recurring income management, payroll integration, tax calculation, and external financial integrations are outside the scope of Version 1.


## 3.5 MonthlyBudget Entity

### 3.5.1 Purpose

The MonthlyBudget entity represents the overall spending limit defined by a user for a specific calendar month.

It serves as the primary budget against which expense utilization is measured and provides the foundation for category budgets, financial analytics, and budget threshold notifications.

Each user may have only one overall monthly budget for a given calendar month.

---

### 3.5.2 Attributes

| Attribute         | Description                              | Required | Unique |
| ----------------- | ---------------------------------------- | -------- | ------ |
| Monthly Budget ID | Unique identifier for the monthly budget | Yes      | Yes    |
| User ID           | Owner of the monthly budget              | Yes      | No     |
| Budget Month      | Calendar month for the budget            | Yes      | No     |
| Budget Year       | Calendar year for the budget             | Yes      | No     |
| Budget Amount     | Overall monthly budget amount            | Yes      | No     |
| Created At        | Budget creation timestamp                | Yes      | No     |
| Updated At        | Last modification timestamp              | Yes      | No     |

---

### 3.5.3 Validation Rules

* Budget Amount shall be greater than zero.
* Budget Month shall represent a valid calendar month.
* Budget Year shall represent a valid calendar year.
* A user shall have at most one monthly budget for the same month and year.
* Every monthly budget shall belong to exactly one user.

---

### 3.5.4 Relationships

* Every Monthly Budget belongs to exactly one User.
* One Monthly Budget may contain zero or more Category Budgets.
* Budget utilization is calculated using Expense records associated with the same user and calendar month.

---

### 3.5.5 Business Rules

* Users may create one overall budget for each calendar month.
* Users may update the monthly budget at any time.
* Updating a monthly budget shall trigger the Budget Service to recalculate budget utilization.
* The Budget Service shall validate that the sum of all category budgets does not exceed the overall monthly budget.
* Budget utilization calculations shall be performed only by the Budget Service.
* Notification Service shall request budget utilization from the Budget Service when evaluating budget threshold notifications.
* Deleting a monthly budget shall also remove all associated category budgets for the same month.

---

### 3.5.6 Lifecycle

1. User creates a monthly budget.
2. The system validates the budget.
3. The monthly budget is stored.
4. Users may optionally create category budgets.
5. Expense records update budget utilization through the Budget Service.
6. Users may update the monthly budget.
7. Updating the monthly budget triggers recalculation of utilization and notification evaluation.

---

### 3.5.7 Constraints

* Monthly Budget ID shall be the Primary Key.
* User ID shall be a mandatory foreign key.
* Budget Amount shall be greater than zero.
* Budget Month shall be mandatory.
* Budget Year shall be mandatory.
* A unique constraint shall exist on (User ID, Budget Month, Budget Year).

---

### 3.5.8 Indexes

* Primary Index on Monthly Budget ID.
* Index on User ID.
* Composite Index on (User ID, Budget Year, Budget Month).

---

### 3.5.9 Excluded Attributes

The following attributes were considered but intentionally excluded from Version 1 because they are not required by the Software Requirements Specification (SRS):

* Weekly Budgets
* Daily Budgets
* Automatic Budget Carry Forward
* Budget Templates
* Shared Budgets
* Multi-Currency Budgets

These attributes may be introduced in future versions if new functional requirements justify their inclusion.

---

### 3.5.10 Scope Assumptions

BudgetWise Version 1 supports one overall budget per user for each calendar month.

Budget periods always follow calendar months and do not support custom date ranges.


## 3.6 CategoryBudget Entity

### 3.6.1 Purpose

The CategoryBudget entity represents the spending limit assigned to a specific expense category within a user's overall monthly budget.

It enables users to allocate portions of their monthly budget to individual expense categories, allowing more detailed financial planning and spending analysis.

Each category budget belongs to exactly one user, one monthly budget, and one expense category.

---

### 3.6.2 Attributes

| Attribute          | Description                               | Required | Unique |
| ------------------ | ----------------------------------------- | -------- | ------ |
| Category Budget ID | Unique identifier for the category budget | Yes      | Yes    |
| Monthly Budget ID  | Parent monthly budget                     | Yes      | No     |
| User ID            | Owner of the category budget              | Yes      | No     |
| Category ID        | Expense category                          | Yes      | No     |
| Budget Amount      | Spending limit for the category           | Yes      | No     |
| Created At         | Category budget creation timestamp        | Yes      | No     |
| Updated At         | Last modification timestamp               | Yes      | No     |

---

### 3.6.3 Validation Rules

* Budget Amount shall be greater than zero.
* Every category budget shall belong to exactly one user.
* Every category budget shall belong to exactly one monthly budget.
* Every category budget shall reference exactly one expense category.
* Only expense categories may be assigned category budgets.
* A category shall have at most one category budget within the same monthly budget.

---

### 3.6.4 Relationships

* Every Category Budget belongs to exactly one User.
* Every Category Budget belongs to exactly one Monthly Budget.
* Every Category Budget belongs to exactly one Expense Category.

---

### 3.6.5 Business Rules

* Users may optionally create category budgets.
* The sum of all category budgets shall not exceed the corresponding monthly budget.
* This validation shall be performed by the Budget Service.
* Updating a category budget shall trigger recalculation of budget utilization.
* Notification Service shall request updated utilization from the Budget Service after category budget changes.

---

### 3.6.6 Lifecycle

1. User creates a monthly budget.
2. User optionally creates category budgets.
3. The system validates the category budget.
4. The category budget is stored.
5. Users may update or delete category budgets.
6. Budget utilization is recalculated after every change.
7. Deleting a Monthly Budget shall automatically delete all associated Category Budgets.

---

### 3.6.7 Constraints

* Category Budget ID shall be the Primary Key.
* Monthly Budget ID shall be a mandatory foreign key.
* User ID shall be a mandatory foreign key.
* Category ID shall be a mandatory foreign key.
* Budget Amount shall be greater than zero.
* A unique constraint shall exist on (Monthly Budget ID, Category ID).

---

### 3.6.8 Indexes

* Primary Index on Category Budget ID.
* Index on User ID.
* Index on Monthly Budget ID.
* Index on Category ID.

---

### 3.6.9 Excluded Attributes

The following attributes were considered but intentionally excluded from Version 1 because they are not required by the Software Requirements Specification (SRS):

* Budget Priority
* Budget Color
* Automatic Budget Adjustment
* Budget Sharing
* Category Budget Notes

These attributes may be introduced in future versions if new functional requirements justify their inclusion.

---

### 3.6.10 Scope Assumptions

Category budgets are optional.

Users may choose to use only an overall monthly budget without creating category budgets.


## 3.7 Notification Entity

### 3.7.1 Purpose

The Notification entity represents system-generated alerts that inform users about important financial events.

Notifications are generated based on business rules, such as budget threshold crossings, and are stored to provide users with a historical record of important financial events.

Every notification belongs to exactly one user.

---

### 3.7.2 Attributes

| Attribute         | Description                                      | Required | Unique |
| ----------------- | ------------------------------------------------ | -------- | ------ |
| Notification ID   | Unique identifier for each notification          | Yes      | Yes    |
| User ID           | Owner of the notification                        | Yes      | No     |
| Notification Type | Type of notification                             | Yes      | No     |
| Title             | Notification title                               | Yes      | No     |
| Message           | Notification content                             | Yes      | No     |
| Is Read           | Indicates whether the notification has been read | Yes      | No     |
| Created At        | Notification creation timestamp                  | Yes      | No     |

---

### 3.7.3 Validation Rules

* Every notification shall belong to exactly one user.
* Notification Type shall be one of the supported system notification types.
* Title shall not be empty.
* Message shall not be empty.
* Is Read shall default to **False**.

---

### 3.7.4 Relationships

* Every Notification belongs to exactly one User.
* Notifications are generated by business events originating from the Budget Service.
* Notification Service manages notification creation and read status.

---

### 3.7.5 Business Rules

* Notifications shall be generated automatically by the system.
* Users shall not manually create notifications.
* Users may mark notifications as read.
* Notification Service shall request budget utilization from the Budget Service before evaluating threshold rules.
* Duplicate threshold notifications for the same user, threshold, category, and month shall not be generated.
* Notifications remain stored for historical reference even if budget values change later.

---

### 3.7.6 Lifecycle

1. A financial event occurs (expense added, updated, deleted, or budget created or updated).
2. Notification Service requests the current budget utilization from the Budget Service.
3. Notification Service evaluates the notification rules.
4. If the notification conditions are satisfied and no duplicate notification exists, a notification is created.
5. The user views the notification.
6. The user may mark the notification as read.

---

### 3.7.7 Constraints

* Notification ID shall be the Primary Key.
* User ID shall be a mandatory foreign key.
* Title shall be mandatory.
* Message shall be mandatory.
* Is Read shall default to False.

---

### 3.7.8 Indexes

* Primary Index on Notification ID.
* Index on User ID.
* Index on Created At.
* Composite Index on (User ID, Is Read).

---

### 3.7.9 Excluded Attributes

The following attributes were considered but intentionally excluded from Version 1 because they are not required by the Software Requirements Specification (SRS):

* Push Notifications
* Email Notifications
* SMS Notifications
* Notification Priority
* Notification Scheduling
* Notification Categories

These attributes may be introduced in future versions if new functional requirements justify their inclusion.

---

### 3.7.10 Scope Assumptions

BudgetWise Version 1 supports only in-application notifications.

Notifications are generated automatically based on system-defined business rules and are not configurable by users.
