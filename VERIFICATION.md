# Neon edition verification

- Strict TypeScript checks cover src, api, server, scripts; Vite production build passed.
- 47 tests passed: 33 existing calculation/schema tests, 4 IRR tests, 10 API integration tests using embedded Postgres (PGlite).
- API tests cover anonymous private GET/PATCH/PUT/DELETE denial, validated public submissions, plan/date checks, admin-only product changes, safe links, CSRF, forged/expired/logged-out sessions, password rotation invalidating old sessions, and form rate limits.
- Node runtime import of API module passed after removing browser-only import.meta.env from shared schema module.
- Browser: /irr.html direct navigation rendered and calculated sample result 4.6455% annually.
- No live Neon connection, Vercel deployment, GitHub push, production database migration, or production admin account creation performed.
- No raw initial password or DATABASE_URL bundled. Firebase SDK removed; private API endpoints enforce server sessions.
- Admin page listings show at most 200 private records; public collections at most 1,000. Public data refresh polls every 30 seconds and after edits/focus.

## Banner update 2026-11-05
- Production build passed. 49 tests passed, including authenticated banner persistence, anonymous denial, invalid image URI rejection, oversized body rejection, and legacy site-record compatibility.
- Browser visual checks: desktop and embedded 375px/768px viewports; all supplied Thai strings rendered as text. Generated banner integrates the attached advisor portrait; original portrait also bundled.
- Live Neon/Vercel deployment and admin file selection on physical iPhone remain unverified. API integration tests use embedded PostgreSQL.
