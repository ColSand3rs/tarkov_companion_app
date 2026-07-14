# Tarkov Companion — Android app

A native Android wrapper around the Tarkov Companion web app.

**Why this exists:** in a browser, the tarkov.dev profile lookup gets blocked by CORS —
that service was built for a desktop app and doesn't grant web pages permission.
This wrapper makes HTTP calls **in native Java**, and native calls aren't subject to CORS
at all. The profile search that fails in a browser works here.

It also loads the app from a real `https://` origin (`appassets.androidplatform.net`)
rather than `file://`, so `localStorage` behaves properly and your stash, offers and
craft timers persist across launches.

---

## Get the APK — no tools to install (easiest)

1. Create a **new GitHub repo** (private is fine).
2. Upload every file in this folder, keeping the structure (the web UI drag-and-drop
   uploader works — just drag the whole folder in).
3. Go to the **Actions** tab → the *Build APK* workflow runs automatically on push.
   (If it doesn't, click *Build APK* → *Run workflow*.)
4. When it finishes (~3–5 min), open the run and download the
   **`tarkov-companion-apk`** artifact. Inside is `app-debug.apk`.
5. Copy it to your phone and tap it. Android will ask you to allow
   *Install unknown apps* for whichever app you opened it from — allow it.

The debug APK is signed with Android's standard debug key, so it installs normally.
It just can't be published to the Play Store — irrelevant for personal use.

## Build in Android Studio

Open this folder as a project → let it sync → **Run**. That's it.

## Build from a terminal

Requires the Android SDK and JDK 17+:

```bash
gradle assembleDebug
# → app/build/outputs/apk/debug/app-debug.apk
```

---

## Updating the app

The entire UI is one file: **`app/src/main/assets/index.html`**.
Edit it, rebuild, done. No JavaScript build step, no npm, no dependencies.

## Permissions

- `INTERNET` — live prices, item icons, craft catalog, profile lookup
- `VIBRATE` — haptics when a craft finishes

## What the native bridge does

`MainActivity.java` exposes `TKV.httpGet(url, callbackId)` to JavaScript. It performs the
request on a background thread and hands the result back to the page. Requests are
restricted to `tarkov.dev` hosts — it is not a general-purpose proxy.

The web app detects the bridge automatically (`window.TKV`). Running in a plain browser,
it falls back to `fetch()` plus optional relays. The same `index.html` works in both places.
