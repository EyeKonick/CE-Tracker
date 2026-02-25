# SESSIONS.md — CE Tracker
> Session-by-session progress tracker  
> Update this file at the END of every session

---

## How to Use This File

At the end of every Claude Code session, update:
1. **Last Completed Step** — what was the last thing finished
2. **Current Status** — what state the project is in right now
3. **Next Session Starts At** — exactly where to pick up
4. **Issues & Notes** — anything that broke, was skipped, or needs revisiting

At the START of every new session, paste this into Claude Code:
```
Read CLAUDE.md and SESSIONS.md before doing anything. 
Continue from where we left off. Do not skip ahead.
```

---

## Project Start Date
February 2026

## Current Phase
✅ Phase 0 — Complete
✅ Phase 1 — Supabase Setup, Database Schema, Auth
⬜ Phase 2 — Login & Registration Screens

---

## Phase Overview

| Phase | Description | Status |
|---|---|---|
| Phase 0 | Environment setup, project creation, folder structure | ✅ Complete |
| Phase 1 | Supabase setup, database schema, auth | ⬜ Not Started |
| Phase 2 | Login & registration screens | ⬜ Not Started |
| Phase 3 | Student selfie capture & validation | ⬜ Not Started |
| Phase 4 | Teacher dashboard & event creation | ⬜ Not Started |
| Phase 5 | QR code generation (student) | ⬜ Not Started |
| Phase 6 | QR scanner (teacher / student officer) | ⬜ Not Started |
| Phase 7 | Live attendance screen | ⬜ Not Started |
| Phase 8 | CE points & penalty system | ⬜ Not Started |
| Phase 9 | Student dashboard & attendance history | ⬜ Not Started |
| Phase 10 | Super admin screens | ⬜ Not Started |
| Phase 11 | n8n automation & email notifications | ⬜ Not Started |
| Phase 12 | Reports & export (PDF / Excel) | ⬜ Not Started |
| Phase 13 | Testing, cleanup & deployment to Vercel | ⬜ Not Started |

---

## Session Log

---

### Session 1
**Date:** February 25, 2026
**Duration:** Session 1
**Phase:** Phase 0 — Environment & Project Setup

**What We Did:**
- [x] Verified Node.js and npm versions
- [x] Created Next.js project with correct settings (TypeScript, ESLint, Tailwind, App Router, no src/, @/* alias)
- [x] Installed all core dependencies (Supabase, Zustand, React Hook Form, Zod, date-fns, Lucide, qrcode.react, html5-qrcode)
- [x] Installed and initialized shadcn/ui (Slate base color, Tailwind v4 detected)
- [x] Set up Poppins font in app/layout.tsx and pointed font-sans to it in app/globals.css
- [x] Created .env.local with all required environment variable keys (values blank until Supabase is set up)
- [x] Verified .gitignore includes .env* — all env files are protected
- [x] Made git commits throughout the session
- [x] Created all project folders with .gitkeep placeholders
- [x] Ran dev server successfully at localhost:3000 — default Next.js page visible, tab shows "CE Tracker"
- [x] CLAUDE.md and SESSIONS.md already in project root from the start

**Last Completed Step:**
Phase 0 fully complete — dev server running, Poppins configured, all folders created, project committed to git.

**What I Learned This Session:**

- npx runs a package without permanently installing it; npm installs packages
- next/font/google downloads fonts at build time and serves them locally — faster than a Google CDN link
- .env* in .gitignore protects ALL env files with a wildcard; NEXT_PUBLIC_ prefix exposes a variable to the browser, no prefix means server-only
- Route Groups in Next.js App Router: folders wrapped in (parentheses) group files without affecting the URL path
- Git doesn't track empty folders — .gitkeep is a placeholder file to force git to notice a folder

**Issues Encountered:**

- create-next-app refused to run with `.` because CLAUDE.md, SESSIONS.md, README.md and the PDF were already in the folder. Fixed by moving them to the parent directory temporarily, running create-next-app, then moving them back.
- Typed `weights:` instead of `weight:` in the Poppins config — TypeScript caught it immediately with a red underline.
- Forgot to add `font-sans` to the body className — added it after.

**Next Session Starts At:**
Phase 1 Step 1 — Create Supabase project at supabase.com, set up the database, and fill in .env.local values.

---

### Session 2
**Date:** February 26, 2026
**Duration:** Session 2
**Phase:** Phase 1 — Supabase Setup, Database Schema, Auth

**What We Did:**
- [x] Created Supabase project (ce-tracker, Asia-Pacific / South Asia Mumbai, Healthy)
- [x] Enabled Data API and automatic RLS during project creation
- [x] Copied Project URL, anon key, and service_role key into .env.local
- [x] Ran the full database schema SQL — all 7 tables created (sections, semesters, profiles, students, events, attendance_records, qr_tokens)
- [x] Ran the auto-profile trigger — fires on every new Supabase Auth signup, inserts a row into profiles automatically
- [x] Ran all RLS policies for all 7 tables
- [x] Created lib/supabase/client.ts — browser Supabase client using createBrowserClient
- [x] Created lib/supabase/server.ts — server Supabase client using createServerClient + cookies()

**Last Completed Step:**
Phase 1 fully complete — Supabase project live, schema built, RLS policies in, both Supabase client files created.

**What I Learned This Session:**

- Supabase is a backend-as-a-service — it gives you a PostgreSQL database, auth, storage, and auto-generated API without managing a server
- A database schema is the blueprint of your tables — defines columns, types, and relationships before any data goes in
- Foreign keys (references) link tables together — e.g. students.id references profiles.id
- gen_random_uuid() generates a unique ID automatically for every new row
- timestamptz is a timezone-aware timestamp — always use this over plain timestamp
- check constraints enforce allowed values at the database level (e.g. role must be one of the four defined values)
- A trigger is a function that runs automatically when something happens in the database (e.g. new user signup → create profile row)
- security definer makes the trigger function run with admin-level permissions, bypassing RLS — needed so new users can insert their own profile row on signup
- coalesce(x, y) returns x if x is not null, otherwise returns y
- RLS on + no policies = nobody can access anything from the client
- RLS policies define who can SELECT, INSERT, UPDATE, DELETE on each table
- auth.uid() returns the ID of the currently logged-in user — used in every policy
- Two Supabase clients are needed: one for the browser (createBrowserClient) and one for the server (createServerClient)
- The server client reads login session from cookies via next/headers — the browser client manages cookies automatically
- process.env reads environment variables in code; the ! tells TypeScript the value is guaranteed to exist
- async/await is needed on the server client because cookies() in Next.js 15 is asynchronous

**Issues Encountered:**
- None this session — all SQL ran cleanly on first try

**Next Session Starts At:**
Phase 2 Step 1 — Build the Login page (app/(auth)/login/page.tsx) with React Hook Form + Zod validation + Supabase Auth sign-in

---

### Session 3
**Date:** _______________  
**Duration:** _______________  
**Phase:** _______________

**What We Did:**
- [ ] _______________
- [ ] _______________
- [ ] _______________

**Last Completed Step:**  
_______________

**What I Learned This Session:**  
_______________

**Issues Encountered:**  
_______________

**Next Session Starts At:**  
_______________

---

### Session 4
**Date:** _______________  
**Duration:** _______________  
**Phase:** _______________

**What We Did:**
- [ ] _______________
- [ ] _______________
- [ ] _______________

**Last Completed Step:**  
_______________

**What I Learned This Session:**  
_______________

**Issues Encountered:**  
_______________

**Next Session Starts At:**  
_______________

---

### Session 5
**Date:** _______________  
**Duration:** _______________  
**Phase:** _______________

**What We Did:**
- [ ] _______________
- [ ] _______________
- [ ] _______________

**Last Completed Step:**  
_______________

**What I Learned This Session:**  
_______________

**Issues Encountered:**  
_______________

**Next Session Starts At:**  
_______________

---

### Session 6
**Date:** _______________  
**Duration:** _______________  
**Phase:** _______________

**What We Did:**
- [ ] _______________

**Last Completed Step:**  
_______________

**What I Learned This Session:**  
_______________

**Issues Encountered:**  
_______________

**Next Session Starts At:**  
_______________

---

### Session 7
**Date:** _______________  
**Duration:** _______________  
**Phase:** _______________

**What We Did:**
- [ ] _______________

**Last Completed Step:**  
_______________

**What I Learned This Session:**  
_______________

**Issues Encountered:**  
_______________

**Next Session Starts At:**  
_______________

---

### Session 8
**Date:** _______________  
**Duration:** _______________  
**Phase:** _______________

**What We Did:**
- [ ] _______________

**Last Completed Step:**  
_______________

**What I Learned This Session:**  
_______________

**Issues Encountered:**  
_______________

**Next Session Starts At:**  
_______________

---

### Session 9
**Date:** _______________  
**Duration:** _______________  
**Phase:** _______________

**What We Did:**
- [ ] _______________

**Last Completed Step:**  
_______________

**What I Learned This Session:**  
_______________

**Issues Encountered:**  
_______________

**Next Session Starts At:**  
_______________

---

### Session 10
**Date:** _______________  
**Duration:** _______________  
**Phase:** _______________

**What We Did:**
- [ ] _______________

**Last Completed Step:**  
_______________

**What I Learned This Session:**  
_______________

**Issues Encountered:**  
_______________

**Next Session Starts At:**  
_______________

---

## Concepts Learned Log
> Track every React / Next.js concept you understood for the first time

| Session | Concept | Notes |
| --- | --- | --- |
| 1 | npx vs npm | npx runs a package once without installing; npm installs permanently |
| 1 | next/font/google | Downloads fonts at build time, serves locally — no external CDN request |
| 1 | NEXT_PUBLIC_ prefix | Variables with this prefix are safe for the browser; without it = server only |
| 1 | Route Groups | Folders in (parentheses) in App Router group files without changing the URL |
| 1 | .gitkeep | Empty placeholder file so git tracks an otherwise-empty folder |
| 1 | @theme inline (Tailwind v4) | Design token block in CSS — maps utility classes like font-sans to actual values |
| 2 | Supabase | Backend-as-a-service — PostgreSQL database, auth, storage, auto-generated API |
| 2 | Database schema | Blueprint of tables — columns, types, relationships defined before any data |
| 2 | Foreign keys | references keyword links tables together — deleting parent can cascade to child |
| 2 | gen_random_uuid() | PostgreSQL function that auto-generates a unique ID for every new row |
| 2 | timestamptz | Timezone-aware timestamp — always use over plain timestamp |
| 2 | check constraints | Enforce allowed values at the database level (e.g. role must be one of 4 values) |
| 2 | Database trigger | Function that runs automatically when something happens (e.g. new user → create profile) |
| 2 | security definer | Makes a function run with owner/admin permissions, bypassing RLS |
| 2 | coalesce(x, y) | Returns x if not null, otherwise y — safe default value |
| 2 | RLS policies | Define who can SELECT/INSERT/UPDATE/DELETE — RLS on + no policies = deny all |
| 2 | auth.uid() | Supabase function returning the currently logged-in user's ID |
| 2 | Two Supabase clients | Browser client (auto cookies) vs server client (manual cookies via next/headers) |
| 2 | process.env | How you read .env.local values in code; ! tells TypeScript the value exists |

---

## Errors & Solutions Log
> Track every error you hit and how it was solved — this becomes your personal debugging reference

| Session | Error | Solution |
| --- | --- | --- |
| 1 | create-next-app refused to run in `.` — existing files conflict | Moved CLAUDE.md, SESSIONS.md, README.md, PDF to parent folder, ran create-next-app, moved them back |
| 1 | TypeScript error on Poppins config — `weights` not assignable | Typo: `weights` should be `weight` (no s) |
| 1 | font-sans not using Poppins | Forgot `font-sans` class on body, and needed `--font-sans: var(--font-poppins)` in globals.css @theme block |

---

## Files Created Log
> Track every file created so you always know what exists

| Session | File | Purpose |
| --- | --- | --- |
| 1 | app/layout.tsx | Root layout — Poppins font, metadata, wraps all pages |
| 1 | app/globals.css | Global styles — Tailwind imports, shadcn tokens, font-sans → Poppins |
| 1 | .env.local | Local environment variables — Supabase keys, Resend, n8n (never committed) |
| 1 | lib/utils.ts | shadcn utility helper — created automatically by shadcn init |
| 1 | components.json | shadcn configuration file |
| 2 | .env.local | Filled in: NEXT_PUBLIC_SUPABASE_URL, NEXT_PUBLIC_SUPABASE_ANON_KEY, SUPABASE_SERVICE_ROLE_KEY |
| 2 | lib/supabase/client.ts | Browser Supabase client — uses createBrowserClient from @supabase/ssr |
| 2 | lib/supabase/server.ts | Server Supabase client — uses createServerClient + cookies() from next/headers |

---

*CE Tracker — SESSIONS.md v1.0 | February 2026*  
*Update this file every session — it is your project memory*
