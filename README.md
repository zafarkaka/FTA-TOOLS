[README.md](https://github.com/user-attachments/files/32321263/README.md)
# ReconcilePro 🚀
### Enterprise Bank Reconciliation & UAE FTA VAT 201 Compliance SaaS Platform

**ReconcilePro** is a production-ready, high-precision accounting web application built for automated Bank Statement Reconciliation and UAE Federal Tax Authority (FTA) VAT 201 Return Reconciliation.

It eliminates floating-point rounding errors by performing all financial computations using **`Decimal.js`**, supports multi-stage matching algorithms (Exact, Date Tolerance, Reference, Fuzzy Description, 1-to-Many Batch, and Many-to-1 Split), provides immutable audit logging, and enforces strict tenant isolation.

---

## Key Highlights

- **Bank Reconciliation Engine**:
  - **Stage 1 (Exact Match)**: 100% confidence match on Amount + Date + Reference.
  - **Stage 2 (Reference Match)**: Reference/Invoice number match across UK/US/UAE formats.
  - **Stage 3 (Date Tolerance Window)**: Configurable tolerance window (0 to 7+ days).
  - **Stage 4 (1-to-Many Batch Match)**: Matches 1 bulk bank deposit to multiple bookkeeping entries.
  - **Stage 5 (Many-to-1 Split Payment Match)**: Matches split bank payments to a single invoice.
  - **Stage 6 (Mismatch & Duplicate Flags)**: Detects `AMOUNT_MISMATCH`, `DATE_MISMATCH`, `MISSING_IN_BOOKS`, `MISSING_IN_BANK`, `DUPLICATE`.
  - **Stage 7 (Fuzzy Multi-Factor Scoring)**: Levenshtein distance + Token Jaccard similarity.
- **UAE FTA VAT 201 Compliance Module**:
  - Reconciles official declared FTA Return figures against internal Accounting VAT ledgers.
  - Generates side-by-side returns: Box 1 (Standard Rated Sales), Box 1b (Output VAT 5%), Box 9 (Standard Rated Purchases), Box 9b (Input VAT 5%), and Box 14 (Net VAT Payable/Refundable).
  - Validates 15-digit UAE TRNs (`100XXXXXXXXX003`).
- **Financial Precision (`Decimal.js`)**:
  - Zero JavaScript IEEE 754 floating-point rounding artifacts (`0.1 + 0.2 === 0.3`).
- **Multi-Tenant RBAC Security**:
  - All database tables scoped to `organizationId`.
  - Roles: `SUPER_ADMIN`, `COMPANY_ADMIN`, `ACCOUNTANT`, `VIEWER`.
- **Instant 1-Click Demo Sandbox**:
  - Pre-seeded with UAE enterprise scenarios (`Al Tareeq Al Mumayaz Trading LLC`, TRN `100234567800003`).
- **Statutory Audit Reports**:
  - Instant exports in **Excel (.xlsx)**, **Official PDF**, and **CSV** with standard UAE headers and footers.

---

## Technology Stack

- **Framework**: Next.js 14 (App Router)
- **Language**: TypeScript
- **Styling**: Tailwind CSS & Lucide Icons
- **Database & ORM**: PostgreSQL / SQLite with Prisma ORM
- **Precision Math**: Decimal.js
- **File Parsers**: Papaparse (CSV), XLSX / SheetJS (Excel), PDF Table Parser Fallback
- **Exports**: jsPDF & jsPDF-AutoTable, XLSX
- **Visual Analytics**: Recharts
- **Validation**: Zod
- **Testing**: Vitest (56 Automated Unit & Integration Tests)

---

## System Architecture

```
reconcile-pro/
├── prisma/
│   ├── schema.prisma          # Multi-tenant schema (Org, Users, Bank, Books, FTA, VAT, Audit)
│   └── seed.js                # Full UAE enterprise demo seed dataset
├── public/
│   └── demo/                  # Pre-packaged sample CSV files (Bank, Books, FTA, VAT)
├── src/
│   ├── app/                   # Next.js App Router (Dashboard, Wizards, VAT, Reports, Auth)
│   │   ├── api/               # REST API endpoints (Auth, Imports, Reconciliation, VAT, Settings)
│   │   ├── bank-reconciliation/# 7-Step Bank Reconciliation Wizard
│   │   ├── vat-reconciliation/ # UAE FTA VAT 201 Return Workspace
│   │   ├── dashboard/         # Financial KPI cards & Recharts
│   │   ├── imports/           # File import history
│   │   ├── history/           # Archived reconciliations
│   │   ├── reports/           # PDF / Excel / CSV export center
│   │   ├── settings/          # Organization & tolerance rules
│   │   ├── users/             # Team & RBAC management
│   │   └── audit-logs/        # Immutable compliance log
│   ├── components/            # UI components and AppShell
│   └── lib/
│       ├── decimal.ts         # Precision financial arithmetic utilities
│       ├── auth.ts            # JWT sessions, bcrypt hashing, RBAC guards
│       ├── prisma.ts          # Database client singleton
│       ├── engine/
│       │   ├── bank/          # Bank Matching Engine & Rules
│       │   └── vat/           # UAE FTA VAT Engine
│       ├── parsers/           # BankStatementParser, FTAParser, AccountingDataProvider
│       └── services/          # NormalizationService, ReportGenerator, AuditService
└── tests/                     # 56 Automated Vitest test suites
```

---

## Getting Started

### 1. Prerequisites
- Node.js 18+ or 20+
- npm

### 2. Clone & Install
```bash
npm install
```

### 3. Database Setup & Demo Seed
```bash
# Push schema to SQLite database (or Postgres)
npx prisma db push

# Seed demo UAE organization and reconciliations
node prisma/seed.js
```

### 4. Run Development Server
```bash
npm run dev
```
Open [http://localhost:3000](http://localhost:3000) in your browser.

### 5. Instant 1-Click Demo
On the login screen, click **⚡ TRY DEMO (1-CLICK ACCESS)** to instantly access the pre-configured organization (`Al Tareeq Al Mumayaz Trading LLC`) with pre-calculated March 2026 Bank Reconciliation and Q1 2026 UAE FTA VAT Return.

Or log in manually:
- **Email**: `admin@reconcilepro.ae`
- **Password**: `Demo1234!`

---

## Running Automated Tests

Run the full automated test suite (56 tests):
```bash
npm run test
```

Test coverage includes:
- Exact 100% amount + date + reference matching
- Date tolerance window logic (0 to 7 days)
- 1-to-Many batch deposit subset-sum solver
- Many-to-1 split payment matching
- Amount mismatch & date mismatch detection
- Duplicate transaction detection
- UAE 15-digit TRN normalization and validation
- UAE 5% VAT calculations and Net VAT payable formulas
- IEEE 754 floating-point safety with Decimal.js
- File parsers (CSV, XLSX) and column auto-detection
- Multi-tenant data segregation constraints

---

## Production Build & Docker Deployment

### Local Production Build
```bash
npm run build
npm run start
```

### Docker Compose
Run both PostgreSQL and ReconcilePro in isolated containers:
```bash
docker-compose up --build
```

---

## Security & Compliance Notes

1. **Strict Tenant Isolation**: All queries enforce `where: { organizationId: session.organizationId }`.
2. **No Banking Passwords**: ReconcilePro parses standard exported files (CSV/XLSX); it never stores or requests online banking or FTA portal passwords.
3. **Immutable Audit Trail**: Every user login, file upload, manual match, unmatch, adjustment, and report export is recorded with timestamps and user identification.

---

## License
Proprietary — All Rights Reserved. Built for Enterprise UAE Financial Operations.
