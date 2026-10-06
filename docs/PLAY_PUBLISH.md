# Proxyku — Google Play publish checklist

Developer: Ethentias · Package: `com.ethentias.proxyku` (permanent after
first upload) · Fully free: no ads, no in-app products.

## 1. Signing (required for upload)
Play accepts **AAB** signed with your upload key. Debug builds use the default debug key; do NOT upload those.

```bash
# upload keystore already exists at ~/tuproxy-upload.jks (alias tuproxy).
# It can be reused for this new app listing — keep the .jks + passwords SECRET, never commit.

# configure signing via env (no secrets in repo):
export TUPROXY_STORE_FILE=~/tuproxy-upload.jks
export TUPROXY_STORE_PASSWORD=...
export TUPROXY_KEY_ALIAS=tuproxy
export TUPROXY_KEY_PASSWORD=...
./build.sh bundle   # → app/build/outputs/bundle/release/app-release.aab
```

`app/build.gradle.kts` reads these env vars when present; without them the release build stays unsigned (fine for local testing, rejected by Play).

## 2. Build the upload artifact
```bash
./build.sh bundle    # AAB for Play (preferred)
./build.sh release   # signed APK for sideload testing
./build.sh debug     # local dev
```
Current version: `versionCode = 1`, `versionName = "1.0"`. Bump `versionCode` (+1) on every Play upload.

## 3. New app listing (new package = new app)
- Create a **new app** in Play Console (do not reuse the old `com.tustudio.tuproxy` draft).
- Developer name: `Ethentias`. App title: `Proxyku: Proxy Server`.
- Old closed-testing progress does not carry over; testers must join the new opt-in link.

## 4. Multi-resolution readiness (done in code)
- Compose + `BoxWithConstraints`: single column on phones (< 600dp), two columns on tablets/landscape (≥ 600dp).
- Scrollable content, `supports-screens: anyDensity + small→xlarge`, `adjustResize`, sp/dp only, no fixed-pixel layouts.
- Launcher icons mdpi→xxxhdpi + adaptive icon from the P logo; in-app header uses `drawable/logo_app.png`.
- Test on: small phone (360×640), standard (412×915), tablet (800×1280), landscape. Play pre-launch report will also cover this automatically.

## 5. Notifications (running vs stopped)
- Running: ongoing foreground notification `Proxyku [HTTP+…] is running` with live ↓/↑ rates + **Stop** action.
- Stopped: dismissible `Proxyku is stopped — tap to start` so status is always visible.
- Android 13+: runtime `POST_NOTIFICATIONS` prompt on first launch (`MainActivity`).

## 6. Play Console data-safety + content forms (simplified: no ads, no IAP)
- Data safety: **No data collected, no data shared** — nothing to declare, no SDK section needed.
- Store listing: **Contains ads → No**, **In-app purchases → No**.
- Content: no user-generated content, no login, target audience 13+ (network tool). Privacy-policy URL still required: `https://github.com/hidayanto56/Proxyku/blob/main/PRIVACY_POLICY.md` (rename the GitHub repo TuProxy → Proxyku so the URL is live).
- Permissions declared: INTERNET, ACCESS_NETWORK_STATE, FOREGROUND_SERVICE(+DATA_SYNC), WAKE_LOCK, POST_NOTIFICATIONS. Foreground-service type `dataSync` matches the proxy use.

## 7. Store listing assets (in `store-assets/`)
- Icon `icon-512.png`, feature graphic `feature-graphic.png` (1024×500).
- Screenshots: phone (min 2), 7-inch + 10-inch tablet recommended, landscape one recommended. Re-take after the rename (old shots show the old name).
- Texts: `docs/PLAY_LISTING.md`.

## 8. Pre-upload sanity
- [ ] `./build.sh bundle` succeeds, install check on Android 9 + 14
- [ ] Toggles, copy buttons, chart, rotation, dark theme, notification Stop action verified
- [ ] QS tile: add via notification shade edit, toggle on/off, state follows service
- [ ] Connection log populates during real proxy use; Clear works
- [ ] In-App Review triggers on 3rd/10th/25th open (Play quota applies)
- [ ] Privacy policy URL live; link it in Console + data-safety form
- [ ] No secrets in repo (`.env` untracked)
- [ ] GitHub repo renamed TuProxy → Proxyku (old URLs redirect automatically)
