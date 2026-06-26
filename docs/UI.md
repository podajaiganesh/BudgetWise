# BudgetWise UI Documentation

## 1. Overview

The BudgetWise user interface is designed to provide a simple, intuitive, and responsive experience for managing personal finances.

The interface enables users to register, authenticate, manage financial records, create budgets, monitor spending, receive notifications, and view financial analytics through a clean and consistent workflow.

The user interface communicates exclusively with the BudgetWise REST API and does not perform business calculations locally. All financial calculations, validations, and business rules are handled by the backend services.

---

# 2. Design Principles

The BudgetWise user interface follows these principles:

* Simplicity over complexity.
* Consistent navigation across all screens.
* Responsive design for desktop, tablet, and mobile devices.
* Clear visual feedback for every user action.
* Client-side validation for improved user experience.
* Business calculations are performed only by the backend.
* Consistent layout and component styling throughout the application.
* Error messages returned by the API are displayed clearly to the user.

---

# 3. Application Navigation

After successful authentication, users can navigate through the following application modules:

* Dashboard
* Expense Management
* Income Management
* Category Management
* Budget Management
* Notification Center
* User Settings

Navigation is available through the main application menu.

Unauthenticated users may access only the Login and Registration screens.

---

# 4. Login Screen

## Purpose

Authenticate an existing user.

## Components

* Email Address
* Password
* Login Button
* Register Link

## User Actions

* Login
* Navigate to Registration

## API Used

POST /api/auth/login

---

# 5. Registration Screen

## Purpose

Create a new BudgetWise account.

## Components

* Full Name
* Email Address
* Password
* Register Button
* Login Link

## User Actions

* Register
* Navigate to Login

## API Used

POST /api/auth/register

---

# 6. Dashboard

## Purpose

Provide a complete financial overview for the authenticated user.

## Components

* Total Income
* Total Expenses
* Total Savings
* Monthly Budget
* Remaining Budget
* Budget Utilization
* Expense Distribution Chart
* Recent Transactions
* Recent Notifications

## User Actions

* Navigate to Expense Management
* Navigate to Income Management
* Navigate to Budget Management
* View Notifications

## API Used

GET /api/analytics/dashboard

GET /api/analytics/expense-distribution

GET /api/notifications

---

# 7. Expense Management

## Purpose

Manage user expense records.

## Components

* Expense List
* Search
* Filters
* Add Expense Button
* Edit Expense Button
* Delete Expense Button

## User Actions

* View Expenses
* Create Expense
* Update Expense
* Delete Expense

Expense forms display only **Expense** categories.

## API Used

GET /api/expenses

GET /api/expenses/{expenseId}

POST /api/expenses

PUT /api/expenses/{expenseId}

DELETE /api/expenses/{expenseId}

---

# 8. Income Management

## Purpose

Manage user income records.

## Components

* Income List
* Search
* Filters
* Add Income Button
* Edit Income Button
* Delete Income Button

## User Actions

* View Income
* Create Income
* Update Income
* Delete Income

Income forms display only **Income** categories.

## API Used

GET /api/income

GET /api/income/{incomeId}

POST /api/income

PUT /api/income/{incomeId}

DELETE /api/income/{incomeId}

---

# 9. Category Management

## Purpose

Manage custom financial categories.

## Components

* Category List
* Category Name
* Category Type (Income / Expense)
* Add Category Button
* Edit Category Button
* Delete Category Button

Default categories are displayed as read-only and cannot be modified or deleted.

## User Actions

* Create Category
* Update Category
* Delete Category

## API Used

GET /api/categories

POST /api/categories

PUT /api/categories/{categoryId}

DELETE /api/categories/{categoryId}

---

# 10. Budget Management

## Purpose

Manage overall monthly budgets and optional category budgets.

## Components

* Monthly Budget
* Category Budget List
* Budget Progress
* Budget Utilization
* Add Category Budget Button
* Edit Category Budget Button
* Delete Category Budget Button

## User Actions

* Create Monthly Budget
* Update Monthly Budget
* Delete Monthly Budget
* Create Category Budget
* Update Category Budget
* Delete Category Budget

## Validation Feedback

If the Budget Service rejects a category budget because the total allocated category budgets exceed the monthly budget, the user interface shall display the validation message returned by the API and prevent the changes from being saved.

## API Used

GET /api/monthly-budgets

POST /api/monthly-budgets

PUT /api/monthly-budgets/{budgetId}

DELETE /api/monthly-budgets/{budgetId}

GET /api/category-budgets

POST /api/category-budgets

PUT /api/category-budgets/{categoryBudgetId}

DELETE /api/category-budgets/{categoryBudgetId}

---

# 11. Notification Center

## Purpose

Display system-generated financial notifications.

## Components

* Notification List
* Notification Status
* Mark as Read Button
* Delete Notification Button

## User Actions

* View Notifications
* Mark Notification as Read
* Delete Notification

## API Used

GET /api/notifications

PUT /api/notifications/{notificationId}/read

DELETE /api/notifications/{notificationId}

---

# 12. User Settings

## Purpose

Allow users to manage their account information and account security.

### 12.1 Profile Management

#### Components

* Full Name
* Email Address
* Save Changes Button

#### User Actions

* Update Full Name
* Update Email Address

#### API Used

GET /api/users/profile

PUT /api/users/profile

---

### 12.2 Change Password

#### Components

* Current Password
* New Password
* Confirm New Password
* Change Password Button

#### User Actions

* Change Password

The current password must be provided before a new password can be saved.

#### API Used

PUT /api/users/password

---

### 12.3 Delete Account

#### Components

* Delete Account Button
* Confirmation Dialog

#### User Actions

* Permanently Delete Account

#### API Used

DELETE /api/users/profile

---

# 13. Responsive Design

BudgetWise Version 1 supports responsive layouts for:

* Desktop Computers
* Tablet Devices
* Mobile Devices

The interface automatically adapts to different screen sizes while maintaining consistent functionality and usability.

---

# 14. UI Validation Rules

The user interface performs client-side validation to improve user experience before sending requests to the backend.

Validation includes:

* Required field validation.
* Valid email format validation.
* Password length validation.
* Positive monetary value validation.
* Mandatory category selection.
* Expense forms display only Expense categories.
* Income forms display only Income categories.

Client-side validation improves usability but does not replace backend validation.

All business validation remains the responsibility of the backend services.
