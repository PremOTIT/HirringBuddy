# HiringBuddy PWA — Setup

A real installable app icon, works offline, all data stays on her phone only — nothing is sent anywhere.

## Why it needs hosting (quick, free, 5 minutes)

"Add to Home Screen" only registers as a proper installable app when the files are served over `https://`, not opened directly as a file. The easiest free option:

### Option A — GitHub Pages (recommended, free, permanent link)
1. Create a free GitHub account if she doesn't have one.
2. Create a new repository, e.g. `hiringbuddy`.
3. Upload these 5 files to it: `index.html`, `manifest.json`, `sw.js`, `icon-192.png`, `icon-512.png`.
4. Go to Settings → Pages → set source to the `main` branch → Save.
5. GitHub gives a URL like `https://username.github.io/hiringbuddy/`.

### Option B — Netlify Drop (fastest, no account needed for a quick test)
1. Go to `app.netlify.com/drop` in a browser.
2. Drag the folder with all 5 files onto the page.
3. It gives an instant `https://random-name.netlify.app` URL.
4. (Optional) Create a free account to keep the link permanent instead of temporary.

## Installing on her phone

1. Open the hosted link in **Chrome** on her Android phone.
2. Tap the **⋮** menu → **"Add to Home Screen"** (or Chrome will show an automatic install banner).
3. Confirm — HiringBuddy now appears as a real app icon on her home screen, opens full-screen, no browser bar.

## First use

- On first open, she'll be asked to **set a 4-digit PIN** — this locks the app each time it's opened. It's a phone-level deterrent (not full encryption), appropriate for a personal tracker.
- **If she forgets the PIN**: there's no recovery — she'd need to clear the app's storage in phone Settings → Apps → HiringBuddy → Storage → Clear, which also erases her saved candidates. Worth telling her this upfront so she picks something memorable.

## Data & security notes (be upfront with her about this)

- All candidate data lives in the browser's local storage (IndexedDB) **on her device only** — nothing is uploaded to any server, including GitHub/Netlify, which only host the empty app shell.
- This is appropriate protection for a personal tool, but it is **not** full disk encryption — if someone has her phone unlocked, the PIN alone doesn't stop them from finding ways in. It protects against casual snooping, not a determined attacker with device access.
- Works fully offline after the first load (service worker caches the app shell).
- **Backing up data**: there's currently no built-in export button. If you want, I can add a "Export as JSON" button so she can periodically back up her candidate list to a file — worth doing before she relies on this long-term.

## Google Drive backup setup (one-time, ~10 minutes)

This lets her sign in with her own Google account and back up/restore her candidate data to a private file in her own Drive — nothing goes through any server of yours, and you never see or touch her data.

1. Go to **console.cloud.google.com** and create a free project (any name, e.g. "HiringBuddy").
2. Go to **APIs & Services → Library**, search for **"Google Drive API"**, click **Enable**.
3. Go to **APIs & Services → OAuth consent screen**:
   - User type: **External**
   - Fill in app name (e.g. "HiringBuddy"), your email as support contact
   - Scopes: add `https://www.googleapis.com/auth/drive.appdata`
   - Test users: add **her Google account email** (required while the app is in "Testing" mode — this restricts sign-in to only her, which is actually what you want)
4. Go to **APIs & Services → Credentials → Create Credentials → OAuth Client ID**:
   - Application type: **Web application**
   - Authorized JavaScript origins: add your hosted URL (e.g. `https://username.github.io`)
   - Create — copy the **Client ID** it gives you (looks like `123456-abc.apps.googleusercontent.com`)
5. Open `index.html`, find this line near the bottom:
   ```js
   const GOOGLE_CLIENT_ID = 'YOUR_CLIENT_ID.apps.googleusercontent.com';
   ```
   Replace it with the real Client ID from step 4.
6. Re-upload `index.html` to GitHub Pages/Netlify.

**What this does and doesn't do:**
- Uses Google's `drive.appdata` scope — this creates a **hidden folder only this app can see**, not her regular visible Drive. She won't see a random JSON file cluttering her Drive UI.
- Her Google sign-in doubles as the "login" — if she has 2-Step Verification enabled on her Google account already, that's her MFA, with nothing extra to build or maintain.
- Backup/Restore buttons appear in the sidebar once she signs in. Restore always asks for confirmation before overwriting local data.
- Because the app stays in Google's "Testing" publishing status (step 3), only the test-user emails you added can sign in — this is a feature here, not a limitation, since it keeps the app private to her without needing Google's full app-review process.

## Updating the app later

If you want to change anything (add a feature, fix a bug), just re-upload the updated `index.html` to the same GitHub repo / Netlify site — her installed app will pick up the change next time she opens it with internet access (the service worker refreshes the cache).
