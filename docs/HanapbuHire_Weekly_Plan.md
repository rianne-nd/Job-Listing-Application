# HanapbuHire — Finals Weekly Plan v3 (Pages, Owners, Wireframes, Data Fixes)

> Updated 2026-11-22. Companion to `AppDev_Kotlin_Reference.md` (coding-style bible) and the lesson notes in `Downloads/AppDev/Lessons/md/`.
> Final deadline: **Dec 20, 2026, 11:59 PM** — PDF + APK + video in the shared Google Drive.
> v3 changes: wireframe mapping (Rick's 10 archetypes → our 7 pages + trims), corrected database findings with exact numbers, build-order-by-lesson (what can be coded NOW vs after the Auth lesson), distribution check counting already-done work.

---

# 1. Roster, contributions so far, and the distribution check

Roster: **Edrianne John Puno (Member A)**, **Rick Andrie Santos (Member B)**, **Jade Anne Denise Ciar (Member C)**.

### Already completed (counts toward the PDF contributions table)

| Member | Already done |
| :--- | :--- |
| Edrianne John Puno | Firebase project + app connection, GitHub repo setup, Android Studio project, seeded tbl_job_listings (30 jobs) |
| Rick Andrie Santos | All 10 HTML wireframes (the whole app's design), seeded tbl_applications (30 records) |
| Jade Anne Denise Ciar | Created tbl_users collection and sample user |

### Is the split even? (rough effort check)

| Member | Pages left to build | Extra duties | Already-done weight | Rough total |
| :--- | :--- | :--- | :--- | :--- |
| Edrianne John Puno | Register, Login (2) | bottom-nav shell, security rules, data cleanup, signed APK | Firebase + GitHub + jobs seed | ~11 |
| Rick Andrie Santos | Jobs page + card template (1 page) | search, location filter, loading/empty/error states, final PDF | 10 wireframes + applications seed | ~10 |
| Jade Anne Denise Ciar | Job Details, My Applications, Profile (3) | Apply flow, Edit Profile, video lead, Job Fit Indicator (pair) | users seed | ~10 |

**Verdict: yes, effectively even.** Jade Anne Denise Ciar carries the most pages but the lightest setup history; Edrianne John Puno carries the heaviest infrastructure; Rick Andrie Santos already delivered the entire design. If anyone still wants a tweak, the cleanest swap is moving **Edit Profile** from Jade Anne Denise Ciar to Rick Andrie Santos (same collection style as the Jobs forms). Keep one owner per page either way.

---

# 2. The app's view pages — mapped to Rick's wireframes

Where the page list comes from: the exam brief (app type = job postings + resumes + application tracking, plus its collection schemas), the group's own Week 1 task plan (Register/Login, Job List, Job Details + Apply, My Applications, Profile, bottom navigation), and now Rick's wireframes — all three agree on the same 7 pages.

| # | Page (Activity) | Layout file | Wireframe by Rick Andrie Santos | What the user does there | Firestore | Owner | Week |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| 0 | MainActivity | activity_main.xml | — (no screen; silent redirect) | logged in → Jobs, else → Login | Auth state | Edrianne John Puno | 2 |
| 1 | RegisterActivity | activity_register.xml | create-account-archetype.html | create account (name, email, password) | tbl_users + Auth | Edrianne John Puno | 2 |
| 2 | LoginActivity | activity_login.xml | login.html | sign in; Forgot password (reset-password-archetype.html) | Auth | Edrianne John Puno | 2 |
| 3 | JobsActivity (Home) | activity_jobs.xml | **jobs-feed-archetype.html** (job-listings.html is its duplicate — merge its search bar, then retire it) | browse job cards; search; location filter (filter.html → simplified, location only) | reads tbl_job_listings | Rick Andrie Santos | 2–3 |
| 4 | JobDetailsActivity | activity_job_details.xml | job-details-archetype.html | full job info; Job Fit Indicator; Apply | reads tbl_job_listings, writes tbl_applications | Jade Anne Denise Ciar | 3 |
| 5 | MyApplicationsActivity | activity_my_applications.xml | my-applications-screen-bottom-nav-archetype.html | your applications + status chips + applied date | reads tbl_applications | Jade Anne Denise Ciar | 4 |
| 6 | ProfileActivity | activity_profile.xml | profile-screen.html (trimmed) | view own profile; Edit + Log Out buttons | reads tbl_users | Jade Anne Denise Ciar | 2 |
| 7 | EditProfileActivity | activity_edit_profile.xml | edit-profile-archetype.html (trimmed) | edit name, resume link, skills | writes tbl_users | Jade Anne Denise Ciar | 2/4 |
| — | Job card template | activity_job_item.xml | card design inside jobs-feed | NOT a page — one card copied per job (EJP Display.kt pattern) | — | Rick Andrie Santos | 2 |

### Wireframe trims (keep only what the brief requires)

- **job-listings.html vs jobs-feed-archetype.html: same page twice.** Keep jobs-feed as the card design (it shows salary + location, which exist in the DB), borrow the search bar from job-listings, retire the second file.
- **filter.html:** keep ONLY the Location part as a small dialog on the Jobs page. Drop Employment-type and Minimum-salary filters — `tbl_job_listings` has no employment_type field, and salary is a text range ("₱30,000 - ₱40,000") that would need ugly parsing.
- **edit-profile:** drop Phone and About/Bio — not in the brief's tbl_users schema (user_id, name, email, resume_url + our skills array). Also fix the copy bug: "We only use this to contact you about this **booking**" — leftover from another template.
- **profile-screen:** keep name, single Resume row (one resume_url), skills chips, Edit + Log Out. Drop the multi-file resume list, certifications, bio, and location rows.
- **Job cards:** drop the employment-type chips and "Posted X days ago" — no such fields in the collection. Card = job_title, company_name, location, salary.

Bottom navigation on pages 3, 5, 6: tabs **Jobs / Applications / Profile**, switched with intents (class way — no fragments). The wireframes already show this bar — keep it.

---

# 3. What is in the database (exported Nov 21; re-run: `cd firebase-tools && node export_firestore.js`)

> Row-by-row audit with exact numbers: `Database_Audit.md` (this folder and AndroidStudioProjects) — from a fresh export on 2026-11-22.

| Collection | Records | Exact findings |
| :--- | :--- | :--- |
| tbl_job_listings | 30 | Only **5 unique jobs × 6 copies each** (Software Engineer@Accenture, IT Support Specialist@Jollibee, UI/UX Designer@PLDT, Data Analyst@Globe, DevOps@SM Prime — 6 docs apiece). ONE distinct description across all 30. Every requirements array has exactly 2 items. 5 locations × 6 jobs. |
| tbl_applications | 30 | Statuses: Accepted 8, Under Review 8, Rejected 7, Pending 7. **0 of 30 match a real job id; 0 of 30 match a real user** — every record is orphaned. |
| tbl_users | 1 | One test user (Remus Lupin). **No skills field.** |

**Problems and fixes (basis: firestore_dump.json from the export script — verifiable numbers, not guesses):**

1. **Orphaned applications (all 30).** Their user_id values (user_007-style) exist nowhere in tbl_users, and their job_id values (job_007-style) match none of the 30 real document ids. Whoever seeded tbl_applications used made-up ids while the other two collections used different id styles — three people seeded independently, so the links never met. **Fix:** repoint all sample applications to REAL document ids (Week 2 task 6).
2. **No skills on users.** The Job Fit Indicator compares skills vs requirements; tbl_users has only the 4 brief fields. **Fix:** add a skills array (Week 2 task 6).
3. **5 jobs duplicated 6×, one shared description, uniform 2-item requirements.** Fine for testing, weak for the demo and the PDF. **Fix:** deduplicate to ~10–15 jobs with varied, realistic descriptions and 3–5-item requirements arrays (better fit-indicator demos).
4. **Status vocabulary:** four values already exist (Pending / Under Review / Accepted / Rejected) — keep exactly these four, capitalized, everywhere in the app.
**Key safety:** `firebase-tools/service-account.json` is a MASTER KEY. Never commit it to GitHub, never upload it to Drive; regenerate it if it ever leaks.

---

# 4. Build order by lesson (what can be coded NOW)

Lessons done: Kotlin variables/logic, Android Lifecycle, Android View Groups (layouts) + the Firestore labs (create = Finals LA2 pattern, display = LA3 pattern — see EJP_HandsOn/PUNO_HandsOn). **Authentication has NOT been taught yet.**

| Buildable NOW (taught material) | Must wait for the Auth lesson |
| :--- | :--- |
| All 8 layout XMLs + the card template (View Groups) | Creating real accounts (Auth register) |
| Jobs page display — snapshot listener + card loop (LA3) | Login / logout / password reset wiring |
| Job Details display; My Applications display; Profile display | Session redirect in MainActivity (needs auth state) |
| Edit Profile save; Apply → tbl_applications write (LA2) | Auth-based security rules |
| Duplicate-apply check (listener + loop logic); sample-data cleanup (console) | |

**Interim trick so pages work before Auth:** while Auth is not taught yet, pages that need "the current user" use one constant at the top of the file — `val currentUserId = "user_1" // TODO: replace with the Auth UID once authentication is taught` — and swapping it later is a one-line change per file. Build all the display/write pages NOW with this; wire Auth the week it is taught.

---

# 5. Weekly rhythm (same every week)

- **Sunday 8:00 PM — code freeze:** push, merge, full app runs on the emulator.
- **Progress Report due weekly** (graded deliverable). Assembler: W2 Edrianne John Puno, W3 Rick Andrie Santos, W4 Jade Anne Denise Ciar, W5 Edrianne John Puno, W6 Rick Andrie Santos + Jade Anne Denise Ciar. Own rows + screenshots by Saturday 9 PM.
- **Blockers:** chat within 24 h; ask for help by Wednesday.
- **Style:** follow `AppDev_Kotlin_Reference.md` + the lesson notes in `Lessons/md/`. Class skeleton, `hb` id prefix, lowercase step comments.
- **Reviewer rule:** one other member runs your page before merge; reviewer rotates.
---

# 6. Week 2 (Nov 16–22) — Foundation + all display pages

> Weekend push: whatever is not started gets done Sat–Sun; nothing slips past Sunday.

| # | View Page | Task — in plain words | Owner | Done when |
| :--- | :--- | :--- | :--- | :--- |
| 1 | ALL | 30-min conventions meeting: palette, `hb` prefix, this page list, branch names | ALL | Decisions in chat; palette in colors.xml |
| 2 | Jobs page | Build the card template (activity_job_item.xml) + Jobs page from jobs-feed-archetype: all jobs as cards (title, company, location, salary) via snapshot listener loop. Temporary `currentUserId` constant until Auth is taught | Rick Andrie Santos | All jobs scroll; live updates from console edits |
| 3 | Profile page | Show own name, email, resume link from tbl_users (snapshot listener); Edit button opens Edit Profile; Log Out button present (wiring waits for Auth) | Jade Anne Denise Ciar | Profile renders the sample user; layout matches profile-screen (trimmed) |
| 4 | Edit Profile page | Fields: Full Name, Resume URL, Skills (comma-separated → array). Save writes back to tbl_users (LA2 pattern) | Jade Anne Denise Ciar | Edits persist and re-render |
| 5 | Register + Login pages | Build BOTH layouts exactly per wireframes with input validation (.error style). Leave clearly marked TODO stubs where the Auth calls will go | Edrianne John Puno | Forms validate; TODO markers list the exact Auth lines to add |
| 6 | (database) | **Data cleanup:** dedupe jobs to 10–15 varied ones (rewrite descriptions, requirements 3–5 items); add skills arrays to users; repoint all 30 applications to real doc ids across Pending/Under Review/Accepted/Rejected; standardize status spelling | Edrianne John Puno with Jade Anne Denise Ciar | Re-running export_firestore.js shows clean links + varied jobs |
| 7 | Job Details page | Layout only this week (display fields from a job id passed by intent) — full wiring lands Week 3 | Jade Anne Denise Ciar | Opens with sample data shown |
| 8 | ALL | Merge + test: open Jobs → tap a job → Details; view Profile; edit Profile | ALL | Runs on emulator AND one phone |
| 9 | — | Progress Report 2 (assembler Edrianne John Puno; rows + screenshots by Sat 9 PM) | ALL | Submitted |
---

# 7. Week 3 (Nov 23–29) — Search, filter, Apply

| # | View Page | Task — in plain words | Owner | Done when |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Jobs / Applications / Profile | Bottom navigation bar on the three main pages, intent switching | Edrianne John Puno | Every tab opens from every other |
| 2 | Jobs page | Search: typing filters cards by title OR company (contains-check in the loop); clearing restores all | Rick Andrie Santos | Live filtering works |
| 3 | Jobs page | Location filter dialog (filter.html, location only): pick a location → only those jobs; combines with search | Rick Andrie Santos | Makati shows only Makati jobs |
| 4 | Job Details page | Full wiring: card tap → putExtra job doc id → snapshot listener on that document; show description, requirements (one per line), salary, location | Jade Anne Denise Ciar | Every card opens its correct details |
| 5 | Job Details page | APPLY: check own applications first (duplicate block), then add (user id, job doc id, status "Pending", serverTimestamp, + copied job_title/company_name); Toast; button becomes APPLIED state like the wireframe | Jade Anne Denise Ciar | First apply succeeds; second is blocked |
| 6 | Register + Login | If the Auth lesson happened by now: replace the TODO stubs with real register/login/reset calls (A). If not yet: they land Week 4 — everything else continues | Edrianne John Puno | Auth cycle works (or documented deferral) |
| 7 | (database) | Security rules draft (jobs readable when logged in; owner-only writes) | Edrianne John Puno | TEST ruleset live |
| 8 | ALL | Merge + full-flow test; Progress Report 3 (assembler Rick Andrie Santos) | ALL | Submitted |
---

# 8. Week 4 (Nov 30–Dec 6) — Tracking + states + security

| # | View Page | Task — in plain words | Owner | Done when |
| :--- | :--- | :--- | :--- | :--- |
| 1 | My Applications page | Cards: job title + company (copied fields), status chip (the four real values), applied date; friendly empty state | Jade Anne Denise Ciar | Only YOUR applications; empty state for new accounts |
| 2 | Jobs + all | Spinner on first load; "No jobs found" on empty results; Toast in listener exception branch (airplane-mode test) | Rick Andrie Santos | Error paths never freeze a page |
| 3 | Register + Login | Auth fully working regardless of lesson timing (register → login → reset); session redirect in MainActivity | Edrianne John Puno | Full cycle on emulator + phone |
| 4 | (database) | Final security rules replace test mode; logged-out access blocked | Edrianne John Puno | Rules enforced; app works for real users |
| 5 | ALL | App identity: launcher icon, HanapbuHire name, palette everywhere | Edrianne John Puno + Rick Andrie Santos | Correct on the phone launcher |
| 6 | ALL | Device testing; bug list with owners | ALL | Critical bugs fixed |
| 7 | — | Progress Report 4 (assembler Jade Anne Denise Ciar) | ALL | Submitted |
---

# 9. Week 5 (Dec 7–13) — Job Fit Indicator + polish

| # | View Page | Task — in plain words | Owner | Done when |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Job Details page | UNIQUE FEATURE — Job Fit Indicator: read own skills array + job requirements array, loop-count matches, show `Skill fit: X of Y required skills` + matched skills + one non-binding sentence. Class style: loops + setText, `//job fit indicator` | Edrianne John Puno + Jade Anne Denise Ciar | Correct counts for 3 test accounts |
| 2 | ALL | Performance + consistency pass: no duplicate listeners, uniform look per the design system | Rick Andrie Santos | Uniform pages; no wasted reads |
| 3 | ALL | Accessibility + reliability sweep: contentDescription, contrast, touch targets, offline behavior | ALL | Checklist ticked per page |
| 4 | — | Early finals: keystore (A), PDF outline (B), video script (C) | A / B / C | Drafts in Drive |
| 5 | — | Progress Report 5 (assembler Edrianne John Puno) | ALL | Submitted |
---

# 10. Week 6 (Dec 14–20) — Ship

| # | View Page | Task — in plain words | Owner | Done when |
| :--- | :--- | :--- | :--- | :--- |
| 1 | — | Signed APK (keystore from W5), versionName 1.0, install test | Edrianne John Puno | APK in Drive installs |
| 2 | — | Final PDF: intro/background, collection screenshots, per-file code screenshots (reference doc §4 + finals pages), app screenshots, contributions table = section 1 of this file | Rick Andrie Santos (assembly; all supply) | PDF complete per brief |
| 3 | ALL | Video: script follows page order; each member demos AND explains their OWN pages | Jade Anne Denise Ciar (lead; all 3 appear) | 5–8 min covering every feature |
| 4 | ALL | Final test + fixes; code freeze | ALL | No known crashes |
| 5 | — | Drive final check + submit before **Dec 20, 11:59 PM** | ALL | Confirmation screenshot in chat |
---

# 11. Rubric → owner mapping

| Criterion | Primary owner | How it is earned |
| :--- | :--- | :--- |
| Design | Rick Andrie Santos (+ all) | Wireframes as blueprint, shared design system, uniform pages |
| Functional suitability | Jade Anne Denise Ciar | Clear navigation, Apply flow, contextual feedback |
| Performance efficiency | Rick Andrie Santos | Lean listeners, fast pages, W5 pass |
| Usability | Jade Anne Denise Ciar + Rick Andrie Santos | Validation, empty states, recovery paths |
| Reliability | Edrianne John Puno | Security rules, error handling, duplicate block, data integrity |

*End of plan v3. Update whenever ownership changes so the next session (or AI agent) sees the truth.*