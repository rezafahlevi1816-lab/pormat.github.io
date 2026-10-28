# Buku Kas Saku

A responsive Indonesian cashbook app built with HTML, CSS, and vanilla JavaScript. Runs as a static site, including on GitHub Pages. No application dependencies or build step.

## Run locally

```sh
cd /workspace/pormat.github.io
python3 -m http.server 8000 --bind 0.0.0.0
```

Open port 8000 in your development environment, or `http://localhost:8000` on your own machine. Use HTTPS when hosting remotely. Web Crypto and service workers require HTTPS or localhost; opening `index.html` directly is not supported.

## Pages and behavior

- **Login:** email/password, password visibility, optional remembered session, demo account shortcuts, and logout.
- **Dashboard:** selected-month income, expenses, and net balance; monthly/7-day cash-flow charts; spending by category; latest transactions.
- **Transactions:** create, read, edit, and delete income/expenses; category/date/notes; search; type and category filters; month selection; pagination; filtered CSV export.
- **Settings:** edit your profile and password. Admins can create/edit/delete users, reset passwords, change roles, and deactivate accounts. The app protects the last active Admin and prevents self-deletion/deactivation.
- **Mobile app:** bottom navigation, touch controls, modal sheets, installable web manifest, and offline support after the first successful online load. Add to Home Screen through your mobile browser. No native Android/iOS binary is included.

Each browser starts with sample transactions in the current month and the previous month. Dates use Asia/Jakarta; currency uses Indonesian rupiah. The dashboard balance is **income minus expenses in the selected month**, without an opening balance or bank integration.

## Demo accounts

All three initial accounts use **`saku12345`**.

| Role | Email | Permissions |
| --- | --- | --- |
| Admin | `admin@saku.app` | View/export, transaction CRUD, user/role management |
| Editor | `editor@saku.app` | View/export, transaction CRUD |
| Viewer | `viewer@saku.app` | View/export only |

Account shortcuts fill the initial credentials; press **Masuk ke Saku** to sign in. If you change the email or password, use the updated credentials. Admins can reset other users' passwords; no invitation or recovery emails are sent.

## Storage and authentication scope

This is a functional **local demo**, not a production authentication system. It has no backend, database server, remote synchronization, or real shared accounts across devices. Users in the same browser share one cashbook. Do not enter sensitive or real financial data.

- Transactions and users persist in `localStorage` under `buku-kas-saku.data.v1`.
- Passwords are salted and derived with PBKDF2-SHA-256 (100,000 iterations), rather than stored as plaintext.
- Session state uses `sessionStorage`, or `localStorage` if **Ingat saya** is checked.
- Client-side role checks are for demonstration and can be bypassed by someone controlling the browser. Production requires server-side authentication, sessions, authorization, validation, and durable database storage.
- Deleting browser/site data removes accounts and transactions. CSV exports contain the currently filtered transaction rows, not a restorable backup of users or settings. Save exports before clearing site data.
- Local-storage write errors do not discard the existing in-memory data. Unreadable stored data is retained and reported instead of silently reset.
- The service worker caches only public app assets. Keep the cache version in `service-worker.js` updated when changing the offline asset set. Online requests prefer the latest deployed assets.

## Validation

JavaScript syntax checks require Node.js:

```sh
node --check app.js
node --check service-worker.js
```

The browser suite needs Python's `playwright` package and Chromium. These are already installed in the prepared cloud environment. On another machine, install Playwright in your development virtual environment, then install Chromium through Playwright or set `CHROMIUM_PATH` to an existing executable.

With the local server running:

```sh
python3 tests/smoke.py
```

Optional variables: `SAKU_BASE_URL` (default `http://127.0.0.1:8000`), `CHROMIUM_PATH`, and `SAKU_ARTIFACTS` (default `/tmp/saku-artifacts`). Each test uses isolated browser storage. The suite covers login, sessions, transaction CRUD and balance calculations, filters, pagination, exports, injection-safe rendering, user CRUD, role restrictions, profile/password changes, storage failures, responsive layouts, keyboard dialogs, GitHub Pages subdirectories, and offline use.

## Deploy to GitHub Pages

In the repository's **Settings → Pages**, choose **Deploy from a branch**, then select `main` and `/ (root)` after the files have been committed and pushed. All application paths are relative, so both project Pages URLs and custom domains work. This repository has not been pushed or published by this implementation.

