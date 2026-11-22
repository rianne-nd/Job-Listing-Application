# 1CS6011 AppDev — Kotlin Code & Coding-Style Reference

> Compiled 2026-11-20 from the group's Android Studio projects in `C:/Users/Kath Puno/AndroidStudioProjects`.
> Purpose: the single reference any AI agent (or member) must read before writing code for the
> **Final Examination — HanapbuHire Job Listings Application**, so the code stays recognizable as
> class work: simple, in the style actually taught, with nothing fancy added silently.

---

# 1. Final Exam Context

- Course: 1CS6011 Applications Development. Instructor: Inst. Rheaxena Gorres, MIT.
- Assigned application type: **Job Listings Application** (examples: LinkedIn, Indeed, Glassdoor).
- Stack: Kotlin + Android Studio + Cloud Firestore. Availability: Oct 14 – Dec 20, 2026.
- Deliverables (shared Google Drive): 1) PDF report (introduction/background, screenshots of collections and code per Kotlin file, screenshots of app outputs, list of members and contributions), 2) compiled APK, 3) video demo covering all features — every member participates.
- Evaluation criteria: Design, Functional Suitability, Performance Efficiency, Usability, Reliability (rubric: Excellent 15 / Very Good 12 / Good 8 / Fair 5).
- Important reminders from the brief: keep the code recognizable — apply the structures practiced in class (advanced techniques allowed, class structures must stay); no templates or ready-made apps; if similar systems exist in class, add one unique feature that stands above the rest; be creative with app name, theme color, and processes.
- Weekly reports: Week 1 Nov 9 (done) → Week 2 Nov 16 → Week 3 Nov 23 → Week 4 Nov 30 → Week 5 Dec 7 → Week 6 Dec 14 final submission & presentation.
- **Group 10 — app name: HanapbuHire.** Members: Edrianne John Puno, Rick Andrie Santos, Jade Anne Denise Ciar.
- Week 1 already done (Nov 9): Android Studio project created, Firebase project + Android app registered, all 3 members added to Firebase, Firestore enabled in test mode, collections created, SDK dependencies connected.
- Proposed unique feature (Week 1 plan): **Job Fit Indicator** — compares the user's saved skills with a job's requirements and shows an explanatory, non-binding fit summary.

### Collections schema fixed by the exam brief

| Collection | Fields |
| :--- | :--- |
| tbl_users | user_id (string), name (string), email (string), resume_url (string) |
| tbl_job_listings | job_id (string), company_name (string), job_title (string), description (string), requirements (array), location (string), salary (string) |
| tbl_applications | application_id (string), user_id (string), job_id (string), application_status (string, e.g. pending/accepted), applied_on (timestamp) |

Full brief: `Downloads/AppDev/Finals Exam/1CS6011 Final Examination Docs Brief.md` (same file was attached to the chat). Weekly report: `Downloads/AppDev/Finals Exam/AppDev_Week1 Progress Report.md`.

---

# 2. Project Inventory (what each Android Studio project is)

| Project | Package | What it demonstrates | Role for the finals |
| :--- | :--- | :--- | :--- |
| EJP_HandsOn | com.example.ejp_handson | Firebase create (`add`) + real-time display (`addSnapshotListener`) with an inflated item template — the Finals Lab 2/3 pattern | **Primary style reference** (newest, cleanest class work) |
| PUNO_HandsOn | com.example.puno_handson | Same hands-on by Kath (Puno), create-only variant | Confirms the taught Firebase pattern |
| MyApplication | com.example.myapplication | Midterms-era screens: layouts, intents with extras, validation, all input widgets (EditText, CheckBox, Switch, RadioGroup), calculator, age verification, lifecycle logging | Style for plain UI screens (pre-Firebase) |
| JobListingApplication | com.example.joblistingapplication | Empty starter: default Hello World `MainActivity` + Firebase dependencies already in gradle | **The finals project lives here** |

Class submissions at `C:/Users/Kath Puno/Downloads/AppDev` document the same work (Finals Exam folder, Hands On 2, LabAct1/2 Firebase labs, Midterms labs). The Firebase lab PDFs are image-only scans, so the code patterns above were taken from the projects themselves.

---

# 3. Coding Style Rules (the class style — follow these)

## 3.1 The exact Activity skeleton (copy for every new screen)

```kotlin
package com.example.joblistingapplication

import android.os.Bundle
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_main)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }
    }
}
```

Rules:

- One Activity class per screen, extends `AppCompatActivity`, package `com.example.<appname>`.
- `onCreate` order is fixed: `super.onCreate` → `enableEdgeToEdge()` → `setContentView(R.layout.activity_x)` → the `ViewCompat.setOnApplyWindowInsetsListener` boilerplate (root id, usually `R.id.main`) → `findViewById` block → listener wiring.
- Everything is a local `val` inside `onCreate`. No class-level fields, no helper methods, no secondary constructors.
- Other lifecycle overrides are only written when the topic is the lifecycle itself (see `ejpHandsOn.kt`: one `Log.d("Phase", "...")` per phase).
- After the inset block, declare ALL views first, then wire listeners below them.

## 3.2 Naming conventions (observed)

- View ids: widget prefix + PascalCase, with an owner/app suffix. Two observed orders — keep ONE per file, consistently:
  - Prefix style (EJP_HandsOn): `ejpEdtPost`, `ejpBtnPost`, `ejpTvTitle`, `ejpTxtUsername`, `ejpTxtLikes`, `ejpVlayout`, `ejpCardPost`, `ejpScrollView`, `ejpImgUser`, `ejpBtnCreatePost`.
  - Suffix style (MyApplication): `et_nameEjp`, `btn_signupEjp`, `tv_resultEjp`, `et_ageEjp`, `btn_verifyEjp`, `cl_profileEjp`, `iv_backEjp`, `cb_giftWrapEjp`, `sw_contactlessEjp`, `rg_deliveryEjp`, `btn_confirmEjp`.
  - Widget prefixes: `et_` (EditText/TextInputEditText), `tv_`/`Txt` (TextView), `btn_` (Button), `cb_` (CheckBox), `sw_` (Switch), `rg_` (RadioGroup), `iv_`/`Img` (ImageView), `cl_` (ConstraintLayout), `Vlayout` (vertical LinearLayout container), `Card` (MaterialCardView), `ScrollView`.
- Kotlin locals: camelCase, usually mirroring the id (`val ejpBtnPost = findViewById<Button>(R.id.ejpBtnPost)`) or a plain domain word (`val post`, `val name`, `val email`, `val num1`, `val sum`).
- Layout files: `activity_<screen_name>.xml` lowercase with underscores; the class name matches the screen (`SignUpActivity` → `activity_sign_up.xml`). Typos exist in the real projects (`activity_age_vereification.xml`, `activity_calcultator.xml`) — do not copy the typos, do copy the pattern.
- Firestore (class hands-on style): collection `tbl_<plural><suffix>` (`tbl_postsEJP`, `tbl_postsEjt`); fields lowercaseNoSpaces + suffix (`useridEJP`, `contentEJP`, `nooflikesEJP`, `nocommentsEJP`, `createdateEJP`). For the FINALS use the exam-brief schema names instead (`tbl_users`, `tbl_job_listings`, `tbl_applications`, snake_case fields).
- Package: `com.example.<projectname>`, all lowercase, no spaces.

## 3.3 Comment style

Lowercase, informal, one short step label per line, above the code, sometimes followed by a blank line:

```text
//start firebase connection
//user input
//field to value
//initialize layout inflater
//variable for each textview
//display
//createdat (data type: timestamp)
//sample data for the template preview
```

Typos happen (`//startfireebaseconnection`) — write clean but keep them lowercase and stepwise. Do NOT convert to doc-comments or full sentences.

## 3.4 Firebase patterns (as taught — Finals Labs: Connection & Create, Display/Get)

**CREATE** — get an instance once, read inputs, `.add(mapOf(...))`, timestamp via `FieldValue.serverTimestamp()`, Toast + navigation inside `addOnSuccessListener`:

```kotlin
//start firebase connection
val conn = FirebaseFirestore.getInstance()

ejpBtnPost.setOnClickListener {
    //user input
    val post = ejpEdtPost.text.toString()

    conn.collection("tbl_postsEJP").add(
        mapOf(
            //field to value
            "useridEJP" to "userid1",
            "contentEJP" to post,
            "nooflikesEJP" to 0,
            "createdateEJP" to FieldValue.serverTimestamp()
        )
    ).addOnSuccessListener {
        Toast.makeText(this, "Post Created", Toast.LENGTH_SHORT).show()

        val intent = Intent(this, Display::class.java)
        startActivity(intent)
    }
}
```

**DISPLAY (real-time)** — `addSnapshotListener`, null-check, `removeAllViews()`, loop documents, inflate a template layout, fill its views, add it to the container. NOTE: the class pattern is a ScrollView + vertical LinearLayout + inflated template — NOT a RecyclerView:

```kotlin
//start firebase connection
val conn = FirebaseFirestore.getInstance()

conn.collection("tbl_postsEJP")
    .addSnapshotListener { documents, exception ->
        if (documents != null) {
            ejpVlayout.removeAllViews()

            for (document in documents) {
                //initialize layout inflater
                val inflater = LayoutInflater.from(this)

                //call the template inflate("from what xml", "to what view", false)
                val template = inflater.inflate(R.layout.activity_post_template, ejpVlayout, false)

                //variable for each textview
                val ejpTxtContent = template.findViewById<TextView>(R.id.ejpTxtContent)
                // ... one findViewById per template view (full file: section 4 Display.kt)

                //display
                ejpTxtContent.text = document.getString("contentEJP")
                ejpTxtComments.text = document.getLong("nocommentsEJP").toString()

                //createdat (data type: timestamp)
                val datetimestamp = document.getTimestamp("createdateEJP")
                val converteddate = datetimestamp?.toDate()

                if (converteddate != null) {
                    ejpTxtDate.text = DateFormat.format("MMM dd, yyyy \n hh:mm:ss", converteddate)
                }

                //display vertical layout to vertical layout
                ejpVlayout.addView(template)
            }
        }
    }
```

Firestore accessors used in class: `document.getString("field")`, `document.getLong("field").toString()`, `document.getTimestamp("field")?.toDate()`. Numbers stored as Int in the map come back with `getLong`. The elided middle is in the verbatim file below — always copy from section 4, never retype.

## 3.5 User input, validation, widgets

- Read text: `val x = etName.text.toString()`.
- Numbers: `toIntOrNull()` / `toFloatOrNull() ?: 0.0f` (elvis default 0).
- Validation: if/else-if chain assigning `et.error = "Input is Required"` per field, final `else` does the work. See `AgeVerificationActivity` / `CalculatorActivity` for the exact shape.
- Branching on values: `when (age) { in 0 .. 3 -> { ... }  20 -> { ... } else -> { ... } }`.
- CheckBox/Switch: `if (cb.isChecked) { ... } else { ... }` into summary TextViews.
- RadioGroup: `val rbID = rg.checkedRadioButtonId`; `-1` means none selected; then `findViewById<RadioButton>(rbID)`.
- Feedback: set a TextView's `.text`, and `Toast.makeText(this, "msg", Toast.LENGTH_SHORT).show()` for actions.
## 3.6 Navigation & passing data

```kotlin
val intent = Intent(this, ProfileActivity::class.java)
intent.putExtra("name", name)
intent.putExtra("email", email)
startActivity(intent)

// receiving side
val name = intent.getStringExtra("name")

// going back
finish()
```
## 3.7 Dates

- 'Now' as text: `SimpleDateFormat("MMM dd,\nyyyy", Locale.US)` + `Date()` (java.text / java.util).
- Firestore timestamp: `document.getTimestamp("f")?.toDate()` then `DateFormat.format("MMM dd, yyyy \n hh:mm:ss", converteddate)` (android.text.format.DateFormat).
## 3.8 Formatting quirks (keep them — do not 'clean up' the code)

- 4-space indent, opening brace on the same line.
- Occasional tight spacing in the real code: `else{`, `}else if(`, `else ->  {`; double blank lines before the closing brace of onCreate. Harmless human style — a little of it is authentic; keep the code readable first.
- Simple interpolation only where natural: `"Delivery option: ${rbChosenDelivery.text}"`. Numbers are rendered with `.toString()`.
- No magic: no extension functions, no scoping functions (`apply/also/let` chains), no lambdas beyond click/snapshot listeners, no `when` on strings, no sealed classes, no generics tricks.
## 3.9 What has NOT been used in class (never add silently)

ViewBinding, RecyclerView + Adapter, data classes / DTOs, ViewModel / LiveData, coroutines, Flow, Hilt/DI, Navigation Component, Room, Retrofit, string resources for UI text (text is hardcoded in the XML), themes with heavy customization, tests beyond the generated boilerplate.
If an advanced technique is truly needed (the brief allows it), keep the class structures visible — Activity + onCreate + findViewById + FirebaseFirestore `mapOf`/`add` — and comment the advanced part clearly so the code stays recognizable.
## 3.10 Layout XML style

- Root: `androidx.constraintlayout.widget.ConstraintLayout`, `android:id="@+id/main"`, `tools:context=".ClassName"`.
- Colors: hardcoded hex for text/dividers (`#333333`, `#666666`, `#222222`, `#E0E0E0`, `#F5F5F5`); theme palette in `colors.xml` (`blue_primary #1976D2`, `blue_accent #2196F3`, `blue_background #E3F2FD`). For HanapbuHire, define your own palette the same way (e.g. `green_primary`, `green_accent`, `green_background`) — brief asks for a creative theme color.
- Cards: `com.google.android.material.card.MaterialCardView` with `cardCornerRadius 8dp`, `cardElevation 1–8dp`, `cardUseCompatPadding`, white background, inner vertical `LinearLayout` with `padding 16dp`.
- Buttons: `com.google.android.material.button.MaterialButton`, height `50dp`, `cornerRadius 25dp`, bold `14sp`, ALL-CAPS label, `backgroundTint` from the palette.
- Type scale: screen title 24sp bold; content 15–18sp; secondary/meta 12–13sp in `#666666`.
- Icons: Material Symbols drawables with generated names (`account_circle_24dp_666666_fill0_wght400_grad0_opsz24`) plus simple `ic_*` drawables (`ic_favorite_border`, `ic_comment`); always set `contentDescription`; `scaleType="fitCenter"`.
- Lists: `ScrollView` (fillViewport) → vertical `LinearLayout` (padding 16dp) → clickable header row → item container `LinearLayout` (`ejpVlayout`) filled by the snapshot listener.
- Clickable rows: LinearLayout with `clickable=true`, `focusable=true`, `foreground="?attr/selectableItemBackground"`.
- Theme: `Theme.Material3.DayNight.NoActionBar`, with the generated `Base.Theme.<App>` / `Theme.<App>` pair in `themes.xml`.
## 3.11 Gradle / project setup style

- `minSdk 24`, `targetSdk 37`, `compileSdk { version = release(37) }`, `versionCode 1`, `versionName "1.0"`, `sourceCompatibility/targetCompatibility = JavaVersion.VERSION_11`.
- Plugins: `alias(libs.plugins.android.application)` + `id("com.google.gms.google-services")` (version `4.5.0 apply false` at project level).
- Dependencies: version-catalog aliases for androidx/material/junit, then a Firebase block: `implementation(platform("com.google.firebase:firebase-bom:34.19.0"))` + `implementation("com.google.firebase:firebase-firestore")` (the JobListingApplication starter also has `firebase-analytics` and `firebase-database`, bom 33.9.0). Verbatim files in section 6.
## 3.12 Manifest pattern

Every screen is registered as `<activity android:name=".X" android:exported="false" />`; only the launcher is `exported="true"` with the MAIN/LAUNCHER intent filter. Verbatim in section 6.
---

# 4. Complete Kotlin Source (verbatim, all 23 .kt files)

Order: main sources first, then the generated test boilerplate. Copy code for the report screenshots from here.

## 4.1 EJP_HandsOn — app/src/main/java/com/example/ejp_handson/

### Display.kt

```kotlin
package com.example.ejp_handson

import android.content.Intent
import android.os.Bundle
import android.view.LayoutInflater
import android.widget.LinearLayout
import android.widget.TextView
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat
import com.google.firebase.firestore.FirebaseFirestore
import android.text.format.DateFormat

class Display : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_display)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        val ejpVlayout = findViewById<LinearLayout>(R.id.ejpVlayout)
        val ejpBtnCreatePost = findViewById<LinearLayout>(R.id.ejpBtnCreatePost)

        //start firebase connection
        val conn = FirebaseFirestore.getInstance()

        ejpBtnCreatePost.setOnClickListener {
            val intent = Intent(this, MainActivity::class.java)
            startActivity(intent)
        }

        conn.collection("tbl_postsEJP")
            .addSnapshotListener { documents, exception ->
                if (documents != null) {
                    ejpVlayout.removeAllViews()

                    for (document in documents) {
                        //initialize layout inflater
                        val inflater = LayoutInflater.from(this)

                        //call the template inflate("from what xml", "to what view", false)
                        val template = inflater.inflate(R.layout.activity_post_template, ejpVlayout, false)

                        //variable for each textview
                        val ejpTxtContent = template.findViewById<TextView>(R.id.ejpTxtContent)
                        val ejpTxtUsername = template.findViewById<TextView>(R.id.ejpTxtUsername)
                        val ejpTxtComments = template.findViewById<TextView>(R.id.ejpTxtComments)
                        val ejpTxtLikes = template.findViewById<TextView>(R.id.ejpTxtLikes)
                        val ejpTxtDate = template.findViewById<TextView>(R.id.ejpTxtDate)

                        //display
                        ejpTxtContent.text = document.getString("contentEJP")
                        ejpTxtUsername.text = document.getString("useridEJP")
                        ejpTxtComments.text = document.getLong("nocommentsEJP").toString()
                        ejpTxtLikes.text = document.getLong("nooflikesEJP").toString()

                        //createdat (data type: timestamp)
                        val datetimestamp = document.getTimestamp("createdateEJP")
                        val converteddate = datetimestamp?.toDate()

                        if (converteddate != null) {
                            ejpTxtDate.text = DateFormat.format("MMM dd, yyyy \n hh:mm:ss", converteddate)
                        }

                        //display vertical layout to vertical layout
                        ejpVlayout.addView(template)
                    }
                }
            }
    }
}
```

### MainActivity.kt

```kotlin
package com.example.ejp_handson

import android.content.Intent
import android.os.Bundle
import android.widget.Button
import android.widget.EditText
import android.widget.Toast
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat
import com.google.firebase.firestore.FieldValue
import com.google.firebase.firestore.FirebaseFirestore

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_main)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        val ejpEdtPost = findViewById<EditText>(R.id.ejpEdtPost)
        val ejpBtnPost = findViewById<Button>(R.id.ejpBtnPost)

        //start firebase connection
        val conn = FirebaseFirestore.getInstance()

        ejpBtnPost.setOnClickListener {
            //user input
            val post = ejpEdtPost.text.toString()

            conn.collection("tbl_postsEJP").add(
                mapOf(
                    //field to value
                    "useridEJP" to "userid1",
                    "contentEJP" to post,
                    "nooflikesEJP" to 0,
                    "nocommentsEJP" to 0,
                    "createdateEJP" to FieldValue.serverTimestamp()
                )
            ).addOnSuccessListener {
                Toast.makeText(this, "Post Created", Toast.LENGTH_SHORT).show()

                val intent = Intent(this, Display::class.java)
                startActivity(intent)
            }
        }
    }
}
```

### PostTemplate.kt

```kotlin
package com.example.ejp_handson

import android.os.Bundle
import android.widget.TextView
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat
import java.text.SimpleDateFormat
import java.util.Date
import java.util.Locale

class PostTemplate : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_post_template)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        val ejpTxtUsername = findViewById<TextView>(R.id.ejpTxtUsername)
        val ejpTxtContent = findViewById<TextView>(R.id.ejpTxtContent)
        val ejpTxtDate = findViewById<TextView>(R.id.ejpTxtDate)
        val ejpTxtLikes = findViewById<TextView>(R.id.ejpTxtLikes)
        val ejpTxtComments = findViewById<TextView>(R.id.ejpTxtComments)
        val dateFormat = SimpleDateFormat("MMM dd,\nyyyy", Locale.US)

        //sample data for the template preview
        ejpTxtUsername.text = "userid1"
        ejpTxtContent.text = "TextView"
        ejpTxtDate.text = dateFormat.format(Date())
        ejpTxtLikes.text = "0"
        ejpTxtComments.text = "0"
    }
}
```

## 4.2 JobListingApplication — app/src/main/java/com/example/joblistingapplication/

### MainActivity.kt (current starter — will be replaced by the finals screens)

```kotlin
package com.example.joblistingapplication

import android.os.Bundle
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_main)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }
    }
}
```

## 4.3 MyApplication — app/src/main/java/com/example/myapplication/

### MainActivity.kt

```kotlin
package com.example.myapplication

import android.os.Bundle
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_main)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }
    }
}
```

### WelcomeActivity.kt

```kotlin
package com.example.myapplication

import android.os.Bundle
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat

class WelcomeActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_welcome)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }
    }
}
```

### SignUpActivity.kt

```kotlin
package com.example.myapplication

import android.content.Intent
import android.os.Bundle
import android.widget.Button
import android.widget.ImageView
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat
import com.google.android.material.textfield.TextInputEditText

class SignUpActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_sign_up)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.cl_signupEjp)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        val etName = findViewById<TextInputEditText>(R.id.et_nameEjp)
        val etEmail = findViewById<TextInputEditText>(R.id.et_emailEjp)
        val etPhone = findViewById<TextInputEditText>(R.id.et_phoneEjp)
        val btnSignUp = findViewById<Button>(R.id.btn_signupEjp)
        val btnBack = findViewById<ImageView>(R.id.iv_backEjp)

        btnBack.setOnClickListener {
            finish()
        }

        btnSignUp.setOnClickListener {
            val name = etName.text.toString()
            val email = etEmail.text.toString()
            val phone = etPhone.text.toString()

            val intent = Intent(this, ProfileActivity::class.java)
            intent.putExtra("name", name)
            intent.putExtra("email", email)
            intent.putExtra("phone", phone)
            startActivity(intent)
        }
    }
}
```

### ProfileActivity.kt

```kotlin
package com.example.myapplication

import android.os.Bundle
import android.widget.TextView
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat

class ProfileActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_profile)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.cl_profileEjp)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        val name = intent.getStringExtra("name")
        val email = intent.getStringExtra("email")
        val phone = intent.getStringExtra("phone")

        findViewById<TextView>(R.id.tv_profileNameEjp).text = name
        findViewById<TextView>(R.id.tv_nameValueEjp).text = name
        findViewById<TextView>(R.id.tv_emailValueEjp).text = email
        findViewById<TextView>(R.id.tv_phoneValueEjp).text = phone
    }
}
```

### OptionsActivity.kt

```kotlin
package com.example.myapplication

import android.os.Bundle
import android.widget.Button
import android.widget.CheckBox
import android.widget.ImageView
import android.widget.RadioButton
import android.widget.RadioGroup
import android.widget.Switch
import android.widget.TextView
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat

class OptionsActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_options)

        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        val cbGiftWrapEjp = findViewById<CheckBox>(R.id.cb_giftWrapEjp)
        val swContactlessEjp = findViewById<Switch>(R.id.sw_contactlessEjp)
        val rgDeliveryEjp = findViewById<RadioGroup>(R.id.rg_deliveryEjp)
        val tvGiftWrapSummaryEjp = findViewById<TextView>(R.id.tv_giftWrapSummaryEjp)
        val tvContactlessDeliverySummaryEjp = findViewById<TextView>(R.id.tv_contactlessDeliverySummaryEjp)
        val tvDeeliveryTypeSummaryEjp = findViewById<TextView>(R.id.tv_deeliveryTypeSummaryEjp)
        val btnConfirmEjp = findViewById<Button>(R.id.btn_confirmEjp)


        btnConfirmEjp.setOnClickListener {
            var giftWrap = ""
            if (cbGiftWrapEjp.isChecked) {
                giftWrap = "Add Gift Wrap: Yes"
            } else {
                giftWrap = "Add Gift Wrap: No"
            }
            tvGiftWrapSummaryEjp.text = giftWrap

            var contactless = ""
            if (swContactlessEjp.isChecked) {
                contactless = "Contactless Delivery: Yes"
            } else {
                contactless = "Contactless Delivery: No"
            }
            tvContactlessDeliverySummaryEjp.text = contactless

            val rbID = rgDeliveryEjp.checkedRadioButtonId
            var deliveryType = ""

            if (rbID == -1) {
                deliveryType = "Delivery option: None selected"
            } else {
                val rbChosenDelivery = findViewById<RadioButton>(rbID)
                deliveryType = "Delivery option: ${rbChosenDelivery.text}"
            }
            tvDeeliveryTypeSummaryEjp.text = deliveryType
        }
    }
}
```

### CalculatorActivity.kt

```kotlin
package com.example.myapplication

import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat
import com.google.android.material.textfield.TextInputEditText

class CalculatorActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_calcultator)

        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        val et_num1Ejp = findViewById<TextInputEditText>(R.id.et_num1Ejp)
        val et_num2Ejp = findViewById<TextInputEditText>(R.id.et_num2Ejp)

        val btn_addEjp = findViewById<Button>(R.id.btn_addEjp)
        val btn_subEjp = findViewById<Button>(R.id.btn_subEjp)
        val btn_mulEjp = findViewById<Button>(R.id.btn_mulEjp)
        val btn_divEjp = findViewById<Button>(R.id.btn_divEjp)

        val tv_resultEjp = findViewById<TextView>(R.id.tv_resultEjp)

        btn_addEjp.setOnClickListener {
            val num1 = et_num1Ejp.text.toString().toFloatOrNull() ?: 0.0f
            val num2 = et_num2Ejp.text.toString().toFloatOrNull() ?: 0.0f
            val sum = num1 + num2

            tv_resultEjp.text = sum.toString()
        }

        btn_subEjp.setOnClickListener {
            val num1 = et_num1Ejp.text.toString().toFloatOrNull() ?: 0.0f
            val num2 = et_num2Ejp.text.toString().toFloatOrNull() ?: 0.0f
            val difference = num1 - num2

            tv_resultEjp.text = difference.toString()
        }

        btn_mulEjp.setOnClickListener {
            val num1 = et_num1Ejp.text.toString().toFloatOrNull() ?: 0.0f
            val num2 = et_num2Ejp.text.toString().toFloatOrNull() ?: 0.0f
            val product = num1 * num2

            tv_resultEjp.text = product.toString()
        }

        btn_divEjp.setOnClickListener {
            val num1 = et_num1Ejp.text.toString().toFloatOrNull() ?: 0.0f
            val num2 = et_num2Ejp.text.toString().toFloatOrNull() ?: 0.0f

            if (num2 == 0.0f) {
                tv_resultEjp.text = "Cannot divide by zero"
            } else {
                val quotient = num1 / num2
                tv_resultEjp.text = quotient.toString()
            }
        }


    }
}
```

### AgeVerificationActivity.kt

```kotlin
package com.example.myapplication

import android.os.Bundle
import android.widget.Button
import android.widget.TextView
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat
import com.google.android.material.textfield.TextInputEditText

class AgeVerificationActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_age_vereification)

        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        val etAgeEjp = findViewById<TextInputEditText>(R.id.et_ageEjp)
        val tvAgeResultEjp = findViewById<TextView>(R.id.tv_ageResultEjp)
        val btnVerifyEjp = findViewById<Button>(R.id.btn_verifyEjp)

        btnVerifyEjp.setOnClickListener {
            val ageInput = etAgeEjp.text.toString()
            val age = ageInput.toIntOrNull()

            if (ageInput.isEmpty()){
                etAgeEjp.error = "Input is Required"
            } else if(ageInput.toIntOrNull() == null){
                etAgeEjp.error = "Please input a number"
            }else if(ageInput.length < 2){
                etAgeEjp.error = "Please enter atleast 2 digits"
            }else {
                val age = ageInput.toIntOrNull() ?: 0

                when(age){
                    in 0 .. 3 -> {
                        tvAgeResultEjp.text = "Infant"
                    }
                    in 13 .. 19 -> {
                        tvAgeResultEjp.text = "Teen"
                    }
                    20 -> {
                        tvAgeResultEjp.text = "Adult"
                    }

                    else ->  {
                        tvAgeResultEjp.text = "Invalid Age"
                    }
                }
            }
        }
    }
}
```

### CartActivity.kt

```kotlin
package com.example.myapplication

import android.os.Bundle
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat

class CartActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_cart)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.ejp_constraintLayout)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }
    }
}
```

### CartActivity1.kt

```kotlin
package com.example.myapplication

import android.os.Bundle
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat

class CartActivity1 : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_cart1)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }
    }
}
```

### ejpHandsOn.kt (lifecycle logging demo)

```kotlin
package com.example.myapplication

import android.os.Bundle
import android.util.Log
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat

class ejpHandsOn : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_ejp_hands_on)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }
        Log.d("Phase", "The application is onCreate phase - Initialize and set up")
    }

    override fun onStart() {
        super.onStart()
        Log.d("Phase", "The application is onStart phase - Visible UI but not yet interactive")
    }

    override fun onRestart() {
        super.onRestart()
        Log.d("Phase", "The application is onRestart phase - Restarting after being stopped")
    }

    override fun onResume() {
        super.onResume()
        Log.d("Phase", "The application is onResume phase - Running and interactive")
    }

    override fun onPause() {
        super.onPause()
        Log.d("Phase", "The application is onPause phase - User is leaving but the ui is partially visible")
    }

    override fun onStop() {
        super.onStop()
        Log.d("Phase", "The application is onStop phase - User already left but still running in the background")
    }

    override fun onDestroy() {
        super.onDestroy()
        Log.d("Phase", "The application is onDestroy phase - The app is already terminated")
    }

}
```

## 4.4 PUNO_HandsOn — app/src/main/java/com/example/puno_handson/

### MainActivity.kt

```kotlin
package com.example.puno_handson

import android.os.Bundle
import androidx.activity.enableEdgeToEdge
import androidx.appcompat.app.AppCompatActivity
import androidx.core.view.ViewCompat
import androidx.core.view.WindowInsetsCompat
import android.widget.*
import com.google.firebase.firestore.FieldValue
import com.google.firebase.firestore.FirebaseFirestore

class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        enableEdgeToEdge()
        setContentView(R.layout.activity_main)
        ViewCompat.setOnApplyWindowInsetsListener(findViewById(R.id.main)) { v, insets ->
            val systemBars = insets.getInsets(WindowInsetsCompat.Type.systemBars())
            v.setPadding(systemBars.left, systemBars.top, systemBars.right, systemBars.bottom)
            insets
        }

        val ejpEdtPost = findViewById<EditText>(R.id.ejpEdtPost)
        val ejpBtnPost = findViewById<Button>(R.id.ejpBtnPost)

        //startfireebaseconnection

        val conn = FirebaseFirestore.getInstance()

        ejpBtnPost.setOnClickListener {
            //user input
            val post = ejpEdtPost.text.toString()

            conn.collection("tbl_postsEjt").add(
                mapOf(
                    //field to value
                    "useridEjt" to "userid1",
                    "contentEjt" to post,
                    "nooflikesEjt" to 0,
                    "createdateEjt" to FieldValue.serverTimestamp()
                )
            ).addOnSuccessListener {
                Toast.makeText(this, "Post Created", Toast.LENGTH_SHORT).show()
            }
        }

    }
}
```

## 4.5 Generated test boilerplate (identical template in every project, package varies)

### ExampleInstrumentedTest.kt

```kotlin
package com.example.ejp_handson

import androidx.test.platform.app.InstrumentationRegistry
import androidx.test.ext.junit.runners.AndroidJUnit4

import org.junit.Test
import org.junit.runner.RunWith

import org.junit.Assert.*

/**
 * Instrumented test, which will execute on an Android device.
 *
 * See [testing documentation](http://d.android.com/tools/testing).
 */
@RunWith(AndroidJUnit4::class)
class ExampleInstrumentedTest {
    @Test
    fun useAppContext() {
        // Context of the app under test.
        val appContext = InstrumentationRegistry.getInstrumentation().targetContext
        assertEquals("com.example.ejp_handson", appContext.packageName)
    }
}
```

### ExampleUnitTest.kt

```kotlin
package com.example.ejp_handson

import org.junit.Test

import org.junit.Assert.*

/**
 * Example local unit test, which will execute on the development machine (host).
 *
 * See [testing documentation](http://d.android.com/tools/testing).
 */
class ExampleUnitTest {
    @Test
    fun addition_isCorrect() {
        assertEquals(4, 2 + 2)
    }
}
```

The remaining four test files (`JobListingApplication`, `MyApplication`, `PUNO_HandsOn` — both kinds) are the same generated template with only the package line changed:

- JobListingApplication: package `com.example.joblistingapplication`
- MyApplication: package `com.example.myapplication`
- PUNO_HandsOn: package `com.example.puno_handson`
---

# 5. Representative Layout XMLs (verbatim, EJP_HandsOn)

## 5.1 activity_main.xml — create-post screen

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/blue_background"
    tools:context=".MainActivity">

    <TextView
        android:id="@+id/ejpTvTitle"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginStart="16dp"
        android:layout_marginTop="32dp"
        android:text="Create a Post"
        android:textColor="@color/blue_primary"
        android:textSize="24sp"
        android:textStyle="bold"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <com.google.android.material.card.MaterialCardView
        android:id="@+id/ejpCardPost"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_margin="16dp"
        app:cardBackgroundColor="@android:color/white"
        app:cardCornerRadius="8dp"
        app:cardElevation="8dp"
        app:cardUseCompatPadding="true"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toBottomOf="@+id/ejpTvTitle">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="16dp">

            <TextView
                android:id="@+id/ejpTvName"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text="userid1"
                android:textColor="#333333"
                android:textSize="16sp"
                android:textStyle="bold" />

            <EditText
                android:id="@+id/ejpEdtPost"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginTop="16dp"
                android:background="@null"
                android:gravity="top|start"
                android:hint="What's on your mind?"
                android:inputType="textMultiLine"
                android:minLines="3"
                android:textColor="#333333"
                android:textColorHint="#999999"
                android:textSize="14sp" />

            <View
                android:layout_width="match_parent"
                android:layout_height="1dp"
                android:layout_marginTop="16dp"
                android:layout_marginBottom="16dp"
                android:background="#E0E0E0" />

            <com.google.android.material.button.MaterialButton
                android:id="@+id/ejpBtnPost"
                android:layout_width="match_parent"
                android:layout_height="50dp"
                android:text="POST"
                android:textColor="@android:color/white"
                android:textSize="14sp"
                android:textStyle="bold"
                app:backgroundTint="@color/blue_primary"
                app:cornerRadius="25dp" />

        </LinearLayout>

    </com.google.android.material.card.MaterialCardView>

</androidx.constraintlayout.widget.ConstraintLayout>
```

## 5.2 activity_display.xml — feed screen (ScrollView + item container)

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#F5F5F5"
    tools:context=".Display">

    <ScrollView
        android:id="@+id/ejpScrollView"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:clipToPadding="false"
        android:fillViewport="true"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:paddingTop="16dp"
            android:paddingBottom="16dp">

            <LinearLayout
                android:id="@+id/ejpBtnCreatePost"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginStart="16dp"
                android:layout_marginEnd="16dp"
                android:layout_marginBottom="8dp"
                android:clickable="true"
                android:focusable="true"
                android:foreground="?attr/selectableItemBackground"
                android:gravity="center_vertical"
                android:orientation="horizontal"
                android:padding="4dp">

                <ImageView
                    android:id="@+id/ejpImageView2"
                    android:layout_width="40dp"
                    android:layout_height="40dp"
                    android:contentDescription="Profile picture"
                    android:scaleType="fitCenter"
                    app:srcCompat="@drawable/account_circle_24dp_666666_fill0_wght400_grad0_opsz24" />

                <TextView
                    android:layout_width="0dp"
                    android:layout_height="48dp"
                    android:layout_marginStart="8dp"
                    android:layout_weight="1"
                    android:background="@drawable/bg_whats_on_your_mind"
                    android:gravity="center_vertical"
                    android:paddingStart="16dp"
                    android:paddingEnd="16dp"
                    android:text="What's on your mind?"
                    android:textColor="@android:color/white"
                    android:textSize="15sp"
                    android:textStyle="bold" />

            </LinearLayout>

            <LinearLayout
                android:id="@+id/ejpVlayout"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:orientation="vertical" />

        </LinearLayout>

    </ScrollView>

</androidx.constraintlayout.widget.ConstraintLayout>
```

## 5.3 activity_post_template.xml — inflated item template

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/main"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:background="#F5F5F5"
    tools:context=".PostTemplate">

    <com.google.android.material.card.MaterialCardView
        android:id="@+id/ejpCardPost"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginStart="16dp"
        android:layout_marginTop="8dp"
        android:layout_marginEnd="16dp"
        android:layout_marginBottom="8dp"
        app:cardBackgroundColor="@android:color/white"
        app:cardCornerRadius="8dp"
        app:cardElevation="1dp"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:orientation="vertical"
            android:padding="16dp">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:gravity="center_vertical"
                android:orientation="horizontal">

                <ImageView
                    android:id="@+id/ejpImgUser"
                    android:layout_width="32dp"
                    android:layout_height="32dp"
                    android:contentDescription="User profile picture"
                    android:scaleType="fitCenter"
                    app:srcCompat="@drawable/account_circle_24dp_666666_fill0_wght400_grad0_opsz24" />

                <TextView
                    android:id="@+id/ejpTxtUsername"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginStart="8dp"
                    android:text="Username"
                    android:textColor="#333333"
                    android:textSize="15sp"
                    android:textStyle="bold" />

                <Space
                    android:layout_width="0dp"
                    android:layout_height="1dp"
                    android:layout_weight="1" />

                <TextView
                    android:id="@+id/ejpTxtDate"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:gravity="end"
                    android:maxWidth="90dp"
                    android:text="00-00-000"
                    android:textColor="#666666"
                    android:textSize="12sp" />

            </LinearLayout>

            <TextView
                android:id="@+id/ejpTxtContent"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginTop="24dp"
                android:layout_marginBottom="24dp"
                android:gravity="center"
                android:minHeight="60dp"
                android:text="TextView"
                android:textColor="#222222"
                android:textSize="18sp" />

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:gravity="center_vertical"
                android:orientation="horizontal">

                <ImageView
                    android:id="@+id/ejpImgLikes"
                    android:layout_width="24dp"
                    android:layout_height="24dp"
                    android:contentDescription="Likes"
                    app:srcCompat="@drawable/ic_favorite_border" />

                <TextView
                    android:id="@+id/ejpTxtLikes"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginStart="4dp"
                    android:text="0"
                    android:textColor="#666666"
                    android:textSize="13sp" />

                <ImageView
                    android:id="@+id/ejpImgComments"
                    android:layout_width="24dp"
                    android:layout_height="24dp"
                    android:layout_marginStart="24dp"
                    android:contentDescription="Comments"
                    app:srcCompat="@drawable/ic_comment" />

                <TextView
                    android:id="@+id/ejpTxtComments"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_marginStart="4dp"
                    android:text="0"
                    android:textColor="#666666"
                    android:textSize="13sp" />

            </LinearLayout>

        </LinearLayout>

    </com.google.android.material.card.MaterialCardView>

</androidx.constraintlayout.widget.ConstraintLayout>
```

## 5.4 colors.xml (palette pattern)

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <color name="black">#FF000000</color>
    <color name="white">#FFFFFFFF</color>
    <color name="blue_background">#E3F2FD</color>
    <color name="blue_primary">#1976D2</color>
    <color name="blue_accent">#2196F3</color>
</resources>
```

## 5.5 themes.xml (theme pattern)

```xml
<resources xmlns:tools="http://schemas.android.com/tools">
    <!-- Base application theme. -->
    <style name="Base.Theme.EJP_HandsOn" parent="Theme.Material3.DayNight.NoActionBar">
        <!-- Customize your light theme here. -->
        <!-- <item name="colorPrimary">@color/my_light_primary</item> -->
    </style>

    <style name="Theme.EJP_HandsOn" parent="Base.Theme.EJP_HandsOn" />
</resources>
```
---

# 6. Gradle & Manifest (verbatim, EJP_HandsOn)

## 6.1 app/build.gradle.kts

```kts
plugins {
    alias(libs.plugins.android.application)
    id("com.google.gms.google-services")
}

android {
    namespace = "com.example.ejp_handson"
    compileSdk {
        version = release(37)
    }

    defaultConfig {
        applicationId = "com.example.ejp_handson"
        minSdk = 24
        targetSdk = 37
        versionCode = 1
        versionName = "1.0"

        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            optimization {
                enable = false
            }
        }
    }
    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_11
        targetCompatibility = JavaVersion.VERSION_11
    }
}

dependencies {
    implementation(libs.androidx.activity.ktx)
    implementation(libs.androidx.appcompat)
    implementation(libs.androidx.constraintlayout)
    implementation(libs.androidx.core.ktx)
    implementation(libs.material)
    testImplementation(libs.junit)
    androidTestImplementation(libs.androidx.espresso.core)
    androidTestImplementation(libs.androidx.junit)

    implementation(platform("com.google.firebase:firebase-bom:34.19.0"))
    implementation("com.google.firebase:firebase-firestore")
}
```

## 6.2 build.gradle.kts (project level)

```kts
// Top-level build file where you can add configuration options common to all sub-projects/modules.
plugins {
    alias(libs.plugins.android.application) apply false
    id("com.google.gms.google-services") version "4.5.0" apply false
}
```

## 6.3 AndroidManifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <uses-permission android:name="android.permission.INTERNET" />

    <application
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.EJP_HandsOn">
        <activity
            android:name=".MainActivity"
            android:exported="true"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>

        <activity
            android:name=".Display"
            android:exported="true" />

        <activity
            android:name=".PostTemplate"
            android:exported="true" />
    </application>

</manifest>
```

## 6.4 gradle/libs.versions.toml

```toml
[versions]
agp = "9.3.2"
coreKtx = "1.19.0"
junit = "4.13.2"
junitVersion = "1.3.0"
espressoCore = "3.7.0"
appcompat = "1.8.0"
material = "1.14.0"
activityKtx = "1.13.0"
constraintlayout = "2.2.2"

[libraries]
androidx-core-ktx = { group = "androidx.core", name = "core-ktx", version.ref = "coreKtx" }
junit = { group = "junit", name = "junit", version.ref = "junit" }
androidx-junit = { group = "androidx.test.ext", name = "junit", version.ref = "junitVersion" }
androidx-espresso-core = { group = "androidx.test.espresso", name = "espresso-core", version.ref = "espressoCore" }
androidx-appcompat = { group = "androidx.appcompat", name = "appcompat", version.ref = "appcompat" }
material = { group = "com.google.android.material", name = "material", version.ref = "material" }
androidx-activity-ktx = { group = "androidx.activity", name = "activity-ktx", version.ref = "activityKtx" }
androidx-constraintlayout = { group = "androidx.constraintlayout", name = "constraintlayout", version.ref = "constraintlayout" }

[plugins]
android-application = { id = "com.android.application", version.ref = "agp" }

```
---

# 7. New-Code Checklist for the Finals (HanapbuHire)

1. Work inside `JobListingApplication` (package `com.example.joblistingapplication`). One Activity per screen; copy the skeleton in §3.1; layout `activity_<screen>.xml`; register in the manifest (§3.12).
2. Pick ONE id prefix for the whole app, mirroring the class pattern — e.g. `hbTvTitle`, `hbBtnApply`, `hbEdtSearch`, `hbVlayout` (HanapbuHire). Keep it consistent within each file; do not mix the two observed conventions in one screen.
3. Collections exactly per the brief (§1): `tbl_users`, `tbl_job_listings`, `tbl_applications` with the brief's field names. Timestamps: `FieldValue.serverTimestamp()`.
4. Writes: `conn.collection("...").add(mapOf(...)).addOnSuccessListener { Toast + navigate }` (§3.4). Reads: `addSnapshotListener` + template inflation loop (§3.4). Do not introduce RecyclerView unless the group decides the teacher-taught structure is insufficient — the template-inflation pattern is what class used.
5. Validation and widgets: §3.5. Navigation/extras: §3.6. Dates: §3.7. Comments: §3.3.
6. Keep `firebase-firestore` from the existing gradle; keep `minSdk 24` / SDK 37 setup as-is (§3.11).
7. For the report PDF: screenshot the code per Kotlin file (section 4 is the source of truth), the Firestore collections, and emulator outputs after each feature.
8. Unique feature (from Week 1 plan): Job Fit Indicator — implement it in class style (read user skills + job requirements arrays, compare, set a TextView).
9. Deadlines: Week 6 — Dec 14–20, 2026: final PDF, APK, video (all members), shared Drive submission.

---

---
# 8. Secondary references (converted lesson notes)

- `C:/Users/Kath Puno/Downloads/AppDev/Lessons/md/5 - Kotlin Variables Logic Expressions.md` (14 slides)
- `C:/Users/Kath Puno/Downloads/AppDev/Lessons/md/Android Lifecycle.md` (23 slides)
- `C:/Users/Kath Puno/Downloads/AppDev/Lessons/md/Android View Groups.md` (13 slides; the `(1)` copy is byte-identical and was skipped)
- Wireframes (Rick's HTML archetypes + extracted text): `C:/Users/Kath Puno/Downloads/AppDev/Finals Exam/FinalizedWireframes/` — see `wireframes-text.md` for a text dump of every screen.

*End of reference. Generated from every `.kt` file under `AndroidStudioProjects` plus the EJP_HandsOn config (2026-11-20); lesson-note pointers added 2026-11-22.*