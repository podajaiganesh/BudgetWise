# Software Requirements Specification
## BudgetWise — Personal Finance and Budget Planning Application

---

## 1. Introduction

### 1.1 Purpose

BudgetWise is a personal finance and budget planning application built to help users understand where their money actually goes, not just record that they spent it.

Most budgeting tools stop at logging transactions. BudgetWise goes further: it tracks income and expenses, monitors spending against a monthly budget, and surfaces calculated insights — such as month-over-month category changes and overspending trends — so users can recognize spending patterns before money runs out, rather than after.

This document defines the functional and non-functional requirements for Version 1 of BudgetWise.

### 1.2 Scope

BudgetWise is a web-based personal finance and budget planning application that allows users to manage their financial activity from a single platform.

Version 1 covers: income and expense recording, categorization, monthly and category-wise budget management, budget threshold alerts, transaction history with search/filter/sort, and a dashboard with visual spending analytics.

Advanced features — including multi-user trip/group expense tracking, receipt scanning, bank account integration, and AI-driven recommendations — are explicitly out of scope for Version 1 and are deferred to future versions.

### 1.3 Target Users

BudgetWise is designed primarily for college students managing a monthly allowance or scholarship, and secondarily for young professionals managing their first salary or general personal income.

Because the primary audience receives fixed, irregular-cycle income (allowance, scholarship) rather than fixed-date salary, Version 1 budget cycles are aligned to the calendar month (see 3.5). The application requires no financial or technical expertise to use.

---

## 2. Overall Description

### 2.1 Product Perspective

BudgetWise is a standalone, full-stack web application. It is not an extension of an existing product and does not integrate with banks or third-party financial services in Version 1.

The system follows a three-tier architecture: a React frontend, a Spring Boot REST API backend, and a MySQL database.

### 2.2 Product Functions

Version 1 of BudgetWise provides:

- User registration and secure authentication (JWT-based).
- User profile management.
- Income and expense recording, editing, and deletion.
- Transaction categorization, including custom categories.
- Monthly and category-wise budget creation and tracking.
- Budget threshold alerts and overspending warnings.
- A dashboard with spending visualizations and calculated insights.
- Transaction history with search, filter, and sort.

### 2.3 User Characteristics

Users are assumed to have basic computer or smartphone literacy. No financial training or technical knowledge is required. The interface prioritizes simplicity and clarity over configurability.

### 2.4 Assumptions and Dependencies

The system assumes users manually enter accurate financial data. BudgetWise does not connect to bank accounts or verify entered amounts against any external source in Version 1; the accuracy of all dashboard insights and reports is entirely dependent on the accuracy of user-entered data.

BudgetWise requires a modern web browser and an active internet connection. The application depends on a MySQL database for persistence and a Spring Boot backend for business logic and request processing.

---

## 3. Functional Requirements

### 3.1 User Authentication

- The system shall allow users to register an account using basic personal information.
- The system shall authenticate users using their email address and password.
- The system shall securely store user credentials using BCrypt password hashing; passwords shall never be stored in plain text.
- The system shall issue a JWT upon successful login and use it to authorize access to protected resources.
- The system shall allow users to log out, invalidating their active session.
- The system shall reject access attempts to protected resources from unauthenticated or unauthorized requests.

### 3.2 User Profile Management

- The system shall allow users to view their profile information.
- The system shall allow users to update their name, email address, and profile picture.
- The system shall allow users to change their password after re-authenticating.
- The system shall isolate each user's financial data so that no user can access another user's records.
- The system shall allow users to permanently delete their account and all associated financial data upon confirmed request.

### 3.3 Expense Management

- The system shall allow users to record an expense with amount, category, date, payment method, and an optional note.
- The system shall allow users to edit and delete existing expense records.
- The system shall require user confirmation before permanently deleting an expense record.
- The system shall validate that the expense amount is greater than zero before saving.
- The system shall reject future-dated expense entries.
- The system shall organize expenses into predefined categories (Food, Transportation, Shopping, Bills, Entertainment, Healthcare, Education, Others) and allow user-defined custom categories.
- The system shall display expenses in chronological order by default.
- The system shall allow users to search and filter expenses by category, date range, amount, and payment method.
- The system shall allow users to sort expenses by date, amount, or category.
- The system shall calculate total expenses for a selected day, week, month, and year.
- The system shall display total expenditure per category for a selected period.

### 3.4 Income Management

- The system shall allow users to record income with amount, source, date, and an optional note.
- The system shall allow users to edit and delete existing income records.
- The system shall require user confirmation before permanently deleting an income record.
- The system shall validate that the income amount is greater than zero before saving.
- The system shall reject future-dated income entries.
- The system shall support predefined income sources (Salary, Allowance, Scholarship, Freelancing, Business, Investments, Others) and allow user-defined custom sources.
- The system shall calculate total income for a selected day, week, month, and year.

### 3.5 Budget Management

- The system shall allow users to set an overall monthly budget and, optionally, category-wise budgets.
- The system shall allow users to disable category-wise budgets and rely solely on the overall monthly budget if preferred.
- **Budget periods shall align to the fixed calendar month** (the 1st through the last calendar day of each month). Custom or rolling budget cycles (e.g., salary-date-based) are out of scope for Version 1.
- The system shall calculate and display the remaining budget in real time as expenses are recorded.
- The system shall display the percentage of budget utilized at all times.
- The system shall notify users when spending reaches 50%, 75%, 90%, and 100% of the budget.
- The system shall notify users when category-specific spending exceeds its allocated category budget.
- When a user modifies a budget mid-month, the system shall recalculate utilization and threshold status against the new budget value immediately; previously triggered alerts for thresholds no longer met under the revised budget shall not be re-shown.
- At the start of each new calendar month, the system shall reset current-month tracking while retaining all prior months' budget and expense data for historical comparison.

### 3.6 Dashboard and Analytics

- The system shall display total income, total expenses, remaining budget, and net savings for the selected month.
- The system shall display a pie chart of expense distribution by category.
- The system shall display a comparison of income versus expenses for the selected month.
- The system shall display monthly spending trends using prior months' data.
- The system shall identify and display the single highest-spending category for the selected month.
- The system shall display the percentage contribution of each category to total monthly spending.
- The system shall display the total number of transactions recorded in the selected month.
- The system shall display average daily expenditure for the selected month.
- The system shall compare current month spending against the previous month.
- The system shall update all dashboard figures automatically whenever a financial record is added, edited, or deleted.

### 3.7 Transaction History and Reports

- The system shall maintain a complete, immutable-by-default history of all income and expense transactions.
- The system shall allow keyword search across transactions.
- The system shall allow filtering by category, amount, payment method, and date range.
- The system shall allow users to view full details of any individual transaction.
- The system shall allow users to review financial records from any previous month.
- The system shall generate a monthly summary of income, expenses, and net savings.

### 3.8 Notifications and Budget Alerts

- The system shall display budget threshold notifications (50%, 75%, 90%, 100%) within the dashboard.
- The system shall display category-specific overspending notifications within the dashboard.
- The system shall allow users to dismiss notifications after viewing them.

---

## 4. Non-Functional Requirements

### 4.1 Performance

- The system shall respond to standard user requests within 2 seconds under normal load on the target deployment environment.
- Dashboard statistics shall be calculated efficiently to avoid noticeable delay when switching between months.

### 4.2 Security

- The system shall hash all passwords using BCrypt; passwords shall never be stored or logged in plain text.
- The system shall require authentication for all endpoints except registration and login.
- The system shall authorize users to access only their own financial data.
- The system shall validate all user input on both client and server before processing.

### 4.3 Reliability

- The system shall ensure financial data is committed to the database only after successful validation, preventing partial or corrupted records.
- The system shall maintain consistency between dashboard figures and underlying transaction records at all times.

### 4.4 Usability

- The application shall provide a clean, minimal interface consistent with the project's design philosophy (no unnecessary visual complexity).
- The application shall require no financial or technical background to operate.
- Navigation shall remain consistent across all pages.

### 4.5 Maintainability

- The application shall follow a layered backend architecture (controller, service, repository, entity, DTO) and a modular frontend structure.
- Frontend, backend, and database layers shall remain independently deployable and testable.
- The codebase shall follow consistent naming conventions to support future feature additions without major rework.

---

## 5. System Features Summary

- User Registration, Login, and Logout
- User Profile Management
- Income Management (with custom sources)
- Expense Management (with custom categories)
- Monthly and Category-wise Budget Management
- Budget Threshold Alerts (50/75/90/100%)
- Dashboard with Financial Overview
- Pie Chart and Trend Visualizations
- Highest Spending Category Identification
- Monthly Savings Calculation
- Transaction History with Search, Filter, and Sort
- Monthly Financial Summaries
- Dashboard Notifications