# Sub-4 companion — the connected setup

Four pieces. Each one is independent; do them in whatever order you like.

---

## 1. The home-screen app (10 minutes, free)

The folder contains four files: `index.html`, `icon.png`, `manifest.webmanifest`, `sw.js`. Host them anywhere; GitHub Pages is free and takes five minutes.

1. github.com → sign in (or create an account) → **New repository** → name it `sub4`, Public, Create.
2. **Add file → Upload files** → drop all four files in → Commit.
3. Repository **Settings → Pages** → Source: *Deploy from a branch* → Branch: `main`, folder `/ (root)` → Save.
4. After a minute your app is at `https://<your-username>.github.io/sub4/`.
5. Open that URL in **Safari** on the iPad and the iPhone → Share → **Add to Home Screen**. It opens full-screen with its own icon, and works offline after the first load.

**What "connected" means here, honestly:** the app is *local-first*. Its data lives in the browser on that device. That's the right design for a garage with patchy Wi-Fi — the pacer never stalls — but it means the iPad and the iPhone each keep their own log. Two ways to live with that:

- **Simplest:** the iPad is the pacer, the iPhone is the logbook. Runs finish on the iPad; the Health bridge (§2) puts the measured version into the iPhone anyway. Lifts, weight and BP go in the iPhone. Nothing is lost, one copy of each thing.
- **Two-way sync:** needs a small cloud backend (Firebase's free tier, about 15 minutes to set up). Say the word and I'll add it — the app is built so sync drops in behind the same storage layer.

Either way, **Plan → Backup → Export** copies everything as text; paste it into Notes occasionally. The code contains no data, so a public repo is fine.

The Claude-chat version of the app still works and stays as a fallback. Pick one as primary — I'd use the home-screen one.

---

## 2. The Health bridge — measured runs, weight and BP into the app (20 minutes, once)

Build one Shortcut. Run it after each run (one tap from the Shortcuts widget or by asking Siri), and it opens the app with today's measured numbers already imported: duration, distance and average heart rate from the Watch, plus the latest weight and blood pressure in Health if you have a scale or cuff that syncs. Exact action names vary slightly by iOS version; the shape is the same.

**Shortcut: "Log run to Sub-4"**

1. **Find Workouts** (Health) — Sort by *End Date*, Latest First, Limit **1**.
2. **Get Details of Workout** → *Duration* → **Convert** to minutes → **Round** to 0 places → save as variable `MIN`.
3. **Get Details of Workout** → *Distance* → **Convert** to miles → **Round** to 1 place → `MI`.
4. **Get Details of Workout** → *Start Date* → `START`; *End Date* → `END`.
5. **Find Health Samples** — Type *Heart Rate*, filter *Start Date is after* `START` *and before* `END`, Limit 5000.
6. **Calculate Statistics** — *Average* of the samples → **Round** to 0 → `HR`.
7. **Find Health Samples** — Type *Weight*, Sort Latest First, Limit 1 → **Round** to 1 → `KG`. (Only if you have a scale that writes to Health; otherwise skip and leave the field blank.)
8. **Find Health Samples** — Type *Blood Pressure Systolic*, Latest, Limit 1 → `SYS`; same for *Diastolic* → `DIA`. (Only with a Health-connected cuff.)
9. **Format Date** — `END`, custom format `yyyy-MM-dd` → `DATE`.
10. **Text**:
    `HEALTH|DATE|MIN|MI|HR|KG|SYS|DIA`
    (insert the variables; leave any you skipped empty, keeping the `|`).
11. **URL Encode** the text.
12. **Open URLs**: `https://<your-username>.github.io/sub4/#` followed by the encoded text.

The app reads the line from the URL, writes the run to that date, updates weight and BP, clears the URL, and shows "Imported from Health". If you'd rather push into the Claude-chat version instead, replace step 12 with **Copy to Clipboard** and use the **Paste from Health** button on the Today screen.

If your iOS offers an automation trigger for *when a workout ends*, attach this Shortcut to it and the whole thing becomes automatic. If not, it's one tap.

---

## 3. Let the Watch enforce "easy means easy" (5 minutes)

The single most common way amateurs break a marathon build is running easy days too hard. Let the Watch police it.

1. On the Watch: **Workout** → scroll to *Outdoor Run* (and separately *Indoor Run*) → tap the **⋯** → **Alerts** → **Heart Rate**.
2. Choose **Above** and set **135 bpm** for now.
3. On easy days, if it buzzes, slow down until it stops. On quality days, ignore it or switch it off — it's an easy-day tool.

Why 135: your estimated max is ~175 at 46, and easy running sits around 70–75% of that. It's a starting number. After the week-8 parkrun, tell me the peak heart rate the Watch recorded and I'll set the real ceiling from that instead of from a formula.

Separately, structured sessions can be built as **custom workouts** (Workout → Outdoor Run → ⋯ → Create Workout) with pace alerts — useful outdoors where the pacer isn't in front of you.

---

## 4. The weekly review loop (the part that actually coaches you)

This is what turns a static plan into an adaptive one.

**Once:**
- Strava, free tier. In the Strava app: *Settings → Applications, services and devices → Health* → allow it to read workouts from Apple Health, so every Watch run appears in Strava automatically. (If you buy QZ, its Strava upload adds the treadmill's own readings too.)
- Connect the **Strava connector** to Claude — the card in this chat does it in one tap.

**Every Sunday evening:**
1. Weigh-in and BP reading (log in the app).
2. App → Week → **Copy this week for Claude**.
3. Paste it into this chat with the words: *"Review my week."*

I'll read your Strava activities directly — actual pace, actual heart rate, actual distance — set them against the plan, tell you which easy runs weren't easy, whether the quality session hit its pace, whether the long run needs adjusting, and what next week should look like. If a niggle appeared, we deal with it then rather than in three weeks. That's the coaching loop, and it costs nothing.

A recurring Sunday reminder for this is in your Reminders app.
