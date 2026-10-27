# 🚀 Quick Setup & Run Guide
### AICTE Integrated Complaint Dashboard (SIH PS 26264)

Welcome! This guide will help you get the project up and running on your local machine in under 3 minutes.

---

## 📋 1. Prerequisites

Make sure you have the following installed on your computer:
- **Node.js**: `v18.17.0` or higher (Node 20 or Node 22 recommended)
  - Verify with: `node -v`
- **npm**: `v9.0.0` or higher
  - Verify with: `npm -v`
- **Git** (if cloning via repository)

> [!NOTE]
> No separate database installation (like PostgreSQL or MySQL) is required! The project uses SQLite, which runs completely locally out of the box.

---

## 🛠️ 2. Step-by-Step Installation

### Step 1: Open Terminal in Project Directory
Navigate into the project folder:
```bash
cd aicte-integrated-dashboard
```

### Step 2: Install Dependencies
Install all required packages (Next.js 16, React 19, Prisma, Tailwind 4, etc.):
```bash
npm install
```

### Step 3: Create Environment File
Copy the provided `.env.example` file to `.env`:

**On macOS / Linux:**
```bash
cp .env.example .env
```

**On Windows (Command Prompt):**
```cmd
copy .env.example .env
```

**On Windows (PowerShell):**
```powershell
Copy-Item .env.example .env
```

*(The `.env` contains local defaults like `DATABASE_URL="file:./dev.db"` and `AUTH_SECRET` — no changes needed).*

### Step 4: Setup Database & Seed Initial Data
Run the following two commands to generate the database schema and populate it with sample institutions, complaints, and official users:

```bash
# 1. Create the SQLite database tables
npx prisma db push

# 2. Seed default users, institutions, and overlap cases
npx prisma db seed
```

---

## 🏃 3. Run the Application

Start the local development server:
```bash
npm run dev
```

Once started, open your web browser and go to:
👉 **[http://localhost:3000](http://localhost:3000)**

---

## 🔑 4. Demo Login Credentials

You can log in manually using any of the credentials below, or simply click on any user profile avatar on the login screen for **1-click instant login**.

**Default Password for all accounts:** `password123`

| Role / Designation | User Name | Email Address | Access & Purpose |
|---|---|---|---|
| **AICTE Chairman (Super Admin)** | Prof. T. G. Sitharam | `admin@aicte-india.org` | Full Council overview across all bureaus, institutions, workflows, and exports |
| **Member Secretary (Executive)** | Prof. Rajive Kumar | `ms@aicte-india.org` | Council leadership, cross-bureau overlap coordination, SLA escalation |
| **Regulation Bureau Head (RB)** | Dr. R. K. Soni | `rb.director@aicte-india.org` | Norms enforcement, Show-Cause notices, Lead Bureau coordination |
| **Public Grievance Head (PGRC)** | Dr. Amit Dutta | `pgrc.head@aicte-india.org` | Student/faculty grievances, fee disputes, joint coordination |
| **Approval Bureau Adviser (AB)** | Dr. Ramesh Unnikrishnan | `ab.adviser@aicte-india.org` | Extension of Approval (EoA), intake changes, scrutiny deficiencies |
| **Legal Cell Head (LC)** | Adv. Sushil Sharma | `legal.cell@aicte-india.org` | High Court & Supreme Court writ petitions, sub-judice coordination |
| **Apex Institute Principal** | Dr. Virendra Saxena | `principal@apextech.edu.in` | Institutional compliance portal for Apex Institute (PID: `1-3324512341`) |
| **St. Jude College Principal** | Dr. Joseph Xavier | `admin@stjude.ac.in` | Institutional compliance portal for St. Jude College (PID: `1-4928104821`) |

---

## 🌐 5. Public Citizen Grievance Portal

Anyone can lodge and track complaints without logging in:
- **Lodge Grievance:** Go to **[http://localhost:3000/citizen](http://localhost:3000/citizen)**
  - Includes **Fuzzy College Search** (type partial names like "Apex" or "St Jude").
  - Includes **AI Duplicate Complaint Detection** and document attachment uploads.
- **Track Complaint:** Switch to the **Track Grievance** tab, enter the Case ID (e.g., `AICTE/2026/PGRC-1092`) and complainant email to view live status.

---

## 🧪 6. What to Test & Explore

1. **Multi-Bureau Overlap Alert:**
   - Log in as **Member Secretary** (`ms@aicte-india.org`) or **Chairman**.
   - Notice the amber alert banner: *"2 Institutions with Overlapping Multi-Bureau Cases Detected"*.
   - Click **"Open Coordination Workspace"** to see cases spanning both RB and PGRC for *Apex Institute*.
2. **Accept Transferred Case:**
   - Transfer an RB case to Legal Cell.
   - Log in as **Adv. Sushil Sharma** (`legal.cell@aicte-india.org`) and click **"Accept Case"**.
3. **SLA Scanner & In-App Alerts:**
   - Click the **Bell Icon** in the top navigation bar.
   - Click **"Scan SLA Now"** to execute the SLA check and view automated escalations.
4. **Reports & PDF Export:**
   - Navigate to the **Reports & Analytics** tab in the sidebar.
   - Toggle Monthly/Quarterly intake trends.
   - Click **"Download Official PDF Report"** to generate a statutory PDF document.

---

## ❓ Troubleshooting & FAQs

- **Port 3000 is already in use:**
  Run on a different port:
  ```bash
  PORT=3001 npm run dev
  ```
- **Want to reset the database back to clean demo data?**
  Run:
  ```bash
  npx prisma db push --force-reset
  npx prisma db seed
  ```
- **Check TypeScript & Linting:**
  ```bash
  npx tsc --noEmit
  npx eslint src
  ```
