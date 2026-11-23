# SriBees Hub — PLAN

Owner interview: 2026-11-20 (Sinhala MCQ, rounds 1-2).
Playbook: `.claude/skills/sribees-app-store-architect/SKILL.md`.

## Vision (owner's words, summarised)
- Over 5 years SriBees becomes a **group of many companies** across sectors.
  The goal is for SriBees Lanka (Pvt) Ltd to be the **biggest company in Sri Lanka**.
- **SriBees Hub** is the SriBees app store: the one place where staff,
  customers and businesses install and update every SriBees app.
- Services that will come through the Hub over time:
  online shop + delivery, POS + stock, Finance/accounting, an AI assistant
  for each business, ads (inside the Hub plus Facebook/TikTok ads run for businesses),
  agro (direct from farmers + farmer services), mobile phone sales/repair shops,
  and other apps.
- Audience: everyone (staff + customers + businesses), but build in phases.
- **The Hub app itself is free** (owner 2026-11-20). How businesses pay for
  the services inside it is still undecided (ask before Phase 3).
- Domain: under **sribees.com** (default `hub.sribees.com`).

## Phase 1 — Hub for existing apps (FIRST)
Apps: customer app, Partner, Price, New Orders, Supply, AI Tool (+ Finance when ready).

Decisions confirmed by owner 2026-11-20 (customer app on Play = "for now"):
- Android: Hub = own installer app downloaded from a SriBees website page with a QR code.
  The Hub app is not on Play (Play does not allow app stores).
- Customer app stays on Play. In the Hub it is shown with a "Get on Play"
  button (no APK) to avoid signing-key and Play policy conflicts.
- Staff apps (Price, New Orders, Supply, AI Tool): installed and updated by the Hub
  (silent update where Android allows it).
- iPhone: Hub is a list with App Store links (Apple does not allow sideloading in LK).
- Register SriBees for Google Android developer verification before 2027.

### Done when (pass/fail)
1. [ ] On real Android 10, 12, 14 and 15 phones, the Hub installs New Orders from zero.
2. [ ] Publishing a new New Orders version updates it on those phones from the Hub
       (silent on 12+ where allowed); the version shown in the Hub == APK versionName == pubspec.
3. [ ] Tampered APK (bad sha256 or wrong signing cert) is refused on device.
4. [ ] Staff see only apps their role allows; customers see public apps only.
5. [ ] Admin page shows, for each device, the installed version of each app.
6. [ ] CI uploads a new build to the Hub catalog without manual copying.
7. [ ] Hub text in en/si/ta.

### Status (2026-11-20)
- `hub_app/` Flutter app (package `com.sribees.hub`, 0.1.0+1): catalog fetch, en/si/ta,
  native PackageInstaller (silent update API 31+, update ownership API 34+),
  sha256 + package + versionCode + signer checks, "installed from another source" message,
  store_link entries (customer app -> Play; iPhone -> App Store).
- `flutter analyze` clean, 3 unit tests pass, debug APK builds locally
  (`kotlin.incremental=false` needed because pub cache C: and project D: are on different drives).
- Hub release key: `secrets.out/hub-release.jks` + `key.properties` (git-ignored), backup in
  `D:\sribees online github\Sribees-Firebase-Backup\android-signing\sribees-hub\`.
  Signer SHA-256 45d3bbbb...2f9cb8. Release APK 0.1.0+1 (targetSdk 36, minSdk 24).
- LIVE 2026-11-20: https://hub.sribees.com (landing page en/si/ta, /sribees-hub.apk,
  /catalog.json, /apk/). DNS A hub -> 95.111.235.182 (Vercel, team sribees-digitel).
  Express main b5c8c40 adds the Caddy block (CD green, Caddy restarted, neighbours 200).
  Server stack: `~/sribees-hub` on Contabo (deploy cannot write /opt; no sudo), container
  `sribees-hub-web` (nginx, read-only) on sribees_default. Update files with tar over ssh.
- Catalog now: customer app (Play link) + New Orders 0.7.0+8 (staff). 0.8.0+9 APK is already
  uploaded as /apk/sribees-new-orders-9.apk for the update test.
- Owner test (phone has no New Orders): install Hub -> install New Orders 0.7.0 -> Claude
  switches catalog to 9 -> owner taps Update (should be silent on Android 12+).
- 2026-11-21: Hub 0.1.1+2 LIVE (sha256 1e50933e...bc6f0): fix = installed apps' signing
  certs are now read (the "installed from another source" pre-check was dead before);
  Hub lists itself in the catalog (row shows only when a Hub update waits) -> future Hub
  versions reach phones through the Hub; own launcher icon (tool/make_icons.py).
  7 unit tests pass, analyze clean.
- Local git only: branch `development` (no GitHub repo yet — ask the owner first).
- Switch to the update test (after the owner says New Orders 0.7.0 installed): in
  catalog.json set New Orders version_code 9, version_name "0.8.0",
  apk_url https://hub.sribees.com/apk/sribees-new-orders-9.apk,
  sha256 6e044319a9ef88c991d3bcbc873eeaad31d7a181d26d665dff87f177f700fd01 (same cert), upload.
- Catalog is public: anyone can see/download listed staff APKs (they still need a staff login).
- NOT yet: role login, admin page, CI upload, per-app icons in the list, GitHub repo.

### Access model (owner 2026-11-21) — replaces "staff login / role filtering"
- No staff login. Admin marks each app **public** (everyone with the Hub) or **private**.
- Private apps: admin adds email addresses; only those people see and download them.
- The invited person proves their email once in the Hub with **Google sign-in** (so invite Gmail
  addresses). No passwords.
- The Hub sends an **invitation email** from support@sribeesonline.com with the Hub link
  (needs an email-sending service set up first; the Partner password reset needs the same).
- Private APKs are no longer plain public files: the backend gives a short-lived download
  link only to invited, signed-in users.
- Shape: hub backend (FastAPI + own DB on Contabo), admin web app, mobile Hub app.
  Owner agrees with 3 parts but said "not yet" to creating the 3 GitHub repos; ask again before
  creating them.
- Done-when item 4 becomes: a private app is visible/downloadable only for an invited Google
  account; a public app is visible to everyone.

## Phase 2 — Business onboarding
Businesses register in the Hub, choose services, and their staff install those apps.

## Phase 3 — New sector apps + billing
Agro, mobile shops, ads, per-business AI. Billing model is decided with the owner first.

## Open questions for the owner
- Billing model for business services (the Hub itself is free).
