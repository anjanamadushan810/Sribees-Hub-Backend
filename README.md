# SriBees Hub — Backend

Server side of SriBees Hub, the SriBees app store (https://hub.sribees.com).
Sister repos: `Sribees-Hub-Mobile` (Flutter Hub app) and `Sribees-Hub-Admin` (admin web, coming).

## What runs today
A static host on the Contabo box: nginx (read-only container `sribees-hub-web`) serves
- `/` — landing page (en/si/ta) with the Hub APK download and QR code
- `/catalog.json` — the app catalog the Hub app reads
- `/sribees-hub.apk` and `/apk/*.apk` — signed APKs (not in git)

The shared Caddy from SribeesExpress terminates TLS for `hub.sribees.com` and proxies to
`sribees-hub-web:80` on the `sribees_default` network.

## Layout
| path | purpose |
|---|---|
| `docker-compose.yml` | nginx service, joins `sribees_default` |
| `nginx.conf` | caching rules, explicit APK MIME type |
| `www/index.html` | landing page |
| `www/catalog.json` | catalog (versions, sha256, signer, URLs) |
| `PLAN.md` | product plan, phases and done-when checks |

## Deploy (manual for now)
The stack lives in `~/sribees-hub` on the server. Upload with tar over ssh:
APKs first, `catalog.json` last, then verify with `curl -s https://hub.sribees.com/<file> | sha256sum`.
APKs and signing keys are never committed.

## Next
FastAPI service + own Postgres for public/private apps, invited emails and Google sign-in
(see PLAN.md "Access model").
