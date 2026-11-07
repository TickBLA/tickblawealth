# tickblawealth — Neon edition

React + Vite + TypeScript + Tailwind frontend, Vercel Node Functions, Neon Postgres. This edition replaces Firebase Auth, Firestore, and App Check in the source. Existing live Firebase data is not automatically migrated or deleted.

Read **DEPLOY-NEON-TH.md** before deployment. Files under `api/` must deploy as Vercel Functions; static `dist/` upload alone is insufficient.

## Features

- Existing responsive Thai advisor website, products, leads, financial surveys and site content editor.
- `/irr.html` and `/irr`: annual equally spaced cash flow IRR; detects ambiguous sign changes instead of reporting an arbitrary result.
- `/admin/links`: links to existing routes or HTTPS sites; optional plain-text pages at `/slug.html`. Adding a link alone does not implement a new calculator.
- `/admin/account`: change password, revoke every session, sign in again.
- Opaque 256-bit sessions stored as SHA-256 hashes; passwords salted with scrypt. HttpOnly/SameSite=Strict cookies, Secure in production, 8-hour expiry. Server-side origin checks, schema validation, parameterized SQL and persisted request limits.
- Private collections can be submitted publicly but queried/updated/deleted only after server session verification; updates restricted to status. Browser receives no database credentials.
- Polling every 30 seconds plus refresh after edits/focus, with abort/unsubscribe cleanup. This is not Firestore realtime streaming.

## Local commands

Node >=22.12, `npm ci`, `npm test`, `npm run build`.

`npm run dev` is frontend-only. Use `VITE_DEMO_MODE=true` for a frontend preview. Use Vercel CLI `vercel dev` for the full API after configuring local environment values; do not expose production customer data in a preview database.

API/server/scripts are included in the strict TypeScript build. Automated API tests execute queries with an embedded PostgreSQL engine (PGlite); Neon network connections and actual Vercel deployment require separate verification.

## Data

`content_records` stores validated JSON documents with kinds `insurance_products`, `leads`, `financial_surveys`, `site_content`, `site_links`. `app_admins`, `app_sessions`, and `app_rate_limits` are server-only. RLS is enabled with no public policies, and PUBLIC grants are revoked. The Vercel database owner connection is trusted and must remain server-only; API authorization is the access boundary.

Public content is capped at 1,000 records, private admin listings at 200 newest updated records. `enabled=false` hides navigation/page display; it is not a confidential draft facility. Plain-text page contents are public data.

Run `npm run data:purge` for a dry run, then `npm run data:purge -- --apply` to delete private records older than 90 days and expired session/rate-limit records. Schedule this operationally to meet the retention policy; no automatic cron is enabled.

The artwork comes from the supplied design reference and is displayed via SVG viewports. Sample reviews remain labelled. Replace them and supply verified license/profile details before treating them as real customer testimonials.
