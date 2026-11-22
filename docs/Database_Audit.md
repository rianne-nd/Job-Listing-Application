# HanapbuHire — Database Audit (fresh export 2026-11-22)

> Source: `firebase-tools/firestore_dump.json` via `node export_firestore.js` (free Spark-plan Admin SDK script). Re-run anytime to re-verify.
> Bottom line: **table schemas are correct — every field name matches the exam brief, nothing is misspelled, no required field is missing, no empty cells.** The damage is in the ROWS: orphaned references, duplicated jobs, and two cosmetic user issues.

---

# tbl_job_listings — 30 documents

| Check | Result |
| :--- | :--- |
| Field names vs brief (job_id, company_name, job_title, description, requirements, location, salary) | **All 7 present, exact names, 0 extra, 0 missing** |
| Empty cells | **0 of 210** |
| job_id = document id | 30/30 ✔ (correct pattern) |
| Unique jobs | **only 5** — Software Engineer@Accenture, IT Support Specialist@Jollibee Group, UI/UX Designer@PLDT, Data Analyst@Globe Telecom, DevOps Engineer@SM Prime — each copied **6×** |
| Variety | 1 distinct description ("Handles daily tasks and software development operations.") across all 30; every requirements array is the same 2 items: ["Bachelor Degree", "1-2 years experience"] |
| Salary / location | 5 salary ranges, 5 locations (Makati City, Ortigas Pasig, Quezon City, BGC Taguig, Cebu City) — 6 jobs each |

**Needs fixing (rows, not schema) — owner Edrianne:**

1. Delete the 25 duplicate copies; keep/build 10–15 distinct jobs.
2. Write real, varied descriptions (one per job).
3. Vary requirements arrays to 3–5 items (e.g., Bachelor Degree, 1–2 yrs experience, portfolio, Kotlin/Android, communication skills) — this is what makes the Job Fit Indicator demo interesting.
---

# tbl_applications — 30 documents

| Check | Result |
| :--- | :--- |
| Field names vs brief (application_id, user_id, job_id, application_status, applied_on) | **All 5 present, exact names, 0 extra, 0 missing** |
| Empty cells | **0 of 150** |
| application_id = document id | 30/30 ✔ |
| job_id references | **0 of 30 match a real job.** They use job_001…job_010 (10 synthetic ids); real jobs use Firestore auto-ids (e.g., 0mmif7b6QWwVlGdW32MM) |
| user_id references | **0 of 30 match a real user.** They use user_001…user_030 (30 synthetic users); the real table has only user_1 |
| Statuses | Pending 7, Under Review 8, Accepted 8, Rejected 7 — good spread, keep these four values |
| applied_on | Proper Firestore timestamps, but only 5 distinct values (all inserted in one batch, Oct 20) — cosmetic, fine |

**Needs fixing — owner Rick:** every application points at users and jobs that do not exist. Fix = repoint all 30 records to real document ids (after the other two tables are repaired). The status spread and field values can stay.

---

# tbl_users — 1 document

| Check | Result |
| :--- | :--- |
| Field names vs brief (user_id, name, email, resume_url) | **All 4 present, exact names** |
| Empty cells | 0 |
| user_id = document id | **0/1 — mismatch:** field user_id = "user_1" but the doc id is gD0VVrDBldpmM8dZ3Dct (jobs and applications both use field-id = doc-id) |
| skills field | **missing** — needed for the Job Fit Indicator (group's own planned extension, not in the brief's minimal schema) |
| resume_url | Points to a Wikipedia *image* of Remus Lupin (with a trailing space and tracking junk) — not a resume document |

**Needs fixing — owner Jade:**

1. Add a skills array to the existing doc (e.g., ["Kotlin", "Firebase", "UI/UX"]).
2. Replace resume_url with a realistic document link (e.g., a public Google Drive PDF) and trim the trailing space.
3. Pre-create 2–4 demo users (with skills) so the 30 applications have real targets BEFORE Auth is taught. Convention from now on: **document id = user_id = Auth UID** (the current mismatch disappears once real accounts exist — each member registers once in Week 3/4).
---

# Direct answers

- **Are field names wrong anywhere?** No — all three tables use the brief's exact field names.
- **Are rows missing?** No required rows are missing — but tbl_users (1 row) and tbl_job_listings (5 unique) are far too small for what tbl_applications assumes (30 users, 10 jobs). The fix is adding/repointing, not restoring lost data.
- **What breaks in the app if unfixed?** My Applications and Job Details would show blanks/lookup failures for every seeded application; the Job Fit Indicator cannot work without skills.
- **Recommended repair order:** 1) fix tbl_job_listings (dedupe + enrich) → 2) fix tbl_users (skills, resume_url, demo users) → 3) repoint tbl_applications to real ids → 4) re-run `node export_firestore.js` and confirm 0 orphans.

*End of audit. Regenerate with `cd firebase-tools && node export_firestore.js` then compare with this file.*