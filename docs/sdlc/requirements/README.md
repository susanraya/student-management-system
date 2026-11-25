# Product Requirements

> Maintained by QuorQ AI SDLC. Text between `sdlc:auto` markers is regenerated; anything outside them is yours and is kept.

<!-- sdlc:auto:start narrative -->
## Summary

### REQ-STACK-001 · approved · source: requirements:technology stack

Technology stack fixed by the product owner for v1: FastAPI backend, AWS hosting, PostgreSQL database, React frontend.

- Backend: FastAPI 0.115 -- authenticates the single owner, stores/retrieves expenses, computes category and monthly totals for the dashboard, produces CSV exports; chosen for being lean and easy to extend.
- Cloud: AWS -- hosts the web app and PostgreSQL cheaply at one-user scale with strong reliability/documentation; managed database gives automatic backups of expense history.
- Database: PostgreSQL 16 -- relational fit for expense rows (amount, date, category, note); reliably handles date-range filtering, sorting, sum-by-category/month grouping, and precise money values.
- Frontend: React 19 -- handles the chart-heavy dashboard well, pairs with mature charting libraries, also used for simpler entry/list/filter/sort screens; delivers a responsive app for laptop and phone.

**Related:** NFR-005, NFR-006

### REQ-SERVICES-001 · approved · source: requirements:capabilities in scope

Full list of capabilities the application must provide at v1.

- Owner account login (email and password, single owner)
- Manual expense entry (amount, date, category, note)
- View, edit and delete past expenses
- Filter and sort expenses by date and category
- Expense categories, including owner-added custom categories
- Spending dashboard: totals by category and by month
- Summary charts of spending
- Currency selection in settings (default USD)
- Ships with common business expense categories
- Text-only expense records (no receipt images or PDFs)
- Free to the owner -- no subscription or billing

### REQ-SCOPE-001 · approved · source: requirements:scope

Delivery scope is lightweight, version 1, approved. Defines what is explicitly excluded from this build.

Out of scope -- not to be designed or built:
- Automatic bank/credit-card transaction import.
- Category budgets and overspending alerts.
In-scope capabilities are documented separately (see REQ-SERVICES-001). Delivery scope flagged as 'lightweight'; this is requirements version 1.

### REQ-FEAT-001 · approved · source: requirements:Sign-in & accounts

Single-owner authentication: sign in, sign out/session expiry, and free-of-charge use.

- Owner sign-in: sign in with email and password, land on dashboard; wrong email or password shows a generic error that does not reveal which field was incorrect.
- Sign out and session end: owner can sign out ending the session; an inactive session also expires on its own; either way the owner must sign in again before reaching any expense data.
- Free to use: every feature usable without ever being asked to pay, enter billing details, or subscribe.

### REQ-FEAT-002 · approved · source: requirements:Recording expenses

Manual entry of new expenses with validation.

- Record a new expense: enter amount, date, category and optional note; amount, date and category are required, amount must be a positive number; invalid entries show a validation message explaining what to correct. Expenses are text only -- no receipt image or PDF attachment option.

### REQ-FEAT-003 · approved · source: requirements:Managing expenses

Viewing, editing, deleting, filtering and sorting recorded expenses.

- View past expenses: list shows amount, date, category and note per expense; empty-state message prompts adding the first expense when none exist.
- Edit an expense: change amount, date, category or note and save with valid values; invalid values or a removed category are rejected with a validation message.
- Delete an expense: delete after confirmation, permanently removing it from the list and all summaries; cancelling leaves it unchanged.
- Filter and sort expenses: filter by category and date range, sort by date or category ascending/descending; when nothing matches, a message appears and filters can be cleared.

### REQ-FEAT-004 · approved · source: requirements:Categories

Default and custom expense categories.

- Default expense categories: new accounts come with common business expense categories already available for recording or editing an expense.
- Custom categories: owner can add and edit categories with unique names; changes appear everywhere the category is shown; blank or duplicate names are rejected with a validation message. No budget or overspending alert option exists.

### REQ-FEAT-005 · approved · source: requirements:Spending dashboard

Dashboard showing spending totals by category and by month, as summaries and charts.

- Spending totals by category: total amount spent per category shown as summary and chart; empty state shown when there are no expenses.
- Spending totals by month: monthly spending totals shown as summary and chart, displayed in the owner's selected currency, reflecting expenses only (no income, profit or budget figures).

### REQ-FEAT-006 · approved · source: requirements:Settings

Currency selection for the whole app.

- Choose the app currency: select a single currency in settings, defaulting to USD; all amounts across expenses, summaries and charts then display in that currency. Only one currency can be chosen at a time -- no multi-currency or live exchange-rate options.

### REQ-FEAT-007 · approved · source: requirements:Export

CSV export of expense records for the accountant.

- Export expenses to CSV: produces a CSV with each expense's amount, date, category and note, reflecting any filters currently applied to the list; when there are no expenses to export, the owner is told there is nothing to export or receives a file with only column headers.

### NFR-001 · approved · source: requirements:NFR-001

Security: account passwords must be stored only as hashes.

The owner's account password must be stored only in hashed form and never in plain text, so it cannot be read from the database.

**Related:** REQ-FEAT-001

### NFR-002 · approved · source: requirements:NFR-002

Security: sessions must end on sign out and expire after inactivity.

A signed-in session must end when the owner signs out, and must also expire after a period of inactivity, so the owner then has to sign in again before reaching any expense data.

**Related:** REQ-FEAT-001

### NFR-003 · approved · source: requirements:NFR-003

Security: sign-in failure messages must not reveal which credential was wrong.

When sign-in fails, the error message must not reveal whether the email or the password was the incorrect one.

**Related:** REQ-FEAT-001

### NFR-004 · approved · source: requirements:NFR-004

Privacy: all expense data, summaries and exports must be private to the single signed-in owner.

All expense records, summaries and exports must be accessible only to the single signed-in owner and never exposed to anyone else.

**Related:** REQ-FEAT-003, REQ-FEAT-005, REQ-FEAT-007

### NFR-005 · approved · source: requirements:NFR-005

Compatibility: app must be a responsive web app usable on laptop and phone.

The app must work as a responsive web application that the owner can use comfortably on both a laptop and a phone.

**Related:** REQ-STACK-001

### NFR-006 · approved · source: requirements:NFR-006

Availability: app should be available whenever needed, with automatic backup of expense history.

As a small personal tool the app is expected to be available whenever the owner wants to record or review expenses, with the owner's expense history backed up automatically so it is not lost.

**Related:** REQ-STACK-001
<!-- sdlc:auto:end narrative -->

<!-- sdlc:auto:start summary -->
**Requirements version:** v1  
**Delivery scope:** lightweight  
**Approved:** 2026-11-25

**Capabilities in scope:** Owner account login, Manual expense entry (amount, date, category, note), View, edit and delete past expenses, Filter and sort expenses by date and category, Expense categories with owner-added custom categories, Spending dashboard: totals by category and by month, Summary charts of spending, Currency selection in settings, Email and password login for a single owner account, Ships with common business expense categories, Text-only expense records (no receipt images or PDFs), Default currency set to USD, Free to the owner — no subscription or billing

**Approved technology stack:**

| Layer | Choice | Rationale |
|---|---|---|
| backend | FastAPI | The backend's job here is modest and well-defined: authenticate one owner, store and retrieve expenses, compute the category and monthly totals behind the dashboard, and produce a CSV for the accountant. FastAPI is a lean Python framework that does exactly that without ceremony, and Python makes the summing/grouping for charts and the CSV export straightforward to write and change later. |
| cloud | AWS | This is a small, always-available personal tool, and AWS can host the web app and PostgreSQL cheaply at this one-user scale while staying rock-solid and well-documented. Its managed database service means the owner's expense history is backed up automatically rather than something anyone has to remember to do. |
| database | PostgreSQL | You confirmed a relational database, and expenses are naturally relational — each row is an amount, date, category and note, grouped by category for the dashboard. PostgreSQL does the date-range filtering, sorting, and sum-by-category/month grouping the dashboard depends on reliably, and handles money values precisely so totals never drift by a cent. It's ample for one owner's data and leaves room if this ever grows. |
| frontend | React | The heart of this app is the dashboard — totals by category and by month shown as charts the owner reads at a glance. React handles that kind of interactive, chart-heavy screen well and pairs cleanly with mature charting libraries, while still being perfectly comfortable for the simpler expense-entry and list/filter/sort screens. It also gives you the responsive web app the owner expects on both laptop and phone. |
<!-- sdlc:auto:end summary -->

<!-- sdlc:auto:start epics -->
## EPIC-001: Owner Account and Authentication

A single business owner can create and access their own account using email and password. The app is a single-account personal tool; no multi-user roles, employee submission, or approval workflows exist. The product is free to the owner with no subscription or billing.

### US-001: Owner signs in with email and password

**Priority:** critical

> As a business owner, I want to sign in to my account with my email and password, so that only I can access my private expense records.

| ID | Given | When | Then |
|---|---|---|---|
| AC-001 | I am a registered owner with valid credentials | I enter my correct email and password and submit the sign-in form | I am signed in and taken to my dashboard |
| AC-002 | I am on the sign-in page | I enter an incorrect email or password | I am not signed in and I see an error message that does not reveal which field was wrong |
| AC-003 | my account credentials are stored | a password is saved to the database | it is stored only in hashed form and never in plain text |

### US-002: Owner signs out and sessions expire

**Priority:** high

> As a business owner, I want my session to end when I sign out or after it expires, so that my expense data is not left accessible on an unattended device.

| ID | Given | When | Then |
|---|---|---|---|
| AC-004 | I am signed in | I choose to sign out | my session ends and I must sign in again to access any expense data |
| AC-005 | I am signed in and inactive beyond the configured session lifetime | the session expires | I am required to sign in again before accessing my data |

### US-003: Owner uses the app free of charge

**Priority:** medium

> As a business owner, I want to use the full app without any payment, subscription, or billing, so that I can track expenses as a free personal tool.

| ID | Given | When | Then |
|---|---|---|---|
| AC-006 | I have an account | I use any feature of the app | I am never asked to pay, enter billing details, or subscribe |

## EPIC-002: Manual Expense Entry and Management

The owner manually records text-only expense entries with amount, date, category, and note, and can view, edit, and delete them. Expenses are text records only — no receipt image or PDF attachment. The app tracks expenses only, not income, revenue, or profit. Automatic bank or credit-card import is out of scope.

### US-004: Owner records a new expense

**Priority:** critical

> As a business owner, I want to manually enter an expense with amount, date, category, and note, so that I have an accurate record of my business spending.

| ID | Given | When | Then |
|---|---|---|---|
| AC-007 | I am signed in and on the new expense form | I enter a valid amount, date, and category, add an optional note, and save | the expense is stored and appears in my list of expenses |
| AC-008 | I am entering a new expense | I leave the amount, date, or category empty or enter a non-positive or non-numeric amount | the expense is not saved and I see a validation message indicating what to correct |
| AC-009 | I am recording an expense | I view the entry form | there is no option to attach a receipt image or PDF, as expenses are text records only (open assumption: no file attachments) |

### US-005: Owner views past expenses

**Priority:** high

> As a business owner, I want to see a list of my recorded expenses, so that I can review what I have spent.

| ID | Given | When | Then |
|---|---|---|---|
| AC-010 | I have recorded expenses | I open the expenses list | I see each expense showing its amount, date, category, and note |
| AC-011 | I have recorded no expenses | I open the expenses list | I see an empty-state message prompting me to add my first expense |

### US-006: Owner edits an existing expense

**Priority:** high

> As a business owner, I want to edit a past expense, so that I can correct mistakes in amount, date, category, or note.

| ID | Given | When | Then |
|---|---|---|---|
| AC-012 | I am viewing an existing expense | I change its amount, date, category, or note and save with valid values | the updated values are stored and shown in the list |
| AC-013 | I am editing an expense | I enter an invalid amount, date, or remove the category | the changes are not saved and I see a validation message |

### US-007: Owner deletes an expense

**Priority:** medium

> As a business owner, I want to delete an expense I no longer want to track, so that my records stay accurate.

| ID | Given | When | Then |
|---|---|---|---|
| AC-014 | I am viewing an existing expense | I choose to delete it and confirm the deletion | the expense is permanently removed from my list and excluded from all summaries |
| AC-015 | I choose to delete an expense | I cancel the confirmation | the expense remains unchanged |

### US-008: Owner filters and sorts expenses by date and category

**Priority:** high

> As a business owner, I want to filter and sort my past expenses by date and category, so that I can quickly find and review specific spending.

| ID | Given | When | Then |
|---|---|---|---|
| AC-016 | I have expenses across multiple categories and dates | I apply a category filter and/or a date range filter | only the expenses matching the selected criteria are shown |
| AC-017 | I am viewing my expenses list | I sort by date or by category in ascending or descending order | the list reorders accordingly |
| AC-018 | I have applied filters | no expenses match the selected criteria | I see a message indicating no expenses match and can clear the filters |

## EPIC-003: Expense Categories

The app ships with common business expense categories and lets the owner add and edit their own custom categories. Every expense is organized by a single category. No category budgets or overspending alerts are included.

### US-009: App provides common default categories

**Priority:** high

> As a business owner, I want the app to come with common business expense categories, so that I can start categorizing expenses without setting everything up first.

| ID | Given | When | Then |
|---|---|---|---|
| AC-019 | I have a new account | I open the category list or create my first expense | a set of common business expense categories is already available to choose from |

### US-010: Owner manages custom categories

**Priority:** medium

> As a business owner, I want to add and edit my own expense categories, so that my categories match how my business spends.

| ID | Given | When | Then |
|---|---|---|---|
| AC-020 | I am on the categories screen | I add a new category with a unique name | it is saved and becomes selectable when recording or editing an expense |
| AC-021 | I have a custom category | I edit its name to another unique name | the change is saved and reflected everywhere the category is shown |
| AC-022 | I try to add or rename a category | the name is blank or duplicates an existing category name | it is not saved and I see a validation message |
| AC-023 | I am managing categories | I view category options | there is no option to set a budget or overspending alert, as these are out of scope |

## EPIC-004: Spending Dashboard and Charts

The primary output: an on-screen dashboard with summaries and charts showing total spending by category and by month, so the owner can understand spending at a glance. Summaries reflect expenses only — no income, profit, or budgets.

### US-011: Owner views spending totals by category

**Priority:** critical

> As a business owner, I want to see my total spending broken down by category, so that I understand where my money goes.

| ID | Given | When | Then |
|---|---|---|---|
| AC-024 | I have recorded expenses in several categories | I open the dashboard | I see the total amount spent per category displayed as a summary and a chart |
| AC-025 | I have no recorded expenses | I open the dashboard | I see an empty state indicating there is nothing to summarize yet |

### US-012: Owner views spending totals by month

**Priority:** critical

> As a business owner, I want to see my total spending broken down by month, so that I can see how my spending changes over time.

| ID | Given | When | Then |
|---|---|---|---|
| AC-026 | I have recorded expenses across multiple months | I open the dashboard | I see monthly spending totals displayed as a summary and a chart |
| AC-027 | I am viewing the dashboard summaries | amounts are displayed | they are shown in my selected currency and reflect expenses only, with no income, profit, or budget figures |

## EPIC-005: Currency Settings

The owner can select a single currency for the app in settings, defaulting to USD. There is no multi-currency support and no live exchange rates.

### US-013: Owner selects the app currency

**Priority:** medium

> As a business owner, I want to choose a single currency for the app in settings, so that amounts are displayed in the currency I work in.

| ID | Given | When | Then |
|---|---|---|---|
| AC-028 | I have a new account | I have not changed the currency setting | the currency defaults to USD |
| AC-029 | I am in settings | I change the currency to another single currency and save | all amounts across expenses, summaries, and charts are displayed in the newly selected currency |
| AC-030 | I am choosing a currency | I view the currency setting | I can select only one currency at a time, with no multi-currency or live exchange-rate options (open assumption: single selectable currency) |

## EPIC-006: CSV Export for Accountant

A secondary convenience feature that lets the owner export their expense records as a CSV file to share with an accountant. This is a secondary feature; summaries and charts remain the primary output.

### US-014: Owner exports expenses to CSV

**Priority:** low

> As a business owner, I want to export my expenses as a CSV file, so that I can share my records with my accountant.

| ID | Given | When | Then |
|---|---|---|---|
| AC-031 | I have recorded expenses | I choose to export to CSV | a CSV file is produced containing each expense's amount, date, category, and note |
| AC-032 | I have applied filters to my expenses list | I export to CSV | the export reflects the currently filtered set of expenses |
| AC-033 | I have no recorded expenses | I choose to export to CSV | I am informed there is nothing to export or receive a file containing only the column headers |
<!-- sdlc:auto:end epics -->
