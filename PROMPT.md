# MASTER DEVELOPMENT PROMPT
## Build a Production-Ready, Self-Hosted Resort Restaurant POS System for Windows

### 1. ROLE AND OBJECTIVE

Act as a senior full-stack engineer, software architect, database specialist, POS systems developer, UI/UX designer, and quality assurance engineer.

Design, implement, test, and document a complete Restaurant Point of Sale (POS) application for a resort in India.

The POS must run on a **Windows-based local server** and be accessible from cashier desktops, managers' computers, and waiters' mobile phones through the resort's internal LAN/Wi-Fi network.

It must support restaurant tables and cottage-based food and beverage ordering, kitchen and bar ticket printing, billing, configurable discounts, INR-based taxation, split bills, payments, order transfers, and administrative management.

**The goal is a working, production-ready software application, not a visual prototype or a collection of disconnected screens.**

Build the complete application with frontend, backend, database, printing integration, automated tests, deployment scripts, and documentation.

---

# 2. CONFIRMED BUSINESS REQUIREMENTS

Use these settings as fixed requirements unless explicitly changed.

| Setting | Confirmed requirement |
|---|---|
| Business | Resort restaurant |
| Country | India |
| Currency | Indian Rupee (INR / ₹) |
| Local server OS | Windows |
| Deployment | Self-hosted on local network |
| Waiter devices | Android/iPhone web browsers |
| Cashier devices | Desktop web browsers |
| Printer connectivity | LAN/Ethernet |
| Printing protocol | ESC/POS where supported, with model-specific adapters if needed |
| KOT | Kitchen Order Ticket |
| BOT | Bar Order Ticket |
| Receipt printer | LAN thermal printer |
| Tax system | Configurable GST and other supported tax models |
| Cottage ordering | Supported |
| Room-charge ledger | Not required |
| Cottage payment | Independent restaurant bill settlement |
| Internet dependency | No mandatory internet connection for routine operations |

### Important cottage billing rule

Cottages are order locations, just like restaurant tables.

For example, a waiter may create an order against Cottage 105.

The system must track that order against Cottage 105, print its KOT/BOT, generate the bill, and settle the payment using an enabled payment method.

However:

- Do not implement a hotel property management system.
- Do not create a room-charge ledger.
- Do not transfer unpaid restaurant bills into a cottage account.
- Do not include "Room Charge" as a default payment method.
- Do not automatically combine unrelated historical cottage orders.

Each cottage restaurant order must be independently billable and settleable.

---

# 3. FUNCTIONAL REFERENCE

Review this existing open-source POS project as a reference:

https://pos.nodedr.com/docs

Study its available documentation and source code, if accessible, for relevant POS capabilities.

Use the reference to understand common restaurant operations and identify potentially missing functionalities.

Do not copy source code, assets, or UI implementations without checking their licensing and suitability.

The requirements in this prompt take precedence over the reference application.

---

# 4. TECHNOLOGY STACK AND ARCHITECTURE

Use the following preferred technology stack.

## Frontend

- React
- TypeScript
- Vite
- Tailwind CSS
- Responsive web design
- Progressive Web App support
- TanStack Query or equivalent for server-state management
- A consistent, accessible UI component library

## Backend

- Node.js LTS
- TypeScript
- NestJS
- REST APIs
- WebSocket-based real-time synchronization
- Server-side authentication and authorization
- Structured application logging

## Database

- PostgreSQL
- Prisma ORM or an equivalent stable ORM
- Database migrations
- Transaction-safe billing and payments
- Decimal-safe financial calculations

## Deployment

- Windows 11 Pro or supported Windows Server environment
- Docker Desktop with WSL2 for compatible Windows workstation deployments
- A supported Linux VM or equivalent container runtime for Windows Server deployments
- Docker Compose
- Local PostgreSQL storage
- Caddy or Nginx reverse proxy

Do not assume Docker Desktop supports every Windows Server configuration.

Use a modular monolith architecture unless there is a demonstrated need for additional services.

## Architectural requirements

The backend must be responsible for:

- Order validation
- Menu management
- Table/cottage assignments
- Tax calculations
- Discount calculations
- Payment settlement
- Financial integrity
- Printer job generation
- Authorization
- Audit logging
- Real-time event distribution

The frontend must never be trusted as the sole authority for prices, permissions, taxes, payments, or settlement totals.

---

# 5. WINDOWS LOCAL SERVER SETUP

The application must be easy for a resort administrator or IT technician to install on a Windows computer.

## Deployment requirements

Provide:

1. Windows-specific installation documentation.
2. Scripts for initializing required services.
3. Docker Compose configuration.
4. Database migration commands.
5. Environment configuration templates.
6. Startup and shutdown procedures.
7. Service health checks.
8. Data backup and restoration tools.
9. Windows Firewall configuration instructions.
10. LAN connectivity troubleshooting procedures.

## Server networking

The Windows server should use a reserved or static private LAN IP address.

Example:

192.168.1.10

This is an illustrative address only.

The POS might then be accessible through:

https://pos.resort.internal

The final hostname must be configured for the resort's actual DNS and certificate environment.

Do not assume that a custom `.internal` domain has a publicly trusted TLS certificate.

Provide a practical HTTPS setup using an internal CA or another trusted local-network solution, including instructions for trusting the certificate on waiter phones and cashier computers.

## Startup and recovery

The deployment must support:

- Automatic application startup after a server restart
- Automatic restart of failed application processes
- Database persistence
- Application health monitoring
- Graceful shutdown
- Recovery from unexpected power failures

Document the limitations of running services through Docker Desktop with a user session and recommend a supported always-on server configuration where necessary.

## Internet independence

The system must remain usable when internet access is unavailable, provided that:

- The Windows server is powered on.
- The resort LAN/Wi-Fi is working.
- Local application and database services are running.
- Client devices can access the server.

Avoid mandatory dependencies on external authentication providers, hosted databases, cloud printing, cloud APIs, or internet-loaded frontend assets.

---

# 6. LAN / ETHERNET PRINTER INTEGRATION

Implement centralized server-controlled printer management.

The resort will use LAN/Ethernet printers for:

1. Kitchen Order Tickets (KOT)
2. Bar Order Tickets (BOT)
3. Customer receipts/bills

## Printer configuration

The administrator must be able to configure each printer with:

- Display name
- IP address
- Network port
- Printer role
- Paper width
- Character encoding
- Compatible command protocol
- Enabled/disabled status
- Assigned preparation station
- Fallback printer, if configured

Example configuration:

| Printer | Example IP | Purpose |
|---|---|---|
| Kitchen Printer | 192.168.1.201 | Food KOT |
| Bar Printer | 192.168.1.202 | Beverage BOT |
| Billing Printer | 192.168.1.203 | Final receipt |

These are example addresses, not actual printer addresses.

## Printing architecture

The printing service should run on the local server or on a dedicated LAN-accessible print bridge.

For compatible Ethernet thermal printers, consider ESC/POS printing over TCP, commonly using port 9100.

Do not assume every Ethernet printer supports raw ESC/POS or the same network port.

Implement model-specific configuration and document verified compatibility.

## KOT / BOT routing

Menu items must have configurable preparation stations.

Example:

- Paneer Tikka → Kitchen
- Dal Makhani → Kitchen
- Butter Naan → Kitchen
- Fresh Lime Soda → Bar
- Beer → Bar
- Mocktail → Bar

When an order contains both kitchen and bar items:

- Send food items to the kitchen queue.
- Send beverage items to the bar queue.
- Create separate preparation tickets.
- Maintain references to the original order.
- Do not duplicate items unnecessarily.

## Printer queue

Implement persistent print jobs with statuses such as:

- Pending
- Sending
- Completed
- Failed
- Retry Required

Support:

- Test print
- Manual retry
- Manual reprint
- Failed-job inspection
- Printer-specific routing
- Printer configuration changes
- Printer error logs

A network socket accepting print data does not necessarily confirm physical printing. Reflect this limitation in job statuses and operational documentation.

## Reprints and amendments

The system must distinguish:

- Original KOT/BOT
- Additional items
- Quantity changes
- Item cancellations
- Manual reprints

Reprints must be clearly marked to reduce accidental duplicate preparation.

---

# 7. USER ACCOUNTS AND ACCESS CONTROL

Implement role-based access control.

## Administrator

Full authorized access to:

- User accounts
- Roles and permissions
- Menu and pricing
- Categories
- Tables and cottages
- Taxes
- Discounts
- Payment methods
- Printers
- Reports
- Operational settings
- Audit logs
- Backups

## Manager

Configurable access to:

- Active orders
- Order transfers
- Order splitting
- Discount approval
- Void approval
- KOT/BOT reprinting
- Receipt reprinting
- Payment settlement
- Selected reports

## Waiter

Mobile-focused access to:

- Assigned tables
- Cottages
- Menu browsing
- Taking orders
- Updating open orders
- Adding preparation notes
- Sending KOT/BOT
- Checking order statuses
- Requesting bills

## Cashier

Access to:

- Open orders
- Bills
- Payments
- Split settlements
- Receipts
- Transaction history
- Authorized corrections

## Optional roles

Support kitchen and bar operators.

All permissions must be enforced on the backend.

---

# 8. TABLE AND COTTAGE MANAGEMENT

Provide a visual management screen containing:

- Restaurant tables
- Cottage numbers
- Sections
- Occupancy
- Open orders
- Current totals
- Assigned waiters

Example table IDs:

Table 01, Table 02, Table 03

Example cottage IDs:

Cottage 101, Cottage 102, Cottage 103

Support:

- Creating and editing locations
- Occupancy tracking
- Opening orders
- Viewing active orders
- Assigning staff
- Moving orders between locations
- Closing locations after settlement
- Optional multiple independent active orders

A cottage must not be treated as a payment method.

---

# 9. ORDER MANAGEMENT

Implement the complete order workflow.

## New order

1. Waiter logs in.
2. Selects a table or cottage.
3. Browses menu categories.
4. Adds items and quantities.
5. Adds special instructions.
6. Reviews the order.
7. Submits the order.
8. Backend validates and saves the order.
9. Billing desktop receives the update.
10. KOT/BOT jobs are created according to the configured routing.

## Order modifications

Support:

- Adding items
- Decreasing quantities
- Removing items
- Changing preparation notes
- Recalling open orders
- Viewing historical orders
- Reprinting preparation tickets

Changes to already-submitted items must create an auditable amendment or cancellation ticket when relevant.

## Order identification

Every order must have:

- Unique order number
- Creation timestamp
- Table/cottage identifier
- Waiter
- Items
- Item prices
- Taxes
- Discounts
- Preparation ticket references
- Order state
- Payment state
- Audit history

---

# 10. REAL-TIME MULTI-DEVICE OPERATION

Use WebSockets for real-time synchronization.

Example:

A waiter submits an order on an Android phone.

Immediately after successful server-side persistence:

- The billing counter receives the order.
- The table or cottage status updates.
- Kitchen and bar tickets are queued.
- Authorized users see updated order information.

Handle:

- Client reconnection
- Concurrent edits
- Duplicate submissions
- Network interruptions
- Version conflicts
- Failed requests

Use idempotency keys and appropriate database constraints.

A mobile device must never display "Order Successfully Submitted" until the server acknowledges successful persistence.

---

# 11. MOBILE WAITER WEB APPLICATION

Create a mobile-first responsive POS.

Required screens:

- Login
- Tables and Cottages
- Menu Categories
- Menu Items
- Current Order
- Order Notes
- Submit Order
- Active Orders
- Order Details

Prioritize:

- Large touch targets
- Fast navigation
- Minimal data entry
- Clear quantities
- Quick item search
- Good Android/iPhone browser compatibility

Implement PWA capabilities where appropriate.

Do not require separate native Android or iOS applications.

If offline drafts are supported, clearly distinguish local unsent drafts from confirmed orders.

---

# 12. MENU MANAGEMENT

Admin must be able to:

- Create menu categories
- Edit categories
- Create items
- Modify prices
- Configure tax slabs
- Set kitchen/bar routing
- Disable items
- Mark items temporarily unavailable
- Add optional modifiers and variants

Example:

| Item | Price | Category | Station |
|---|---:|---|---|
| Paneer Tikka | ₹280 | Starters | Kitchen |
| Dal Makhani | ₹320 | Main Course | Kitchen |
| Butter Naan | ₹60 | Breads | Kitchen |
| Fresh Lime Soda | ₹120 | Beverages | Bar |

These are sample values only.

Historical transactions must retain the price and tax information that applied when they were finalized.

---

# 13. INDIA-READY GST AND TAX SYSTEM

Use INR as the default currency.

Display amounts in Indian currency formatting where appropriate.

Examples:

₹250.00

₹1,250.00

₹1,25,000.00

Implement a configurable tax engine capable of supporting:

- GST
- CGST
- SGST
- IGST
- Zero-rated items
- Tax exemptions
- Different product-level rates
- Tax-inclusive prices
- Tax-exclusive prices
- Configurable tax rounding

The system must not hardcode a single GST rate.

Support tax configurations using rate definitions and effective dates.

The administrator must be able to configure tax labels, registration information, and applicable rules.

**Important:** Restaurant GST treatment may depend on the applicable legal rules, resort classification, services, and transaction circumstances. Do not assume that any example GST rate is automatically correct.

Use configurable business rules and require the business's accountant or tax advisor to validate the production tax setup.

## Receipt tax breakdown

Example structure:

Subtotal: ₹1,000.00

Discount: ₹100.00

Taxable amount: ₹900.00

CGST: calculated based on the configured rate

SGST: calculated based on the configured rate

Total: calculated amount

The application must use decimal-safe calculations and an explicitly documented rounding policy.

Support configuration of GSTIN and receipt/invoice fields as applicable.

---

# 14. DISCOUNT ENGINE

Support administrator-configured discounts.

Types:

- Item percentage discount
- Item fixed-amount discount
- Category discount
- Food-only discount
- Bar-only discount
- Order-level percentage discount
- Order-level fixed discount
- Manager-approved discount

Configure:

- Discount name
- Type
- Value
- Eligible items
- Eligible categories
- Effective dates
- Approval requirements
- Maximum allowed amount
- Stacking rules

Track the user who applied or approved a discount.

Tax calculations must correctly incorporate the configured discount treatment.

---

# 15. BILLING AND RECEIPTS

Create a cashier-oriented desktop interface.

Display:

- Order number
- Table/cottage
- Waiter
- Item names
- Quantities
- Prices
- Discounts
- Subtotal
- Tax components
- Grand total
- Payment status

## Receipt settings

Admin can configure:

- Resort name
- Restaurant name
- Address
- Contact details
- GSTIN
- Invoice numbering
- Date/time format
- Footer message
- Printer
- Paper width

Support:

- Bill preview
- Bill printing
- Bill reprinting
- Historical receipt retrieval
- Tax breakdown

Generate receipt data on the backend rather than trusting calculations supplied by the browser.

---

# 16. PAYMENT METHODS AND SETTLEMENT

The administrator can configure payment types.

Initial supported methods:

- Cash
- UPI
- Credit Card
- Debit Card
- Bank Transfer
- Complimentary
- Other

No room-charge payment method is required.

## Payment settlement

Support:

- Full payment
- Partial payment
- Mixed payments
- Split-bill settlement
- Reference numbers
- Settlement timestamps
- Payment audit trails

Example:

Total bill: ₹3,000

Cash: ₹1,000

UPI: ₹2,000

Balance: ₹0

### Payment integrity

- Never settle the same amount twice.
- Prevent unauthorized payment edits.
- Log reversals and refunds.
- Retain financial transaction history.
- Do not store sensitive payment-card data.

For manual UPI/card payments, distinguish a manually recorded successful payment from a technically verified payment gateway transaction.

---

# 17. SPLIT BILLS

Support:

1. Splitting by items
2. Splitting by quantity
3. Equal splitting
4. Custom amount splitting where compatible with the selected tax allocation rules

Each split must preserve:

- Correct taxable amount
- Correct discount allocation
- Correct payment amount
- Original order references
- Individual settlement state

Ensure that splitting cannot create duplicate revenue or lose items.

---

# 18. ORDER TRANSFER

Allow authorized staff to transfer active orders.

Examples:

Table 03 → Table 08

Table 05 → Cottage 102

Cottage 101 → Cottage 104

Requirements:

- Preserve original order number.
- Record source and destination.
- Record user and timestamp.
- Require confirmation.
- Prevent conflicts.
- Preserve historical KOT/BOT records.

---

# 19. VOID AND CANCELLATION CONTROLS

Implement mandatory remarks when an order is voided.

Store:

- Order identifier
- Reason
- Requesting user
- Approving user if required
- Timestamp
- Financial effect
- Audit trail

Do not permanently delete voided orders.

Already-settled orders must be corrected using an auditable reversal/refund mechanism.

---

# 20. KITCHEN AND BAR DISPLAYS

Provide optional browser-based displays.

Kitchen display:

- Pending food orders
- Order age
- Table/cottage
- Item quantities
- Preparation notes
- Status updates

Bar display:

- Pending beverage orders
- Table/cottage
- Item quantities
- Status updates

Use the same backend and real-time infrastructure.

---

# 21. REPORTING

Build administrator and manager reports.

Required reports:

- Daily sales
- Monthly sales
- Custom date-range sales
- Food sales
- Bar sales
- Table-wise sales
- Cottage-wise sales
- Waiter-wise sales
- Payment-method sales
- Discounts
- Voids
- Tax summaries
- Unsettled orders
- Reprints
- Printer failures

Exports:

- CSV
- Print-friendly views
- PDF where practical

Use the resort's configured timezone and business-day cutoff for reporting.

---

# 22. DATABASE AND FINANCIAL INTEGRITY

Design the PostgreSQL schema around:

- Users
- Roles
- Permissions
- Tables
- Cottages
- MenuCategories
- MenuItems
- Orders
- OrderItems
- OrderRevisions
- PreparationTickets
- PrintJobs
- Bills
- SplitBills
- Taxes
- TaxRules
- Discounts
- Payments
- PaymentMethods
- Refunds
- Printers
- PreparationStations
- AuditLogs
- Settings

Do not include a hotel room-charge ledger.

Use:

- Foreign keys
- Database indexes
- Transactions
- Historical price snapshots
- Historical tax snapshots
- Idempotency keys
- Optimistic locking where useful
- Reliable print-job persistence

Protect financial history from accidental deletion or modification.

---

# 23. SECURITY AND AUDIT

Implement:

- Authentication
- Password hashing
- Backend role authorization
- Secure sessions
- Input validation
- Request rate limiting
- CSRF protection as appropriate
- Audit logging
- Restricted database access
- HTTPS for local browser access

Audit events must include:

- Discount approvals
- Menu price changes
- Tax changes
- Void requests
- Refunds
- Order modifications
- Split bills
- Table transfers
- Ticket reprints
- Bill reprints
- Payments

---

# 24. BACKUP AND RESTORE

Support local-server data protection.

Required functionality:

- Daily scheduled database backup
- Manual backup
- Backup retention
- Backup integrity verification
- Restore instructions
- Recovery from server failure
- Migration procedures

Provide a Windows-compatible backup workflow.

Prefer a second storage location or external/NAS backup destination rather than relying exclusively on the server's own disk.

Ensure that database recovery is tested.

---

# 25. TESTING REQUIREMENTS

Implement automated tests covering:

- Waiter login
- New orders
- Table/cottage assignment
- Order modification
- Kitchen/bar routing
- Printer queue persistence
- Failed-printer recovery
- Ticket reprints
- Bill generation
- GST calculations
- Discounts
- Mixed payments
- Split bills
- Table transfers
- Order voiding
- User authorization
- Concurrent orders
- Network interruptions
- Application restarts
- Backup restoration

Use a printer emulator for automated ESC/POS testing, followed by real-device testing against selected supported printer models.

Do not claim physical printing is verified through emulator tests alone.

---

# 26. DEVELOPMENT ROADMAP

Implement in this order:

**Phase 1 — Project foundation**

- Architecture
- Repository setup
- Docker configuration
- Windows deployment plan
- PostgreSQL schema
- Initial migrations

**Phase 2 — Core administration**

- Authentication
- Users
- Roles
- Tables and cottages
- Menu management
- Settings

**Phase 3 — Ordering**

- Mobile waiter POS
- Desktop order management
- WebSocket synchronization
- Order lifecycle

**Phase 4 — Preparation tickets and printers**

- Kitchen/bar routing
- LAN printer configuration
- KOT/BOT generation
- Persistent print queue
- Reprint controls

**Phase 5 — Financial operations**

- GST engine
- Discounts
- Billing
- Payments
- Splitting
- Order transfer
- Void/refund workflows

**Phase 6 — Reporting**

- Sales reports
- Tax reports
- Payment reconciliation
- Audit logs
- Data exports

**Phase 7 — Production readiness**

- Windows installation scripts
- LAN/HTTPS configuration
- Testing
- Backups
- Security hardening
- Operator documentation

At the end of each phase, provide working code, migration updates, tests, and steps to verify the functionality.

Do not declare a feature complete without implementing and validating its essential backend workflow.

---

# 27. EXPECTED DELIVERABLES

Deliver:

1. Complete React frontend
2. Complete Node.js backend
3. PostgreSQL database schema
4. REST APIs
5. WebSocket integration
6. Mobile waiter interface
7. Desktop cashier interface
8. Administrator interface
9. Kitchen/bar screens
10. ESC/POS-compatible LAN printer service
11. KOT/BOT workflows
12. GST and tax configuration
13. Discount engine
14. Billing and settlement
15. Split billing
16. Table/cottage management
17. Reports and audit logging
18. Docker Compose deployment
19. Windows installation instructions
20. Backup and recovery procedures
21. Automated tests
22. API documentation
23. Database ER diagram
24. Printer compatibility/testing guide
25. Production-readiness checklist

---

# 28. INITIAL DEVELOPMENT INSTRUCTIONS

Before writing application code:

1. Review all requirements.
2. Inspect the referenced open-source POS documentation where accessible.
3. Identify implementation risks.
4. Propose the final architecture.
5. Present the database ER diagram.
6. Specify how LAN printers will be configured and accessed.
7. Explain the Windows deployment method.
8. Define the order, ticket, billing, and settlement lifecycles.
9. Define the GST calculation approach.
10. Establish project milestones and acceptance tests.

Use reasonable defaults for noncritical decisions.

Ask only questions that materially affect hardware compatibility, financial correctness, or architecture.

Once those decisions are resolved, begin implementing Phase 1 and provide a runnable application foundation.

Do not stop at planning. Continue implementing the project through testable development phases.

## FINAL SUCCESS CRITERIA

The application is considered ready for production acceptance only when a real user can:

- Open the application from a cashier desktop on the resort LAN.
- Log in from a waiter's smartphone.
- Create an order for a table or cottage.
- See that order on the main billing desktop.
- Generate correctly routed kitchen and bar tickets.
- Print those tickets on configured LAN printers.
- Add or modify items without losing ticket history.
- Generate an INR-denominated bill with correctly configured taxes.
- Apply authorized discounts.
- Split or transfer an order.
- Settle bills using configured payment methods.
- Reprint authorized tickets and receipts.
- Void an order with mandatory remarks.
- Review reports and audit history.
- Recover data from a verified backup.
- Continue normal operations without an internet connection.

Deliver a stable, maintainable, locally hosted Restaurant & Resort POS system suitable for real daily operations.