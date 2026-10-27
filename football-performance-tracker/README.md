# Football Performance Tracker (Android PWA)

A small installable Progressive Web App for personal football development. It includes:
- Four training days per week
- Five drills for each of six attributes (30 total)
- Session minutes, energy, notes and 1–5 ratings
- Progress summary, rating bars and a rating-trend chart
- Local browser storage, JSON export and restore
- Offline cache after the first successful online load

## Run it locally

Because service workers and PWA installation require a secure context, do not expect full installation/offline behavior from opening `index.html` directly as a file. You can test the tracker in a browser, but publish it over HTTPS for installation.

## Publish free with GitHub Pages

1. Sign in or create an account at https://github.com.
2. Create a new **public** repository, for example `football-performance-tracker`.
3. Upload `index.html`, `manifest.json`, `sw.js`, `icon.svg`, and this README to the repository root.
4. Open repository **Settings → Pages**.
5. Under build/deployment, choose **Deploy from a branch**, select `main` and `/ (root)`, then save.
6. Wait for GitHub Pages to publish the site. The address will look like `https://YOUR-USERNAME.github.io/football-performance-tracker/`.
7. Open that HTTPS address on your Android phone in Chrome. Wait for the page to finish loading once while online.

## Install on Android

1. Open the published HTTPS website in Chrome.
2. Tap Chrome's three-dot menu.
3. Choose **Install app** if shown, or **Add to Home screen**.
4. Confirm the name and tap Install/Add.
5. Open **Football Tracker** from the new home-screen icon.

Chrome menu wording varies by Android version. The web app needs to be served over HTTPS for its service worker and installability behavior.

## Local data and backups

- Session data is stored in `localStorage` in the browser on that device.
- Data is not automatically sent to a server or synced between devices.
- Clearing Chrome site data, uninstalling/clearing app data, or using a different browser/profile can remove or separate records.
- On the History tab, use **Export backup** to download a JSON file regularly.
- Use **Restore from a backup file** to import that JSON backup later.
- Keep backup files somewhere safe. Anyone with access to the unlocked device/browser profile may be able to access the tracker.

## Updating the app

After replacing files in GitHub, increment the cache name in `sw.js` (for example, `football-tracker-v2`) so installed copies refresh to the new version. Revisit the site online to receive the update.

## Notes

This is a static client-side PWA, not a native Play Store APK. It can be installed from the browser after HTTPS hosting, and it works offline after its assets are cached. A Play Store release would require a separate Android packaging and publishing process.
