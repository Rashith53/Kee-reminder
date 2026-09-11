# Kee Reminder 🥰

A private, offline-first task reminder, habit tracker, period tracker, and stress-release page — installable as a Chrome app. Nothing is sent anywhere by default; all data stays in your browser's local storage on your own device.

## 1. Put this on GitHub

1. Create a new **public** GitHub repository (Pages needs public on the free tier, or Pages enabled on a private repo if your plan supports it).
2. Upload **all** files in this folder, keeping the structure exactly as-is:
   ```
   index.html
   manifest.webmanifest
   sw.js
   .nojekyll
   icons/icon-192.png
   icons/icon-512.png
   ```
   Easiest way: on the repo page, click **Add file → Upload files**, drag the whole folder contents in (including the `icons` folder), and commit.

## 2. Turn on GitHub Pages

1. In the repo, go to **Settings → Pages**.
2. Under **Build and deployment → Source**, choose **Deploy from a branch**.
3. Branch: `main` (or `master`), folder: `/ (root)`. Save.
4. GitHub will give you a URL like `https://yourusername.github.io/your-repo-name/`. It can take a minute or two to go live.

## 3. Install it as an app in Chrome

1. Open that URL in Chrome (desktop or Android).
2. Chrome will show an install icon in the address bar (desktop) or an "Add to Home screen" prompt (Android) — the app also has its own **⬇️ install button** in the top-right of the Tasks page once Chrome detects it's installable.
3. Click/tap install. It now opens like a normal app, works offline, and keeps your data locally.

On iPhone/iPad (Safari, not Chrome): open the URL, tap Share → **Add to Home Screen**.

## About the AI feature in "Refresh Me"

The **Refresh Me** page normally uses small built-in offline responses — nothing leaves your device. If you tap the **⚙️** on that page and add your own Anthropic API key, it switches to real AI-generated advice tailored to what you write (it also understands Tanglish and other transliterated languages). This sends your text to Anthropic's API when turned on. Crisis-related messages are always caught by the built-in offline safety responses first, regardless of whether AI is on.

Get a key at [console.anthropic.com](https://console.anthropic.com) if you want to use this. It's entirely optional — the app works fully offline without one.

## Background alerts (alarms that fire even with the app closed) — optional

By default, task alarms only fire while the app is open (foreground or backgrounded, but not fully closed) — that's a hard limit of browsers, not something this app can work around on its own. To get real alerts even with the app fully closed, you can run the small server in the `server/` folder. It uses the Web Push standard: your server holds a private key, your browser holds a subscription, and when a task is due the server pushes a notification straight to your device.

**What this changes:** each task's title and due time (not habit/period data) gets sent to your server so it knows when to alert you. Habit and period data still never leave your device.

### Set it up

1. **Install Node.js** (18+) on your machine if you don't have it.
2. In the `server/` folder: `npm install`
3. Generate your keys: `npm run generate-keys` — copy the two printed keys.
4. Copy `.env.example` to `.env` and fill in:
   - `VAPID_PUBLIC_KEY` / `VAPID_PRIVATE_KEY` — from step 3
   - `ALLOWED_ORIGINS` — your GitHub Pages URL, e.g. `https://yourusername.github.io`
   - `SHARED_SECRET` — any long random string (stops strangers from posting fake data to your server); generate one with `node -e "console.log(require('crypto').randomBytes(24).toString('hex'))"`
5. Run it: `npm start`. For real use you need this running somewhere reachable 24/7 — a free tier on **Render**, **Railway**, or **Fly.io** all work. Set the same environment variables there as in your `.env`. (Note: some free tiers "sleep" after inactivity, which can delay alerts by up to a minute until the next scheduler tick after waking — a free external ping service hitting `/api/health` every few minutes keeps it awake if that matters to you.)
6. In the app, tap the **🔔** icon on the Tasks page, enter your server's URL and the shared secret, and tap **Turn on**. Allow notifications when the browser asks.

From then on, any task with a due time/alarm syncs to your server automatically, and reminders will reach you even with the app fully closed.

## Notes

- All data (tasks, habits, period history) lives only in your browser's local storage. Use **Periods → Backup → Export data** occasionally to save a copy, especially before switching phones or browsers.
- The two icon PNGs in `icons/` are simple placeholders — swap them out for your own 192×192 and 512×512 images (same filenames) if you'd like a custom app icon.
