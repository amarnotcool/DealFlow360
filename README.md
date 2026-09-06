# DealFlow360 - Intelligent, Self-Governing Sales Operations Platform: Odoo Hackathon 2026

DealFlow360 is a comprehensive, self-governing B2B sales operations platform that bridges the gap between sales quotation, discount governance, warehouse inventory reality, and customer negotiation. Moving beyond static quote-to-invoice forms, the platform provides automated approval routing with a pure blended risk engine, live upsell recommendations, multi-warehouse fulfillment splitting, hybrid subscription billing with mid-cycle proration, and customer portal negotiation.

---

## Features

### For Sales Reps
- **Quotation Builder:** Rapidly configure quotes mixing hardware, services, and recurring subscriptions with live catalog pricing.
- **Real-Time Margin & Ceiling Indicators:** Visual indicators (`OVER (+Npt)`) update live as discounts are typed against product and customer tier ceilings.
- **Live Upsell & Cross-Sell:** Ranked suggestion panel surfaces healthy-margin items (≥20% margin cutoff) with instant margin and order total recalculation.
- **Approval Tracking:** Automated submission and routing; reps never have to manually guess who needs to approve a discount.
- **Negotiation Management:** Review, accept, or reject counter-discount proposals and comments submitted by customers directly within the quotation view.

### For Sales Managers
- **Multi-Tier Discount Governance:** Automatic policy enforcement evaluating line-item category ceilings and customer tier limits.
- **Blended Risk Score Engine:** Intelligent scoring (`0.6 × max single overage + 0.4 × total overage`) that catches both single egregious breaches and quiet margin erosion spread across multiple lines.
- **Approval Workbench:** Comprehensive review screen displaying the "Why Flagged" breakdown, risk metrics, approval timelines, and audit trails.
- **Decision Controls:** One-click `Approve`, `Reject`, or `Return for Revision` with required justification logging.
- **Deal Health Monitoring:** Track at-risk deals with automated anomaly detection for stalled quotes, discount anomalies, and delivery slippages.

### For Finance & Operations
- **Tier-2 Escalations:** Second-level approvals for high-risk discounts and policy violations.
- **Multi-Warehouse Split Optimization:** Algorithmic split allocator (`split-allocator.ts`) that minimizes total shipments and respects warehouse shipping cost weights.
- **Manual Fulfillment Override:** Interactive matrix to override warehouse assignments with mandatory audit justification.
- **Backorder Management:** Automatic backorder creation with one-click **"Consolidate Remaining Backorder"** when new inventory arrives.
- **Hybrid Billing & Invoicing:** Separate invoicing streams for physical goods (billed upon shipment) and subscription lines (billed per cycle schedule).
- **Automated Credit Notes:** Mid-cycle subscription cancellations and downgrades automatically calculate daily proration and generate credit notes.

### For Customers (Portal Users)
- **Dedicated Negotiation Portal:** Isolated customer view with customer-safe terminology (internal margins, ceilings, and risk scores are hidden).
- **Line-Level Change Requests:** Submit line-item comments, request delivery dates, and propose counter-discounts.
- **Instant Order Confirmation:** Confirm quotes with a single click.
- **Auto Re-Approval Protection:** If customer-confirmed terms breach company thresholds, the deal automatically re-enters internal manager approval.

### For Administrators
- **Discount Tiers & Policy Setup:** Dynamic configuration screen (`/discount-tiers`) for customer tier ceilings, category limits, and global backstops.
- **Catalog & Price List Management:** Comprehensive CRUD for products, variants with `extraPrice`, recurring cycles, and tier-linked price lists.
- **Warehouse & Inventory Administration:** Configure regional warehouses, shipping weights, stock receipts, and inventory corrections.
- **User & RBAC Directory:** Manage internal staff roles (`SALES_REP`, `SALES_MANAGER`, `FINANCE`, `ADMIN`).
- **Reporting & Analytics:** Generate exportable sales performance reports with period, rep, status, and product filters in **PDF** and **XLSX**.

---

## Security & Monitoring

- **Immutable Audit Logging:** Every approval, rejection, discount modification, manual split override, and stock movement writes an audit log entry with actor ID, timestamp, and rationale.
- **Surface Isolation:** Complete boundary separation between the internal workspace (`/auth/*`) and customer portal (`/portal/*`) utilizing independent JWT secrets (`JWT_SECRET` vs `PORTAL_TOKEN_SECRET`).
- **Deal Health Detectors:** Automated scanning for stalled quotes, historical rep discount anomalies, and delivery promise slippage.
- **Action Bar Notifications:** Built-in notification bell aggregating pending approvals, active negotiations, and health alerts.

---

## Technology Stack

### Frontend
- **React 18** - Component-based user interface
- **TypeScript** - Strict end-to-end type safety
- **Vite 6** - Next-generation frontend tooling and build pipeline
- **Tailwind CSS 3** - Custom neo-glassmorphic styling system
- **React Router 6** - Client-side routing with role-based guards

### Backend
- **Node.js (v20+) & Express 4** - RESTful API backend
- **TypeScript** - Full backend type safety
- **Prisma ORM 6** - Type-safe database queries and migrations
- **Zod 3** - Strict runtime request validation
- **PDFKit & XLSX** - Native server-side document generation for PDF and Excel exports
- **Pure Algorithmic Engines** - Zero-dependency mathematical engines for discount risk, split allocation, and proration

### Database & Storage
- **PostgreSQL 16** - Relational data store (41 tables in schema)
- **Decimal Precision** - `Decimal(14,2)` for currency and `Decimal(6,2)` for percentages (no floating-point drift)

### DevOps & Development Tools
- **Docker & Docker Compose** - Containerized PostgreSQL instance
- **npm Workspaces** - Monorepo architecture (`shared`, `backend`, `frontend`)
- **Vitest 3** - Fast unit test runner for business engines
- **Playwright** - End-to-end browser verification

---

## System Architecture

![DealFlow360 System Architecture](docs/architecture.png)

### Architectural Layer Breakdown

1. **Dual-Surface Client Layer:**
   - **Internal Sales Workspace:** High-velocity, role-adaptive workspace for sales reps, managers, finance, and admins (`/dashboard`, `/quotations`, `/approvals`, etc.).
   - **Restricted Customer Portal:** Customer-safe, isolated negotiation environment (`/portal/*`) where internal pricing ceilings, risk scores, and margins are stripped out.
2. **API & Security Boundary:**
   - **Surface Token Isolation:** Independent JWT tokens (`JWT_SECRET` vs `PORTAL_TOKEN_SECRET`) prevent portal contacts from accessing internal endpoints even via URL tampering.
   - **Path-Scoped RBAC:** Endpoints are individually guarded with role-based policies.
3. **Pure Algorithmic Core (The Judged Differentiator):**
   - Pure mathematical engines (`discount-engine.service.ts`, `split-allocator.ts`, `proration.ts`) operate with **zero Express, Prisma, or runtime I/O dependencies**. They evaluate plain data objects using integer hundredths, guaranteeing deterministic behavior across all tests and workflows.
4. **Persistence & Auditing:**
   - Single source of truth in PostgreSQL 16 managed via Prisma ORM across 41 relational tables.
   - All state mutations (discounts, approvals, manual fulfillment overrides, stock adjustments) automatically write immutable records to the `audit_log` table.

---

## Project Structure

```
dealflow360/
├── shared/                         # @dealflow360/shared — Types shared across FE & BE
│   └── types/                      # Universal domain interfaces & enums
├── backend/                        # Express API & business logic
│   ├── prisma/                     # Database schema (41 tables) and seed script
│   │   ├── schema.prisma
│   │   └── seed.ts
│   ├── src/
│   │   ├── modules/                # Domain-driven backend modules
│   │   │   ├── approvals/          # Multi-level approval state machine
│   │   │   ├── auth/               # Internal staff authentication
│   │   │   ├── billing/            # Invoicing and payment reconciliation
│   │   │   ├── deal-health/        # Anomaly detectors (Stalled, Anomaly, Slippage)
│   │   │   ├── discount-engine/    # Pure blended risk scoring engine
│   │   │   ├── discount-tiers/     # Discount ceilings & approval chain rules
│   │   │   ├── fulfillment/        # Warehouse split allocator & backorders
│   │   │   ├── inventory/          # Stock movements & reorder management
│   │   │   ├── negotiation/        # Staff-side negotiation response handlers
│   │   │   ├── portal/             # Customer-facing quotation negotiation
│   │   │   ├── portal-auth/        # Customer portal authentication
│   │   │   ├── products/           # Catalog, variants & price lists
│   │   │   ├── quotations/         # Quote builder, lines & recomputation
│   │   │   ├── recommendations/    # Upsell / cross-sell scoring & pairings
│   │   │   ├── reporting/          # Aggregations, PDF & XLSX exports
│   │   │   └── subscriptions/      # Recurring plans & pure proration engine
│   │   ├── middleware/             # Auth, Portal-Auth, RBAC, Validation
│   │   └── shared/audit/           # Transactional audit logging
│   └── tests/                      # Vitest unit test suites
└── frontend/                       # React 18 frontend
    └── src/
        ├── components/
        │   ├── layout/             # InternalNav, InternalLayout, PortalLayout, NotificationBell
        │   └── ui/                 # Reusable design tokens (Button, Card, Table, Badge)
        ├── features/               # Domain API clients and custom hooks
        └── pages/
            ├── internal/           # Internal screens (Dashboard, Quotations, Approvals, etc.)
            └── portal/             # Customer portal screens (Overview, Negotiation, Messages)
```

---

## Setup & Installation

### Prerequisites
- **Node.js 20+**
- **npm 10+**
- **Docker & Docker Compose**

### Local Development

1. **Clone the repository:**
   ```bash
   git clone https://github.com/amarnotcool/DealFlow360_trial.git
   cd DealFlow360_trial
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

3. **Environment setup:**
   ```bash
   cp backend/.env.example backend/.env
   cp frontend/.env.example frontend/.env
   ```

4. **Database setup (PostgreSQL in Docker):**
   ```bash
   # Start PostgreSQL 16 container (Host port 5433)
   docker compose up -d

   # Build shared TypeScript types
   npm run build -w @dealflow360/shared

   # Generate Prisma client and apply migrations
   npm run prisma:generate -w backend
   npm run prisma:migrate -w backend

   # Seed database with demo accounts, products, warehouses, and quotations
   npm run seed
   ```

5. **Start development servers:**
   ```bash
   npm run dev
   ```
   - **Internal Sales Workspace:** [http://localhost:5173](http://localhost:5173)
   - **Customer Negotiation Portal:** [http://localhost:5173/portal/login](http://localhost:5173/portal/login)
   - **Backend API:** [http://localhost:4000](http://localhost:4000)
   - **API Health Check:** [http://localhost:4000/health](http://localhost:4000/health)

---

## Default Seed Accounts & Credentials

All seeded accounts use the universal password: **`dealflow360`**

### Internal Staff Accounts (`/login`)
| Role | Email | Name | Focus Area |
|---|---|---|---|
| **Sales Rep** | `rep@dealflow360.test` | Riya Sales Rep | Build quotes, apply discounts, upsell suggestions |
| **Sales Manager** | `manager@dealflow360.test` | Manav Sales Manager | Approve/reject quotes, configure discount tiers, deal health |
| **Finance / Ops** | `finance@dealflow360.test` | Farah Finance | Tier-2 high-risk approvals, split overrides, hybrid billing |
| **Administrator** | `admin@dealflow360.test` | Anaya Admin | Full system configuration, products, staff users, reporting |

### Customer Portal Accounts (`/portal/login`)
| Customer | Tier | Contact Email | Contact Name |
|---|---|---|---|
| **Acme Corp** | Gold (15% ceiling) | `aarti@acme.test` | Aarti Buyer |
| **Globex Industries** | Silver (12% ceiling) | `gita@globex.test` | Gita Rao |
| **Initech** | Bronze (10% ceiling) | `ishan@initech.test` | Ishan Mehta |

---

## Key Features Breakdown

### 1. The Blended Discount Risk Engine
Evaluates quotes line-by-line against `min(tierCeiling, categoryCeiling)`.

$$\text{Applicable Ceiling} = \min(\text{Tier Ceiling}, \text{Category Ceiling})$$
$$\text{Overage} = \max(0, \text{Discount Given} - \text{Applicable Ceiling})$$
$$\text{Blended Score} = 0.6 \times \max(\text{Line Overages}) + 0.4 \times \sum(\text{Line Overages})$$

*Worked Example:*
- Customer: **Acme Corp** (Gold tier: 15% ceiling)
- Product 1: **Laptop Pro 14** (Hardware ceiling 15%) @ **12% discount** → 0pt overage.
- Product 2: **Onsite Setup Service** (Services ceiling 10%) @ **18% discount** → **8pt overage**.
- Even though 15% sounds acceptable for a Gold customer, the service line breached its stricter limit. The blended score flags **8.00 / HIGH Risk** and automatically routes to **Sales Manager → Finance**.

### 2. Multi-Warehouse Split Allocator
Optimizes fulfillment across warehouses (`split-allocator.ts`):
1. **Shipment Minimization:** Prefers single-warehouse coverage; reuses warehouses already shipping earlier order lines.
2. **Cost Weight Optimization:** Breaks ties using warehouse `shipping_cost_weight`.
3. **Automated Backorder Handling:** Unfulfilled shortfalls generate backorder records, with a **"Consolidate Remaining Backorder"** action once stock arrives.

### 3. Hybrid Billing & Proration
Mixes one-time and subscription lines on a single order:
- **One-time physical items:** Invoiced upon shipment dispatch.
- **Recurring subscriptions:** Invoiced on billing schedule cycles (`MONTHLY`, `QUARTERLY`, `ANNUAL`).
- **Mid-Cycle Proration:** Computes daily rate differences over cycle days (`30`, `90`, `365`). Mid-cycle cancellations automatically issue a `CreditNote`.

### 4. Customer Portal Negotiation
Customers negotiate live through an isolated portal:
- Line comments and counter-discount proposals.
- Staff can review, accept, or reject counters in their workspace.
- If terms are accepted that breach approval thresholds, the deal automatically re-enters the approval chain.

### 5. Deal Health Monitoring
Three real-time detectors:
- **`STALLED_DEAL`:** Quotations inactive for more than 14 days.
- **`DISCOUNT_ANOMALY`:** Discounts >15 points above the specific rep's historical average.
- **`DELIVERY_SLIPPAGE`:** Confirmed orders with unfulfilled shipments past the promised delivery date.

---

## Useful Commands

```bash
# Run backend and frontend concurrently
npm run dev

# Run Vitest test suite (16 tests green)
npm test -w backend

# Run full monorepo typecheck (0 errors)
npm run typecheck

# Build all packages for production
npm run build

# Recompile shared TypeScript definitions
npm run build -w @dealflow360/shared

# Re-seed the PostgreSQL database
npm run seed
```

---

## License & Credits

Built for the **Odoo Hackathon 2026**.  
Designed and engineered as a self-governing B2B sales operations platform.
