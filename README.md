# AICTE Integrated Complaint Monitoring & Tracking Dashboard
### Smart India Hackathon (SIH PS 26264)
> **Single-Window Dashboard for Monitoring, Tracking, and Cross-Bureau Overlap Resolution of Institution-Related Complaint Cases Across AICTE Bureaus & Cells**

---

## 🏛️ Problem Statement Overview

The **All India Council for Technical Education (AICTE)** receives thousands of grievances, whistleblowing disclosures, and regulatory complaints annually from students, faculty, parents, and civil society. These complaints are traditionally handled in silos across distinct autonomous bureaus:

- **Regulation Bureau (RB):** Norm compliance, de-recognition proceedings, unauthorized batches, infrastructure lapses.
- **Public Grievance Redressal Cell (PGRC):** Student fee refunds, certificate withholding, faculty salary arrears, victim harassment.
- **Approval Bureau (AB):** Annual Extension of Approval (EoA), intake variation, deficiency scrutiny.
- **Legal Cell (LC):** High Court & Supreme Court writ petitions, standing counsel directives, compliance affidavits.
- **Regional Offices (RO):** On-ground fact-finding committees, inspections, and regional ombudsman coordination.

### The Problem
Without an integrated tracking mechanism:
1. **Conflicting Inter-Bureau Decisions:** PGRC might grant fee refund mediation while RB issues a closure notice; or Legal Cell may submit counter-affidavits unaware of active RB scrutiny.
2. **Duplication of Administrative Burden:** Multiple officers independently serve inquiry notices to the same institution.
3. **SLA Breaches & Lack of Accountability:** Cases exceed statutory 30-day turnaround without inter-bureau coordination or automated escalation.

---

## 🚀 Key Architectural Innovations & Features

### 1. ⚡ Cross-Bureau Overlap Detection & Coordination Engine
- **Multi-Bureau Overlap Engine (`src/lib/overlapSync.ts` & `src/lib/overlapEngine.ts`):** Evaluates all non-disposed cases against an AICTE Permanent ID (PID). When complaints span $\ge 2$ distinct bureaus, the system flags the institution with active overlaps and synchronizes case IDs.
- **High-Visibility Overlap Banner:** Alerts officials immediately with `"Potential Overlapping Cases – Coordination Required"`.
- **Inter-Bureau Coordination Workspace:**
  - Designates a **Lead Bureau Coordinator** (e.g., RB leading regulatory sanctions while harmonizing PGRC fee claims).
  - Synchronized directives and joint resolution workflows prevent conflicting notices.
  - Manual case linking (`POST /api/cases/[id]/link`) allows officers to cross-reference related complaints.

### 2. ⏱️ Dynamic SLA Timers, Ageing Analysis & Auto-Escalation
- Real-time SLA calculation based on statutory 30-day limits.
- Dynamic days pending and overdue flags (`calculateDaysPending`, `calculateIsOverdue`, `isApproachingDeadline`).
- Ageing brackets: `< 15 Days`, `15–30 Days (Approaching SLA)`, `31–60 Days (Breached)`, `> 60 Days (Critical)`.
- **Automated Escalation Engine (`src/lib/slaJob.ts`):** Automatically detects cases overdue by more than 15 days, escalates them to the Member Secretary, and dispatches in-app notifications.

### 3. 🌐 Public Citizen Grievance Portal (`/citizen`)
- **Fuzzy Institution Resolution:** Employs Levenshtein distance and token Jaccard similarity to suggest matching colleges when citizens do not have an exact PID, eliminating accidental misattribution.
- **AI Duplicate Complaint Detection:** Detects duplicate submissions by complainant email, phone, and subject similarity before case creation.
- **Multipart Document Uploads (`POST /api/uploads`):** Allows citizens to attach PDFs and images (up to 10MB) stored in secure uploads.
- **Secure Public Case Tracking (`/citizen/track/[id]`):** Requires complainant email verification and implements in-memory rate limiting (10 req/min), preventing data scraping and sanitizing sensitive internal officer data.

### 4. 🔒 Enterprise Security & State-Driven Workflow
- **Stateless JWT Cookie Authentication (`jose`):** HTTP-only, SameSite=Lax session cookies with 8-hour expiration.
- **Bcrypt Password Verification:** Demo accounts secured with salted bcrypt hashes.
- **Role-Based Access Control (RBAC):** Strict view and operational filtering by bureau, leadership, and institution PID.
- **State Machine Transitions (`POST /api/cases/[id]/status`):** Enforces strict state transitions (`PENDING` $\rightarrow$ `UNDER_PROCESS` $\rightarrow$ `ACTION_TAKEN` $\rightarrow$ `DISPOSED`), mandatory remarks on disposal/escalation, and transfer acceptance handoffs (`POST /api/cases/[id]/accept`).

### 5. 📊 Real-Time Analytics & jsPDF Statutory Reporting
- **Executive Scorecards:** Live KPI metrics for active cases, overlap alerts, SLA breaches, and disposal rates.
- **Recharts Analytics:** Monthly and quarterly intake vs. disposal trends, bureau workload, regional office breakdown, and grievance categorization.
- **One-Click jsPDF Export:** Generates official statutory compliance reports with tabular case summaries and AICTE council stamps.

---

## 🛠️ Technology Stack

| Layer | Technology |
|---|---|
| **Framework** | Next.js 16 (App Router with Turbopack, React 19) |
| **Language** | TypeScript 5 (Strict Mode, 0 errors) |
| **Styling** | Tailwind CSS 4 |
| **Database & ORM** | SQLite + Prisma ORM 6.19.3 |
| **Authentication** | JWT (`jose`) in HTTP-only cookies + `bcryptjs` |
| **Visualizations** | Recharts (Responsive charts) |
| **Exporting** | jsPDF + jsPDF-AutoTable |
| **Icons** | Lucide React |

---

## 💻 Setup & Installation Guide

### Prerequisites
- **Node.js**: v18.17+ or v20+ (tested on Node v24)
- **npm**: v9+ or v10+

### Step-by-Step Setup

1. **Clone the repository and enter the directory:**
   ```bash
   git clone <repository-url>
   cd aicte-integrated-dashboard
   ```

2. **Install project dependencies:**
   ```bash
   npm install
   ```

3. **Configure Environment Variables:**
   Copy the example environment file:
   ```bash
   cp .env.example .env
   ```
   *(Defaults: `DATABASE_URL="file:./dev.db"`, `AUTH_SECRET="aicte-secure-jwt-secret-key-2026"`, `PORT=3000`)*

4. **Initialize and Seed the Database:**
   ```bash
   # Push schema to SQLite
   npx prisma db push

   # Seed initial institutions, cases, users, and audit trails
   npx prisma db seed
   ```

5. **Run the Development Server:**
   ```bash
   npm run dev
   ```
   Open **[http://localhost:3000](http://localhost:3000)** in your browser.

6. **Production Build & Verification:**
   ```bash
   npx tsc --noEmit
   npx eslint src
   npm run build
   ```

---

## 👥 Demo Personas & Credentials

All demo accounts use password: **`password123`** (or click any profile chip on the login screen for instant one-click login).

| Persona | Name | Email | Role | Scope / Permissions |
|---|---|---|---|---|
| **AICTE Super Admin** | Prof. T. G. Sitharam | `admin@aicte-india.org` | `AICTE_SUPER_ADMIN` | Full apex governance across all bureaus, institutions, workflows, and exports |
| **Member Secretary** | Prof. Rajive Kumar | `ms@aicte-india.org` | `LEADERSHIP` | Council executive oversight, cross-bureau coordination, SLA escalation receiver |
| **Regulation Bureau Head** | Dr. R. K. Soni | `rb.director@aicte-india.org` | `RB_OFFICER` | RB cases, Show Cause orders, compliance inspections, lead coordination |
| **PGRC Head** | Dr. Amit Dutta | `pgrc.head@aicte-india.org` | `PGRC_OFFICER` | Public grievance redressal, student/faculty welfare, fee refund disputes |
| **Approval Bureau Adviser** | Dr. Ramesh Unnikrishnan | `ab.adviser@aicte-india.org` | `APPROVAL_OFFICER` | Extension of Approval (EoA), intake reductions, deficiency scrutiny |
| **Legal Cell Head** | Adv. Sushil Sharma | `legal.cell@aicte-india.org` | `LEGAL_OFFICER` | High Court & Supreme Court writ petitions, compliance affidavits, appeals |
| **Apex Institute Principal** | Dr. Virendra Saxena | `principal@apextech.edu.in` | `INSTITUTION_USER` | Restricted strictly to Apex Institute (PID: `1-3324512341`) |
| **St. Jude Principal** | Dr. Joseph Xavier | `admin@stjude.ac.in` | `INSTITUTION_USER` | Restricted strictly to St. Jude College (PID: `1-4928104821`) |
| **Kalinga Academy Director** | Prof. Subroto Mohapatra | `director@kalinga.edu.in` | `INSTITUTION_USER` | Restricted strictly to Kalinga Academy (PID: `1-7829104712`) |

---

## 🎯 Evaluator Demo Walkthrough

### 1. Dual-Bureau Overlap (Apex Institute of Technology & Engineering)
- **Login as:** `Prof. T. G. Sitharam` (`admin@aicte-india.org`) or `Prof. Rajive Kumar` (`ms@aicte-india.org`).
- **Observation:** Notice the top alert banner: **"2 Institutions with Overlapping Multi-Bureau Cases Detected"**.
- **Inspect:** Click **"Open Coordination Workspace"** or navigate to the **Overlap Hub**.
- **Case 1 (PGRC):** `AICTE/2026/PGRC-1092` — Student caution money refund grievance.
- **Case 2 (RB):** `AICTE/2026/RB-2041` — Unauthorized batch of 60 B.Tech AI students.
- **Resolution:** Click **"Initiate Joint Review"**, designate Regulation Bureau as the Lead Bureau, formulate shared directives, and observe instant audit log synchronization.

### 2. Triple-Bureau Overlap & Legal Sub-Judice Protection (St. Jude College)
- Inspect **St. Jude College of Engineering & Research** (PID: `1-4928104821`).
- Active simultaneously across:
  - **PGRC:** Faculty 7th Pay Commission salary arrears.
  - **RB:** Building safety norms and lab deficiency.
  - **Legal Cell:** High Court Writ Petition challenging AICTE inspection.
- Notice how the system prevents sub-judice contradiction by linking the legal standing counsel directly with the RB inspection officer.

### 3. Citizen Grievance Submission with Fuzzy Search & Attachment
- Log out and click **"Citizen Portal"** (or visit `/citizen`).
- Click **"File a Grievance"**.
- In the Institution search field, enter a partial or misspelled name, e.g., *"Apex Tech"* or *"St Jude"*.
- The system executes fuzzy search (Levenshtein + token Jaccard) and renders exact matches. Select **Apex Institute**.
- Fill complaint details, attach an evidence document (PDF/PNG), and submit.
- The system generates a collision-free tracking ID (e.g. `AICTE/2026/PGRC-0004`) and flags duplicate complaints if matching contact/content is detected.

### 4. Case Transfer & Bureau Acceptance
- Login as **Dr. R. K. Soni** (Regulation Bureau).
- Open any RB case, click **"Transfer Case"**, select **Legal Cell**, and provide a reason.
- Log in as **Adv. Sushil Sharma** (Legal Cell).
- Open the transferred case and click **"Accept Case"** to take official jurisdiction and update the audit log.

### 5. Automated SLA Scan & Member Secretary Escalation
- From the header, click the **Bell Icon** to view unread alerts.
- Click **"Scan SLA Now"** (or call `POST /api/jobs/sla-scan`).
- The engine calculates turnaround days against statutory deadlines. Cases overdue by >15 days are automatically escalated to `ms@aicte-india.org` with an escalation audit action.

### 6. Reports & PDF Statutory Generation
- Navigate to **Reports & Analytics**.
- Switch between **Monthly** and **Quarterly** views to inspect intake vs. disposal trends.
- Click **"Download Official PDF Report"** to generate an automated executive report complete with Council headers and summary tables.

---

## 📡 Complete API Reference

| Method | Endpoint | Description | Auth Required |
|---|---|---|---|
| `POST` | `/api/auth/login` | Authenticate user with bcrypt verification; issues JWT session cookie | Public |
| `POST` | `/api/auth/logout` | Clears `aicte_session` cookie | Authenticated |
| `GET` | `/api/auth/me` | Returns sanitized current user session and permissions | Authenticated |
| `GET` | `/api/cases` | Fetch complaints with search, category, priority, date, and overlap filters | Authenticated |
| `POST` | `/api/cases` | Register a new complaint case with collision-free counter ID | Authenticated |
| `GET` | `/api/cases/[id]` | Fetch single complaint case with full audit trail and attached documents | Authenticated |
| `POST` | `/api/cases/[id]/status` | Update case status with state transition validation and mandatory remarks | Authenticated |
| `POST` | `/api/cases/[id]/transfer` | Transfer case to another bureau with justification and officer reassignment | Authenticated |
| `POST` | `/api/cases/[id]/accept` | Accept a transferred case into the receiving bureau's active caseload | Authenticated |
| `POST` | `/api/cases/[id]/link` | Manually link two related cases across bureaus | Authenticated |
| `POST` | `/api/cases/[id]/note` | Add an internal case proceeding note | Authenticated |
| `POST` | `/api/cases/[id]/reply` | Submit official institutional compliance reply | Authenticated |
| `POST` | `/api/citizen/grievance` | Citizen complaint submission with fuzzy college matching & duplicate detection | Public |
| `GET` | `/api/citizen/track/[id]` | Track complaint status with email validation and rate-limiting | Public |
| `POST` | `/api/uploads` | Multipart file upload (PDF/PNG/JPEG up to 10MB) | Public / Auth |
| `GET` | `/api/documents/[id]` | Download/stream uploaded document attachment | Authenticated |
| `GET` | `/api/institutions` | Fetch institution directory with active case counts and overlap flags | Authenticated |
| `GET` | `/api/institutions/[pid]` | Retrieve institution 360-degree regulatory profile | Authenticated |
| `GET` | `/api/overlaps` | Fetch institutions with cross-bureau overlaps and linked case IDs | Authenticated |
| `POST` | `/api/overlaps/coordinate` | Designate Lead Bureau, joint meeting notes, and shared directives | Authenticated |
| `POST` | `/api/overlaps/resolve` | Mark cross-bureau coordination successfully concluded | Authenticated |
| `GET` | `/api/jobs/sla-scan` | Trigger statutory SLA scan and auto-escalation | Authenticated |
| `GET` | `/api/notifications` | Fetch unread notifications for current user | Authenticated |
| `POST` | `/api/notifications/[id]/read`| Mark notification as read | Authenticated |
| `GET` | `/api/stats` | Aggregate dashboard KPI metrics, bureau counts, and ageing breakdown | Authenticated |

---

## 🛡️ Quality Assurance & Code Integrity

The project adheres to strict type safety and linting standards:

```bash
# TypeScript compilation check (0 errors)
npx tsc --noEmit

# ESLint verification (0 errors, 0 warnings)
npx eslint src

# Production build test
npm run build
```

---

## 📜 License
Developed for the **Smart India Hackathon (SIH)** — AICTE Problem Statement 26264.  
All Rights Reserved © 2026 AICTE & Ministry of Education, Government of India.
# aicte-integrated-dashboard
