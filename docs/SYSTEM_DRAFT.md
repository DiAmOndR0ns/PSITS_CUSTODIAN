# PSITS Custodian — System Draft

## 1. Purpose

PSITS Custodian is a web-based property management system for student organizations. It manages organizational properties from registration and assignment through borrowing, release, return, inspection, damage/loss recording, and reporting.

## 2. Initial Technology Direction

- Frontend: React
- Backend/API: PHP
- Database: MySQL
- Authentication: server-side sessions with secure, HttpOnly cookies
- Passwords: Argon2id or bcrypt password hashes; plaintext passwords are never stored
- Reports: PDF/Excel export in a later phase

## 3. User Roles

### Administrator
- Manage users and organizations
- Manage property categories and locations
- View and manage all inventory
- View all requests and transactions
- View reports and audit logs
- Configure system settings

### Property Custodian
- Manage inventory
- Review borrowing requests
- Approve/reject requests
- Release properties
- Receive and inspect returned properties
- Record damage, loss, and overdue transactions
- Generate operational reports

### Organization Officer
- View the organization's properties
- Submit borrowing requests
- View request status
- View active borrowings
- View borrowing/return history

### Auditor/Viewer
- Read-only access to inventory, transactions, reports, and audit logs

## 4. Core Modules

1. Authentication and authorization
2. Dashboard
3. User management
4. Organization management
5. Property inventory
6. Borrowing requests
7. Property release
8. Property returns and inspection
9. Damage/loss records
10. Reports
11. Audit logs
12. System settings

## 5. Dashboard Drafts

### Administrator Dashboard

Primary cards:
- Total properties
- Available properties
- Borrowed properties
- Damaged properties
- Lost properties
- Pending requests
- Active organizations
- Registered users

Widgets:
- Property status summary
- Recent transactions
- Recent system activity
- Pending actions

### Property Custodian Dashboard

Primary cards:
- Total inventory
- Available properties
- Currently borrowed
- Overdue properties
- Pending requests
- Damaged properties

Priority section:
- Pending requests requiring review
- Overdue properties
- Properties awaiting return inspection
- Recent releases and returns

### Organization Officer Dashboard

Primary cards:
- Organization properties
- Active borrowings
- Pending requests
- Overdue items

Widgets:
- Available properties for request
- My pending requests
- Current borrowings
- Recent transaction history

## 6. Property Lifecycle

```text
REGISTERED
    |
    v
AVAILABLE <-----------------------------+
    |                                    |
    v                                    |
REQUESTED -> APPROVED -> RELEASED -> BORROWED
                                  |         |
                                  |         v
                                  |      RETURNED
                                  |         |
                                  |         v
                                  |     INSPECTION
                                  |       /     \
                                  |      /       \
                                  | AVAILABLE   DAMAGED
                                  |               |
                                  |               v
                                  |          REPAIR/ASSESSMENT
                                  |
                                  +---- LOST (when applicable)
```

## 7. Authentication Request Flow

```text
Login Form
   |
   v
POST /api/auth/login
   |
   +--> Find active user
   |
   +--> Verify password hash
   |
   +--> Create server-side session
   |
   +--> Set Secure + HttpOnly + SameSite cookie
   |
   v
Return authenticated user profile/role
   |
   v
Role-based dashboard
```

The client never receives or stores a plaintext password. Session identifiers are treated as credentials and are not placed in localStorage. Protected API requests use the authenticated session cookie.

## 8. Authorization Rules

Authorization must be enforced by the backend, not only by hiding frontend menu items.

Examples:
- Organization officers cannot approve their own requests.
- Organization officers cannot modify inventory.
- Custodians can process property transactions but cannot manage administrator accounts unless explicitly granted that permission.
- Auditors cannot create, edit, approve, release, or delete records.
- Administrators have full system-management access.

## 9. Auditability

Important actions should create audit-log entries, including:
- Login/logout
- User creation or role changes
- Property creation/edit/archive
- Request submission/approval/rejection
- Property release
- Property return
- Damage/loss recording
- Organization changes
- Report generation where appropriate

## 10. MVP Build Order

### Phase 1 — Foundation
- Project structure
- MySQL schema
- API structure
- Authentication
- Role-based access control

### Phase 2 — Core Property Management
- Organizations
- Categories
- Locations
- Property inventory

### Phase 3 — Transactions
- Borrowing requests
- Approval/rejection
- Release
- Return and inspection
- Damage/loss

### Phase 4 — Dashboards and Reports
- Role-specific dashboards
- Inventory reports
- Transaction reports
- Accountability reports
- PDF/Excel export

### Phase 5 — Enhancements
- QR codes
- Property photos
- Notifications
- Digital signatures
- Advanced analytics

## 11. Security Baseline

- Passwords must be hashed using Argon2id or bcrypt.
- Login should use rate limiting and generic authentication errors.
- Session cookies should be Secure, HttpOnly, and appropriately SameSite-configured.
- CSRF protection should be enabled for state-changing requests when using cookie-based authentication.
- All database access should use parameterized queries/prepared statements.
- User input must be validated on the server.
- Authorization checks must occur on every protected API operation.
- Secrets and database credentials must be supplied through environment configuration and never committed to Git.
- Destructive operations should be audited and preferably require confirmation.
