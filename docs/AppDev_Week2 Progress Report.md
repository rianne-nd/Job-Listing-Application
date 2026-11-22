# **Weekly Project Progress Report**

Applications Development | HanapbuHire: Job Listings Application (Android Studio + Firebase)

Group Name: Group 10  |  Week No.: 2  |  Date: 11/22/2026

# **Project Overview**

Our group is developing **HanapbuHire**, an Android job listings application that connects job seekers with employers, featuring job postings, user profiles with resumes, and application tracking, built with Kotlin in Android Studio and Cloud Firestore as the backend. This week's focus was the **project structure, data integrity, and version control**: all seven screens were scaffolded in our class coding style, all three Firestore collections were audited and repaired so every record links correctly, and the whole project was pushed to GitHub together with its planning documents.

# **1. Summary / List of Completed Tasks and Current Status**

| Task | Status | Assigned Members |
| :---- | ----- | ----- |
| Created all 7 screen skeletons (Register, Login, Jobs, Job Details, My Applications, Profile, Edit Profile) as Activities + layouts, registered in AndroidManifest | Done Nov 22, 2026 | Edrianne John Puno |
| Audited all 3 Firestore collections against the exam brief schema (field names correct; found orphaned references in tbl_applications) | Done Nov 22, 2026 | Edrianne John Puno |
| Repaired tbl_job_listings: removed duplicates (30 → 12 distinct jobs with unique descriptions and 3–5 item requirements) | Done Nov 22, 2026 | Edrianne John Puno |
| Rebuilt tbl_users: 4 demo accounts with skills arrays; document id = user_id convention | Done Nov 22, 2026 | Jade Anne Denise Ciar & Edrianne John Puno |
| Repointed all 30 tbl_applications records to real user/job ids (0 orphans; unique user–job pairs; statuses preserved) | Done Nov 22, 2026 | Rick Andrie Santos & Edrianne John Puno |
| Designed 10 HTML wireframes covering every screen; reviewed against the exam brief and trimmed out-of-scope elements (employment-type filter, phone/bio fields) | Done Nov 20–22, 2026 | Rick Andrie Santos (design) & Edrianne John Puno (review) |
| Set up the shared GitHub workflow; pushed all screens + documentation; hardened .gitignore | Done Nov 22, 2026 | Edrianne John Puno |
| Compiled the Kotlin coding-style reference, weekly plan, and database audit into the repository docs/ folder | Done Nov 22, 2026 | Edrianne John Puno |
| Backed up the pre-repair database (firestore_dump_backup_20261122.json) | Done Nov 22, 2026 | Edrianne John Puno |
| Prepared this Week 2 progress report | Done Nov 22, 2026 | Edrianne John Puno (assembler) |

## **Collections (state after this week's repair)**

| Collection | Fields and current state |
| :---- | :---- |
| tbl_users | 4 records — user_id, name, email, resume_url, skills (array); document id = user_id |
| tbl_job_listings | 12 records — job_id, company_name, job_title, description, requirements (array), location, salary; job_id = document id |
| tbl_applications | 30 records — application_id, user_id, job_id, application_status, applied_on; 100% of references valid |

**Current status:** The project foundation is complete. All seven screens exist as class-style skeletons, the database is fully linked with zero orphaned records, and the codebase lives on GitHub with its documentation. The wireframes are approved as the UI blueprint, so screen building starts next week; the Authentication screens are stubbed and waiting for the Auth lesson before their logic is wired.

# **2. Remaining / Added Future Tasks and Plans for Completion**

| Task | Owner | Target |
| :---- | :---- | :---- |
| Jobs page UI per wireframes (card template + list from tbl_job_listings) | Rick Andrie Santos | Nov 23–24, 2026 |
| Profile + Edit Profile UI per wireframes | Jade Anne Denise Ciar | Nov 23–24, 2026 |
| Register + Login UI per wireframes with input validation | Edrianne John Puno | Nov 23–24, 2026 |
| Bottom navigation shell (Jobs / Applications / Profile) | Edrianne John Puno | Nov 24–25, 2026 |
| Job Details wiring (job_id → document load) + Apply button + duplicate-application block | Jade Anne Denise Ciar | Nov 25–27, 2026 |
| Search by title/company + location filter (location only, per brief) | Rick Andrie Santos | Nov 25–27, 2026 |
| Firebase Authentication wiring (register, login, password reset) — as taught | Edrianne John Puno | When the lesson lands |
| Firestore security rules draft | Edrianne John Puno | Nov 28–29, 2026 |
| My Applications page + empty state | Jade Anne Denise Ciar | Nov 30 – Dec 1, 2026 |
| Loading / empty / error states on all screens | Rick Andrie Santos | Nov 30 – Dec 1, 2026 |
| Merge branches, test on emulator + device, prepare Progress Report 3 | All | Nov 29, 2026 (code freeze) |

# **3. Screenshots of Outputs and Code**

## **3.1 GitHub Repository**

| [ Paste screenshot here ] |
| :---: |

*Figure: GitHub repository with this week's commits (0debcc8, d416eb1) and the docs/ folder*

## **3.2 Database Repair — Before and After**

| [ Paste screenshot here ] |
| :---: |

*Figure: Audit evidence — before: 30 of 30 applications orphaned; after: 0 orphans (Database_Audit.md)*

## **3.3 Firestore Collections (repaired)**

| [ Paste screenshot here ] |
| :---: |

*Figure: tbl_job_listings with 12 distinct jobs*

| [ Paste screenshot here ] |
| :---: |

*Figure: tbl_users with skills arrays on all 4 demo users*

| [ Paste screenshot here ] |
| :---: |

*Figure: tbl_applications with valid user_id and job_id references*

## **3.4 Android Studio Project Structure**

| [ Paste screenshot here ] |
| :---: |

*Figure: Package with all 8 activities and the 7 new layout files*

## **3.5 Code — Screen Skeleton (class style) and Manifest Registration**

```kotlin
class RegisterActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_register)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        //register page - TODO: create the account with Firebase Authentication once it is taught, then save the user to tbl_users using the auth uid
    }
}
```

| [ Paste screenshot here ] |
| :---: |

*Figure: Android Studio — the seven activities in the project tree*

## **3.6 User Interface (Emulator)**

| [ Placeholder for Week 3 — screens are skeletons this week ] |
| :---: |

*Figure: First wired screens (Jobs page, Profile page) — target Nov 23–24*

# **4. Group Members and Their Finished Tasks**

| Member | Role | Finished Tasks This Week |
| :---- | :---- | :---- |
| Edrianne John Puno | Firebase & Backend Lead | Set up the Firebase project and GitHub repository; audited and repaired all 3 collections (30 → 12 unique jobs, skills added to users, 30 applications repointed to 0 orphans); created all 7 screen skeletons in class style; registered them in the manifest; pushed everything to GitHub with docs |
| Rick Andrie Santos | UI Lead (Jobs) & Design | Designed all 10 wireframes for the app's screens (approved as the UI blueprint); seeded the tbl_applications sample data now repointed to real ids |
| Jade Anne Denise Ciar | UI Lead (Applications & Profile) | Created the tbl_users collection and first sample user; rebuilt the demo users with skills arrays for the Job Fit Indicator |

**Attendance:** All members present in lecture sessions this week. [edit if needed]

---

*Note for the group: replace every [ Paste screenshot here ] with your actual captures before exporting to PDF for submission.*