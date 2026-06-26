# BudgetWise API Documentation

## 1. Overview

The BudgetWise REST API provides secure endpoints for user authentication, financial transaction management, budgeting, notifications, and analytics.

The API follows RESTful architecture and communicates using JSON over HTTPS.

All protected endpoints require a valid JWT access token.

The API is stateless, meaning each request contains all information required to process it.

---

# 2. API Standards

## 2.1 Base URL

```
/api
```

## 2.2 Content Type

```
application/json
```

## 2.3 Authentication

Protected endpoints require:

```
Authorization: Bearer <JWT_TOKEN>
```

## 2.4 HTTP Methods

| Method | Purpose            |
| ------ | ------------------ |
| GET    | Retrieve resources |
| POST   | Create resources   |
| PUT    | Update resources   |
| DELETE | Delete resources   |

## 2.5 Date Format

ISO-8601

Example:

```
2026-06-01
```

## 2.6 Currency

Version 1 assumes all monetary values are stored in INR.

---

# 3. Authentication API

| Method | Endpoint       | Description               |
| ------ | -------------- | ------------------------- |
| POST   | /auth/register | Register a new user       |
| POST   | /auth/login    | Authenticate user         |
| POST   | /auth/logout   | Logout authenticated user |

---

# 4. User API

| Method | Endpoint       | Description                |
| ------ | -------------- | -------------------------- |
| GET    | /users/profile | Retrieve user profile      |
| PUT    | /users/profile | Update profile information |
| DELETE | /users/profile | Permanently delete account |

---

# 5. Category API

| Method | Endpoint                 | Description             |
| ------ | ------------------------ | ----------------------- |
| GET    | /categories              | Retrieve all categories |
| POST   | /categories              | Create custom category  |
| PUT    | /categories/{categoryId} | Update category         |
| DELETE | /categories/{categoryId} | Delete category         |

### Business Rules

* Default categories cannot be modified.
* Default categories cannot be deleted.
* Custom categories belong only to the authenticated user.
* Before deleting a custom category, all associated transactions shall be reassigned to the corresponding default **Others** category of the same Category Type.

---

# 6. Expense API

| Method | Endpoint              | Description              |
| ------ | --------------------- | ------------------------ |
| GET    | /expenses             | Retrieve expenses        |
| GET    | /expenses/{expenseId} | Retrieve expense details |
| POST   | /expenses             | Create expense           |
| PUT    | /expenses/{expenseId} | Update expense           |
| DELETE | /expenses/{expenseId} | Delete expense           |

### Business Effects

Creating, updating, or deleting an expense automatically:

* Recalculates budget utilization.
* Triggers notification evaluation.
* Updates dashboard analytics.

---

# 7. Income API

| Method | Endpoint           | Description             |
| ------ | ------------------ | ----------------------- |
| GET    | /income            | Retrieve income records |
| GET    | /income/{incomeId} | Retrieve income details |
| POST   | /income            | Create income           |
| PUT    | /income/{incomeId} | Update income           |
| DELETE | /income/{incomeId} | Delete income           |

### Business Effects

Creating, updating, or deleting income records updates financial analytics and savings calculations.

Income records do not affect budget utilization.

---

# 8. Monthly Budget API

| Method | Endpoint                    | Description             |
| ------ | --------------------------- | ----------------------- |
| GET    | /monthly-budgets            | Retrieve monthly budget |
| POST   | /monthly-budgets            | Create monthly budget   |
| PUT    | /monthly-budgets/{budgetId} | Update monthly budget   |
| DELETE | /monthly-budgets/{budgetId} | Delete monthly budget   |

### Business Rules

* One monthly budget per user per calendar month.
* Updating a monthly budget recalculates utilization.
* Deleting a monthly budget automatically removes associated category budgets.

---

# 9. Category Budget API

| Method | Endpoint                             | Description               |
| ------ | ------------------------------------ | ------------------------- |
| GET    | /category-budgets                    | Retrieve category budgets |
| POST   | /category-budgets                    | Create category budget    |
| PUT    | /category-budgets/{categoryBudgetId} | Update category budget    |
| DELETE | /category-budgets/{categoryBudgetId} | Delete category budget    |

### Business Rules

* Category budgets are optional.
* The total of all category budgets shall not exceed the monthly budget.
* Validation is performed by the Budget Service.

---

# 10. Notification API

| Method | Endpoint                             | Description               |
| ------ | ------------------------------------ | ------------------------- |
| GET    | /notifications                       | Retrieve notifications    |
| PUT    | /notifications/{notificationId}/read | Mark notification as read |
| DELETE | /notifications/{notificationId}      | Delete notification       |

### Business Rules

* Notifications are generated automatically.
* Duplicate threshold notifications are not created.
* Notifications remain stored as historical events.

---

# 11. Analytics API

| Method | Endpoint                        | Description               |
| ------ | ------------------------------- | ------------------------- |
| GET    | /analytics/dashboard            | Dashboard summary         |
| GET    | /analytics/expense-distribution | Expense distribution      |
| GET    | /analytics/monthly-summary      | Monthly financial summary |

The Analytics Service is the canonical source for all business calculations exposed to the frontend.

The frontend shall display analytics returned by these endpoints and shall not calculate business figures independently.

---

# 12. Standard Response Format

Successful Response

```json
{
  "success": true,
  "message": "Operation completed successfully.",
  "data": {}
}
```

Error Response

```json
{
  "success": false,
  "message": "Validation failed.",
  "errors": [
    {
      "field": "email",
      "error": "Email already exists."
    }
  ]
}
```

---

# 13. HTTP Status Codes

| Status | Meaning               |
| ------ | --------------------- |
| 200    | OK                    |
| 201    | Created               |
| 204    | No Content            |
| 400    | Bad Request           |
| 401    | Unauthorized          |
| 403    | Forbidden             |
| 404    | Not Found             |
| 409    | Conflict              |
| 500    | Internal Server Error |

---

# 14. API Design Principles

The BudgetWise API follows these design principles:

* RESTful resource-oriented endpoints.
* Stateless request processing.
* JWT-secured authentication.
* JSON request and response payloads.
* Business logic resides in backend services.
* The frontend never performs business calculations.
* API endpoints expose business capabilities while preserving service ownership.
* Consistent request and response formats across all endpoints.
