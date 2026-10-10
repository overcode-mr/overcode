# Diwan deep-link site (GitHub Pages)

This folder hosts the two things Universal Links need so that a shared poem link
opens the app (or the App Store if it isn't installed).

## 1. Create the GitHub Pages user site

Universal Links require the Apple App Site Association (AASA) to be served from
the **domain root** over HTTPS. On GitHub Pages that means a **user site**:

1. Create a public repo named exactly `<your-username>.github.io`.
2. Copy the contents of this `deeplink-site/` folder into that repo's root:
   - `.well-known/apple-app-site-association`  (no file extension, JSON)
   - `poem/index.html`
3. Enable Pages: repo → Settings → Pages → Deploy from branch `main` / root.
4. Verify these URLs load:
   - `https://<your-username>.github.io/.well-known/apple-app-site-association`
     → must return the JSON with `Content-Type: application/json` (GitHub Pages
     serves extensionless files as JSON automatically).
   - `https://<your-username>.github.io/poem?path=poems/xxxxx`
     → shows the fallback page.

## 2. Point the app at your domain

In `diwan/App/DeepLink.swift` set:

```swift
static let webBase = "https://<your-username>.github.io"
```

(Replace `REPLACE-ME`.) The `appIDs` in `apple-app-site-association` is already
`957G683SDX.com.overcode.diwan` (your team + bundle id). If your Team ID ever
changes, update it there too.

## 3. Xcode capabilities (can't be scripted — do in the UI)

Target **diwan** → **Signing & Capabilities**:

- **+ Capability → Associated Domains** → add:
  `applinks:<your-username>.github.io`
- **Info tab → URL Types** (adds `CFBundleURLTypes`) → add a URL Type with
  **URL Schemes = `diwan`** (identifier e.g. `com.overcode.diwan`). This enables
  the `diwan://poem?path=...` fallback scheme.

## 4. Test

- Build & run on device.
- Put a Universal Link in Notes/Messages (not the address bar) and tap it:
  `https://<your-username>.github.io/poem?path=<poem-doc-path>`
  e.g. `.../poem?path=poems2/AbCdEf` → the app opens straight to that poem.
- Delete the app and tap the same link → the fallback page → App Store.

Nothing here is uploaded to Firebase; it's static hosting only.
