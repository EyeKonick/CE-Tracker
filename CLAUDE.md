# CLAUDE.md — CE Tracker
> Attendance & CE Points Tracking Web App  
> College of Computer Studies — Advising Office

---

## ⚠️ ALWAYS DO FIRST — NO EXCEPTIONS

**Before writing ANY frontend code, every single session:**

```
Invoke the frontend-design skill at /mnt/skills/public/frontend-design/SKILL.md
```

Read it fully before touching a single component, page, or style. No exceptions. Even for small UI fixes.

---

## Project Overview

CE Tracker is a web-based attendance and CE (Co-Curricular Engagement) points tracking application for the College of Computer Studies. It allows teachers and advisers to create events, track student attendance in real time via unique QR codes, and compute CE points and community service penalties per semester.

**Current scope:** 2 teachers (Adviser + Co-Adviser) managing one shared pool of ~200 students.

---

## Tech Stack

| Layer | Tool | Notes |
|---|---|---|
| Framework | Next.js 14+ (App Router) | Never use Pages Router |
| Language | TypeScript | Always — never plain JS |
| Styling | Tailwind CSS | Utility-first, no separate CSS files |
| Components | shadcn/ui | Pre-built accessible base components |
| Database | Supabase (PostgreSQL) | Free tier |
| Auth | Supabase Auth | Role-based |
| Storage | Supabase Storage | Student selfie photos |
| Forms | React Hook Form + Zod | Always validate both client and server |
| State | Zustand | Global state only (user session, active semester) |
| QR Generate | qrcode.react | Student QR codes |
| QR Scan | html5-qrcode | Camera scanner |
| Date/Time | date-fns | All date formatting |
| Icons | Lucide React | Only icon set — never mix |
| Automation | n8n (self-hosted on Railway) | Email notifications, penalty logging |
| Email | Resend | Transactional email via n8n |
| Deployment | Vercel | Free tier, always on |

---

## Brand & Design System

### Font
**Poppins** — loaded via `next/font/google`

```ts
import { Poppins } from 'next/font/google'

const poppins = Poppins({
  subsets: ['latin'],
  weight: ['300', '400', '500', '600', '700'],
  variable: '--font-poppins',
})
```

Required weights only:
- `300` Light — captions, helper text
- `400` Regular — body text, table data
- `500` Medium — input labels, nav items
- `600` SemiBold — card titles, buttons, badges
- `700` Bold — page titles, display numbers

### Color Tokens

```ts
// Always use these exact values — never deviate
colors: {
  navy:      '#0F1F3D',  // Primary — headers, sidebar, key elements
  blue:      '#1E6FFF',  // Accent — buttons, links, active states
  slate:     '#1E293B',  // Primary text
  offwhite:  '#FAFAFA',  // Page background
  // Semantic — attendance states only
  success:   '#16A34A',  // ON TIME, Approved
  warning:   '#D97706',  // LATE, Pending
  danger:    '#DC2626',  // ABSENT, Rejected
  info:      '#0284C7',  // TIME OUT
  // Neutrals
  gray900:   '#111827',
  gray700:   '#374151',
  gray500:   '#6B7280',
  gray300:   '#D1D5DB',
  gray100:   '#F3F4F6',
}
```

### Design Principles
- **Minimal first** — white space is never wasted space
- **8px grid** — all spacing is a multiple of 8px
- **Poppins only** — never introduce another font
- **Semantic colors only for attendance states** — never use them decoratively
- **One primary action per screen** — never two navy filled buttons side by side
- **Cards are always white** — never colored card backgrounds
- Border radius scale: `4px` badges → `8px` inputs → `12px` cards → `16px` hero cards → `9999px` pills

---

## Project Structure

```
ce-tracker/
├── app/
│   ├── (auth)/
│   │   ├── login/page.tsx
│   │   └── register/page.tsx
│   ├── (dashboard)/
│   │   ├── layout.tsx              # Shared dashboard layout with sidebar
│   │   ├── admin/                  # Super Admin pages
│   │   ├── teacher/                # Teacher / Co-Adviser pages
│   │   └── student/                # Student pages
│   ├── api/                        # Route handlers (backend)
│   │   ├── events/
│   │   ├── attendance/
│   │   └── users/
│   ├── layout.tsx                  # Root layout — Poppins font applied here
│   └── page.tsx                    # Landing / redirect page
│
├── components/
│   ├── ui/                         # Generic: Button, Card, Badge, Input, Modal
│   ├── forms/                      # EventForm, LoginForm, RegisterForm
│   ├── dashboard/                  # Sidebar, StatCard, EventRow, ScanResult
│   └── shared/                     # Components shared across roles
│
├── lib/
│   ├── supabase/
│   │   ├── client.ts               # Browser client
│   │   └── server.ts               # Server client (for Server Components)
│   ├── utils.ts                    # Helper functions (cn, formatDate, etc.)
│   └── validations.ts              # Zod schemas — all form validation
│
├── hooks/
│   ├── useUser.ts                  # Current logged in user + role
│   ├── useEvents.ts                # Event CRUD hooks
│   └── useAttendance.ts            # Live attendance hooks
│
├── types/
│   └── index.ts                    # All TypeScript types in one place
│
├── constants/
│   └── index.ts                    # App-wide constants (roles, CE tier defaults)
│
├── middleware.ts                   # Auth guard — protects all dashboard routes
└── CLAUDE.md                       # This file
```

---

## User Roles

| Role | Access Level |
|---|---|
| `super_admin` | Full system — manage teachers, override records, semester control |
| `teacher` | Create events, validate students, assign officers, reports |
| `student_officer` | Scan QR codes only — assigned by teacher |
| `student` | Generate QR, view own attendance and CE points |

Role is stored in Supabase `profiles` table and checked via middleware and Row Level Security (RLS).

---

## Core Features

### Events
Teacher sets per event: name, date/time, applicable section, CE points, grace period, 3 late tiers (auto-calculated CE points + penalty hours each), absent penalty, Time-Out toggle.

### Attendance States
- `on_time` — scanned within grace period → full CE points, no penalty
- `late_tier_1/2/3` — scanned after grace, reduced CE points (min 1), late penalty hours
- `absent` — no scan → 0 CE points, full absent penalty
- `time_out` — scanned during teacher-opened Time-Out window

### QR System
- Unique per student per event
- Refreshes every 5 minutes automatically
- Student can manually regenerate (max 2 per event — Time-In and Time-Out)
- Screenshot prevention via dynamic refresh (no static QR)
- Scan result overlay shows: student selfie + name + ID + section + status badge (3 seconds)

### CE Points Auto-Distribution (3 Tiers)
```
Full points = 20 (example)
Tier 1 default = 75% = 15pts
Tier 2 default = 50% = 10pts
Tier 3 default = 25% = 5pts  (minimum 1pt — never 0)
Absent = 0pts (locked, cannot be changed)
```
Teacher can override any tier value before saving event.

### Penalties
Community service hours accumulate per semester. Auto-logged by n8n when event ends. Cleared manually by teacher at end of semester via report review.

---

## Supabase & Security Rules

### Row Level Security — Always On
- Students can only read their own `attendance_records`
- Students can only read events assigned to their section
- Teachers can read/write all records within their semester
- Super admin has full access
- Never disable RLS — ever

### Environment Variables
```
# .env.local — NEVER commit this file
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=        # Server-side only — never expose to client
RESEND_API_KEY=
N8N_WEBHOOK_SECRET=
```

### Secret Rules
- `SUPABASE_SERVICE_ROLE_KEY` is NEVER used in client components — server only
- All `.env.local` values are in `.gitignore` — never push secrets to GitHub
- Never hardcode any API key, URL, or secret in source code

---

## Code Standards & Best Practices

### TypeScript — Always
```ts
// Every component, function, hook is typed
// No `any` type — ever
// Define all data shapes in types/index.ts
```

### Server vs Client Components
```ts
// Default = Server Component (no directive needed)
// Add "use client" ONLY when the component needs:
// - useState, useEffect, useRef
// - onClick, onChange, or any event handler
// - Browser APIs (window, document)
// - QR code generation or scanning

// Keep "use client" components as small/leaf as possible
// Never make a layout or page a client component unless absolutely required
```

### Component Rules
- One component per file
- PascalCase for component names and filenames
- Under 100 lines — if longer, split into smaller components
- Props are always typed with TypeScript interface
- No inline styles — Tailwind only

### Form Validation — Always Zod
```ts
// Every form has a Zod schema in lib/validations.ts
// Validate on client (React Hook Form) AND server (API route)
// Never trust user input — always revalidate server-side
```

### Git Commit Convention
```
feat: add QR generation screen for students
fix: late penalty not auto-logging after event ends
chore: update Supabase client to v2
style: update badge colors to match brand guidelines
refactor: split EventForm into smaller components
docs: update CLAUDE.md with new QR rules
```

### File Naming
```
components/    → PascalCase  → EventCard.tsx, StatusBadge.tsx
hooks/         → camelCase   → useAttendance.ts, useUser.ts
lib/           → camelCase   → validations.ts, utils.ts
app/           → kebab-case  → /create-event/page.tsx
```

---

## n8n Automation Workflows

| Trigger | Action |
|---|---|
| New student registers | Email teacher: "New student pending validation" |
| Teacher approves account | Email student: "Your account has been approved" |
| Teacher rejects account | Email student with rejection reason + resubmit instructions |
| New event created | Email all applicable students with event details |
| 1 hour before event | Reminder email to all applicable students |
| Event ends | Auto-log absent penalty for students with no Time-In scan |
| Student misses N events | Flag student — notify teacher via email |

---

## Key Development Rules

1. **Read the frontend-design skill before every session** — no exceptions
2. **Never use Pages Router** — App Router only
3. **Never use `any` in TypeScript** — define proper types
4. **Never disable RLS** in Supabase
5. **Never commit `.env.local`** — secrets stay local
6. **Always validate forms with Zod** — both client and server
7. **Always use Server Components by default** — only add `"use client"` when needed
8. **Always use Poppins** — never introduce another font
9. **Always follow the 8px spacing grid** — no arbitrary spacing values
10. **Always use Lucide icons** — never mix icon sets
11. **One primary button per screen** — never two filled navy buttons
12. **Semantic colors for attendance states only** — not for decoration

---

## Useful Commands

```bash
# Start dev server
npm run dev

# Type check
npx tsc --noEmit

# Lint
npm run lint

# Build for production
npm run build

# Generate Supabase types (run after schema changes)
npx supabase gen types typescript --project-id YOUR_PROJECT_ID > types/supabase.ts
```

---

## Project Setup — Step by Step

This is the exact sequence to follow when starting the project from scratch. Follow every step in order. Do not skip anything.

### Step 1 — Prerequisites
Make sure these are installed before doing anything else:
```bash
node -v        # Must be v18 or higher
npm -v         # Must be v9 or higher
git --version  # Any recent version
```
If any of these are missing, install them first before proceeding.

### Step 2 — Create the Next.js Project
```bash
npx create-next-app@latest ce-tracker
```
When prompted, answer exactly like this:
```
✔ Would you like to use TypeScript?          → Yes
✔ Would you like to use ESLint?              → Yes
✔ Would you like to use Tailwind CSS?        → Yes
✔ Would you like to use the src/ directory?  → No
✔ Would you like to use App Router?          → Yes  ← critical
✔ Would you like to customize the default
  import alias (@/*)?                        → Yes
✔ What import alias would you like?          → @/*
```

### Step 3 — Enter the Project
```bash
cd ce-tracker
```

### Step 4 — Install Core Dependencies
Install all required packages in one command:
```bash
npm install @supabase/supabase-js @supabase/ssr zustand react-hook-form zod @hookform/resolvers date-fns lucide-react qrcode.react html5-qrcode
```

### Step 5 — Install shadcn/ui
```bash
npx shadcn@latest init
```
When prompted:
```
✔ Which style would you like to use?        → Default
✔ Which color would you like to use?        → Slate
✔ Would you like to use CSS variables?      → Yes
```

### Step 6 — Set Up Poppins Font
Open `app/layout.tsx` and add the Poppins import. This is the first file you edit.

### Step 7 — Create Environment Variables File
In the project root create a file named `.env.local` and add:
```
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=
RESEND_API_KEY=
N8N_WEBHOOK_SECRET=
```
You will fill in the values after setting up Supabase. Never commit this file.

### Step 8 — Set Up .gitignore
Make sure `.env.local` is in `.gitignore`. It is added automatically by Next.js but always verify.

### Step 9 — Initialize Git
```bash
git init
git add .
git commit -m "chore: initial Next.js project setup"
```

### Step 10 — Create the Folder Structure
Manually create the following folders inside the project:
```
app/(auth)/login/
app/(auth)/register/
app/(dashboard)/admin/
app/(dashboard)/teacher/
app/(dashboard)/student/
app/api/
components/ui/
components/forms/
components/dashboard/
components/shared/
lib/supabase/
hooks/
types/
constants/
```

### Step 11 — Set Up Supabase Project
1. Go to supabase.com and create a free account
2. Create a new project — name it `ce-tracker`
3. Copy your Project URL and anon key into `.env.local`
4. Copy your service role key into `.env.local`
5. Enable Row Level Security on all tables — always

### Step 12 — Run the Dev Server
```bash
npm run dev
```
Open `http://localhost:3000` — you should see the default Next.js welcome page. If you see it, setup is complete.

### Step 13 — Place CLAUDE.md and SESSIONS.md in Project Root
Copy both `CLAUDE.md` and `SESSIONS.md` into the root of your `ce-tracker` folder. Claude will read both every session.

---

## Session Protocol

**Every session starts with:**
```
Read CLAUDE.md and SESSIONS.md before doing anything.
Continue from where we left off. Do not skip ahead.
```

**Every session ends with:**
Update `SESSIONS.md` — fill in what was completed, what you learned, any issues, and exactly where to start next session.

---

## Learning Approach — How Claude Will Teach You

> The developer is learning React and Next.js by building this real project from scratch.

### The Rule — You Write, Claude Guides

**Claude will never just generate code and then explain it afterward.**

Instead every feature is taught like this:

1. **Concept first** — Claude explains what we are about to build and why, what React or Next.js concept it uses, and what the code will look like before you write a single character
2. **You type it** — Claude tells you exactly what to write, line by line or block by block, with explanation as you go
3. **Understand before moving** — Claude explains each decision in plain language before moving to the next step
4. **Why, not just what** — Claude always explains why something is written a certain way, not just what it does

### What This Means in Practice

- Claude will say: *"Now add this line — here is what it does and why we need it here"*
- Claude will NOT paste a 100-line file and say *"here is what was added"*
- If a concept is new, Claude explains it with a simple analogy before showing the code
- If there is a best practice reason for doing something a specific way, Claude says it out loud
- Every session starts by confirming where we left off and what we are building next

### What You Should Do
- Type every line yourself — do not copy paste from Claude
- Ask "why" whenever something is unclear — no question is too basic
- If something does not make sense, say so before moving forward
- Run the code after every meaningful change to see it working

---

*CE Tracker — CLAUDE.md v1.1 | February 2026*
