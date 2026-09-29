# Wuling Vehicle Control

> A third-party Wuling connected-car client for **Android 16**, with home-screen widgets, energy statistics, and in-app auto-update.

[![Latest version](https://img.shields.io/badge/dynamic/json?url=https%3A%2F%2Fedgeone.gh-proxy.org%2Fhttps%3A%2F%2Fraw.githubusercontent.com%2Fdaiyuxiang520%2Fgeren-update%2Fmain%2Fupdate.json&query=%24.versionName&label=Latest&color=blue)](https://github.com/daiyuxiang520/geren-update/releases/latest)
[![Android](https://img.shields.io/badge/Android-16%20(targetSdk%2036)-green)]()
[![Package](https://img.shields.io/badge/package-com.wuling.app.repack-orange)]()

**Wuling Vehicle Control** is a third-party connected-car client for Android 16 (API 36) that talks to the official API directly. It uses an independent package name `com.wuling.app.repack` and **runs on its own, with no official app required**. It fits users whose official app is broken on Android 16, or who want handier vehicle control and energy statistics. Ad-free, and credentials are stored on-device only.

## Contents

- [Features](#features)
- [Download](#download)
- [Auto-update](#auto-update)
- [Build](#build)
- [Credits](#credits)
- [Version History](#version-history)
- [Disclaimer](#disclaimer)

## Features

### Control & Alerts

- **Vehicle status**: range, battery, fuel, and door / window / trunk state in real time
- **Vehicle control**: remote lock / unlock, windows, find-my-car, A/C; sensitive actions such as unlock, window-open, and start require a second confirmation
- **Walk-away alerts**: window left open / door unlocked / trunk open, with one-tap lock or window-close from the notification
- **Charging alerts**: charge started / fully charged / interrupted / low battery (threshold configurable)
- **Bluetooth digital key**: BLE hands-free connection with proximity auto-unlock

### Location, Energy & Charging

- **Location**: vehicle locating and parking records, Chinese address reverse-geocoding, local weather
- **Energy statistics**: daily / monthly / yearly energy use, trend chart plus energy-mix pie chart
- **Charging power**: live charging power (kW) shown on the home battery card, the detail-page battery & charging section, and the home-screen widget; hidden when not charging
- **Scheduled charging**: view, set, edit, and cancel recurring charging windows and charge limits (depends on the vehicle-side API; not supported on some models)

### Widgets, Updates & Security

- **Home-screen widgets**: a 2×3 vehicle-status card (refreshes independently) and a 4×1 quick-control bar (lock / close windows / find car)
- **Auto-update**: checks for new versions on launch, picks the fastest of 15 GitHub mirrors, falls back on failure, verifies by MD5
- **Persistent sign-in**: credentials are encrypted by AndroidKeyStore and stored on-device; on token expiry or being kicked out, the app silently signs back in, so it stays logged in across restarts and process kills
- **Debug logs**: API payloads (redacted) persisted to disk, with filtering, search, and export

## Download

Prefer the [Releases](https://github.com/daiyuxiang520/geren-update/releases/latest) page. Download `wuling-assistant.apk` and install over any previous version (same package name, safe to overwrite).

For users in mainland China, [Gitee](https://gitee.com/daiyuxiang520/geren-update) mirrors this repository with the APK hosted as a release asset, reachable without a proxy:

```
https://gitee.com/daiyuxiang520/geren-update/releases/download/v145/wuling-assistant.apk
```

Replace the trailing `v145` with any version number to download that release.

## Auto-update

The app checks for updates automatically on launch (also available under **Me → Check for updates**). This repository is the authoritative source; its [Gitee mirror](https://gitee.com/daiyuxiang520/geren-update) carries an identical `update.json` (same version number and the same APK URL), so mirroring never causes distribution drift.

- **Check**: queries all sources concurrently and picks the highest version; on a tie, the first to respond wins
- **Download**: both manifests point to the Gitee release asset, so the APK is always fetched directly from Gitee; the fastest mirror is chosen first, with automatic fallback on failure, followed by MD5 verification and the installer
- **Fallback**: if Gitee is unavailable, version info is still read from a GitHub mirror; a full APK is hosted on both GitHub Releases and the Gitee release

The mirror list is derived from [XIU2/UserScript](https://github.com/XIU2/UserScript) and filtered by local testing.

## Build

The repository ships no secrets; sensitive configuration is injected via `local.properties` or environment variables.

```bash
# 1. Credentials
cp local.properties.example local.properties   # fill per comments (some features need it; the build works without it)

# 2. Signing (optional; release falls back to the debug key when unset)
export WULING_KEYSTORE_PATH=/path/to/your.keystore
export WULING_KEYSTORE_PASSWORD=your_store_password
export WULING_KEY_ALIAS=your_key_alias
export WULING_KEY_PASSWORD=your_key_password

# 3. Build (requires JDK 17 + Android SDK 36)
./gradlew assembleRelease                       # output in app/build/outputs/apk/release/
./build-android16.sh                            # or build with the bundled Docker image
```

## Credits

| Project | Contribution |
|---|---|
| [hasscc/wuling](https://github.com/hasscc/wuling) (MIT) | Source of the vehicle-control protocol and API approach; this project is an independent native Android implementation |
| [XIU2/UserScript](https://github.com/XIU2/UserScript) (GPL-3.0) | The public GitHub mirror list used by auto-update |

Other dependencies: Jetpack Compose, Kotlin Coroutines, OkHttp, Gson, Hilt, Coil, DataStore, Security Crypto, and Umeng U-App.

## Version History

See the [Chinese README](./README.md#版本历史) for the full, detailed changelog of every release. Recent highlights:

| Version | Summary |
|---|---|
| **v145** (4.9.1) | Hotfix: feedback submission failed 100% of the time in v144 because OkHttp sends no `User-Agent` by default, and Web3Forms rejects non-browser requests with HTTP 403. Fixed by setting a browser `User-Agent`; server error text is no longer shown to users |
| **v144** (4.9.1) | Feedback now submits in-app via a direct POST, with no system chooser; email is kept only as a fallback. Logs are inlined in the message body |
| **v143** (4.9.0) | Added "Feedback" (feature request / bug report) with logs and crash context attached |
| **v139** (4.8.2) | NFC tags can bind a single action; fixed charging power misread in the hundreds of kW; added flash / horn controls |
| **v133** (4.6.0) | Large-screen adaptive layout; R8 fully enabled, APK 14.57 MB → 6.5 MB |

## Disclaimer

- This is a third-party client for **personal learning and research**, **not an official Wuling release**, and is not affiliated with SAIC-GM-Wuling.
- Vehicle data is fetched through the official API. To support persistent sign-in and auto re-login, credentials (phone number, password) are **encrypted with AndroidKeyStore and stored only on the device**, never uploaded to any third party; vehicle data is likewise never uploaded. Encrypted credentials work only on the current device and are invalidated when app data is cleared or the app is uninstalled. The bundled Umeng analytics collects only anonymous device-level data (disabled by leaving `wuling.umeng.appkey` empty at build time).
- Please comply with the relevant terms of service and **use at your own risk**; use only on vehicles you own. If this infringes any rights, contact us for removal.

## Links

- 📦 [Releases](https://github.com/daiyuxiang520/geren-update/releases)
- 📄 [update.json](./update.json)
- 🇨🇳 [中文说明](./README.md)

---

<sub>Last updated: 2026-09-29</sub>
