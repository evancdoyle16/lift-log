# Lift Log — Setup Guide

A 3-day full-body dumbbell workout tracker with RPE-based weight progression and cross-device sync via Pantry.

**Files in this folder:**
- `workout-tracker.html` — the app
- `README.md` — this file

---

## Step 1 — Get a Pantry ID (for cross-device sync)

1. Go to [getpantry.cloud](https://getpantry.cloud/).
2. Enter your email (`evancdoyle16@gmail.com`).
3. You'll see a pantry ID like `a1b2c3d4-5e6f-7890-abcd-ef1234567890`. **Copy it somewhere safe** (Notes, 1Password, etc.) — if you lose it, you lose access to your cloud-synced data.
4. You'll paste this into the app's **Settings** screen once it's running.

The pantry is free, no account, no password. The ID itself is your credential — anyone with it can read/write your data. Workout logs aren't sensitive, so this is fine.

---

## Step 2 — Deploy to GitHub Pages

Run these four commands in a terminal on your laptop from the folder where `workout-tracker.html` lives:

```bash
# 1. Create a new GitHub repo (replace YOUR-USERNAME)
gh repo create lift-log --public --description "Personal workout tracker"

# 2. Initialize locally, add the app, push
git init
cp workout-tracker.html index.html
git add index.html
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/lift-log.git
git push -u origin main

# 3. Enable GitHub Pages
gh api repos/YOUR-USERNAME/lift-log/pages -X POST -f source[branch]=main -f source[path]=/

# 4. Get your URL (takes ~1 minute for first deploy)
gh api repos/YOUR-USERNAME/lift-log/pages --jq '.html_url'
```

Your app will be live at `https://YOUR-USERNAME.github.io/lift-log/`.

**Don't have `gh` installed?** Install with `brew install gh` on macOS, then `gh auth login`. Alternatively:
- Create the repo manually at github.com/new (name it `lift-log`, make it public).
- Rename `workout-tracker.html` to `index.html`.
- Run `git init && git add . && git commit -m "init" && git remote add origin https://github.com/YOUR-USERNAME/lift-log.git && git push -u origin main`.
- In the repo on github.com, go to Settings → Pages → set Branch to `main` (`/` root) and Save.

---

## Step 3 — Add the app to your phone's home screen

**iPhone (Safari):**
1. Open `https://YOUR-USERNAME.github.io/lift-log/` in Safari.
2. Tap the Share button → "Add to Home Screen" → Add.
3. An app icon with the 🏋️ emoji appears on your home screen. Launch it — runs fullscreen like a native app.

**Android (Chrome):**
1. Open the URL in Chrome.
2. Tap the three-dot menu → "Add to Home Screen" → Add.

---

## Step 4 — Connect your Pantry ID

1. Open the app (either on phone, or visit the URL in any browser tab on your laptop).
2. Tap **Settings** (gear icon, top-right on the home screen).
3. Paste your Pantry ID into the **Pantry ID** field. The app pulls from the cloud automatically.
4. Repeat on your other device with the **same Pantry ID**. Both devices now share state.

**Sync behavior:**
- Every save writes locally first (instant), then pushes to Pantry in the background.
- When you open the app, it checks Pantry and pulls any newer data.
- Last-write-wins by timestamp. If you somehow log on two devices simultaneously, the most recently saved copy wins.
- Fully functional offline — sync resumes when you're back online.

---

## Using the app

**Home screen:** tap `Start Day 1`, `Start Day 2`, or `Start Day 3`.

**During a session:**
- Exercises are grouped by suggested weight (all 10 lb first, then 15 lb, etc.) to minimize dumbbell changes. Toggle to "Program order" if you prefer the original sequence.
- **Tap the exercise name** for a form cue plus buttons to watch a YouTube demo or open the Muscle & Strength exercise page.
- Adjust the weight with `−` / `+` if the suggestion feels wrong before you start a set.
- After each set: tap reps completed, tap RPE (Rate of Perceived Exertion, 1–10).
  - 6 = easy, 4+ reps left
  - 7 = moderate, 3 left
  - 8 = hard, 2 left
  - 9 = very hard, 1 left
  - 10 = absolute max
- When all sets across all exercises are logged, the "Finish" button turns blue. Tap it.

**After a session:** you'll see a "Next Session Preview" with suggested weights for each exercise — plus the reason for each change (e.g., "RPE 7.3, all reps hit — increase 15→20 lb").

**History:** all past sessions viewable from the home screen.

---

## Starting weights (already seeded — conservative)

| Exercise | Start |
|---|---|
| Dumbbell Squat | 15 lb |
| Dumbbell Stiff Legged Deadlift | 20 lb |
| Dumbbell Deadlift | 20 lb |
| Bent Over Dumbbell Row | 15 lb |
| Dumbbell Bench Press | 15 lb |
| Dumbbell Lunge | 15 lb |
| Dumbbell Step Up | 15 lb |
| All isolation (curls, raises, extensions, flys, presses) | 10 lb |

Lots of exercises start at 10 lb. That's the floor of your dumbbell set. Many will feel light the first 1–2 sessions — log them as RPE 6–7 and the app will ramp them up fast.

---

## Backup & portability

- **Export JSON** in Settings downloads a full backup any time.
- **Import JSON** restores from one.
- If you ever lose your Pantry ID, you can still export from any device that has the app open and re-seed a new pantry.

---

## Updating the app later

When a new version of `workout-tracker.html` is ready:

```bash
cp /path/to/new/workout-tracker.html index.html
git add index.html
git commit -m "Update"
git push
```

Changes go live in ~30 seconds. Your data stays intact (it lives in Pantry + localStorage, not in the HTML).

---

## Progression rules (for reference)

After each session, each exercise gets a new suggested weight based on the average RPE of its working sets:

- **Avg RPE ≤ 7.4 + all reps hit** → +5 lb (next dumbbell up)
- **Avg RPE 7.5–8.4 + all reps hit** → same weight, try +1 rep next time
- **Missed reps** → same weight (hit the target first)
- **Avg RPE ≥ 9.5** → −5 lb (drop to the previous dumbbell)

Available weights: 10, 15, 20, 25, 30, 35, 40, 45, 50 lb.
