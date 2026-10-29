# Agro Deluxe Growers order portal

This project is a deployable customer ordering portal and admin dashboard for Agro Deluxe Growers. It uses a Cloudflare Worker for the API and UI, Cloudflare D1 for persistent relational records, and Cloudflare R2 for private order PDFs. The Site project is already created and deployed privately at:

`https://agro-deluxe-order-form.balmylark16.chatgpt.site/orderform`

## What is included

- One login for customers and administrators, with PBKDF2 password hashing, secure HttpOnly sessions, expiry, same-origin checks, and login throttling.
- Customer isolation through server-side party and outlet checks.
- Dynamic product availability per outlet, with server-side revalidation at submission time.
- Product Master is a dedicated product list where an administrator adds a product name and selects KG, PACKET, or PICESS.
- Outlet Availability provides the separate TRUE/FALSE matrix for each outlet and product.
- User Access lets an administrator create customer logins, assign a party/location, and map permitted outlets.
- Location Master lets an administrator add, edit, and delete company names and outlet locations; new outlets appear in Outlet Availability automatically.
- Order dates validated against Asia/Kolkata business time.
- Unique `ORD-YYYYMMDD-XXXXXX` IDs and idempotency keys to prevent double submissions.
- D1 tables for parties, users, outlets, products, permissions, orders, items, sessions, audit logs, and login attempts.
- PDF generation and private R2 storage with authenticated admin access.
- Admin dashboard with Today, Yesterday, All Orders, selected-date, party, outlet, and Order ID filtering.
- CSV exports, including the pivoted report named `Order In Details` with one row per order and product quantity columns.

## Initial placeholder accounts

The first request seeds these placeholders when the D1 tables are empty:

- Customer: `social@example.invalid`
- Admin: `admin@example.invalid`
- Temporary password for both: `ChangeMe!123`

Set real values through Site runtime secrets before production use. The Worker reads `SEED_ADMIN_EMAIL`, `SEED_ADMIN_PASSWORD`, `SEED_SOCIAL_EMAIL`, and `SEED_SOCIAL_PASSWORD`. After a user row exists, changing those variables does not overwrite its password; use the admin account-management flow or a controlled database migration for password changes.

## Local files

- `worker/index.js` contains the Worker, API routes, UI, PDF builder, and seed bootstrap.
- `migrations/0001_init.sql` contains the persistent D1 schema.
- `.openai/hosting.json` binds the project to Site, D1 (`DB`), and R2 (`BUCKET`).
- `scripts/build.mjs` and `scripts/validate.mjs` produce and validate `dist/`.

## Deployment

The source is pushed to the Site repository and the current production deployment is private. On Windows, build with:

```text
node scripts/build.mjs
node scripts/validate.mjs
```

The deployment archive must contain `dist/.openai/hosting.json` and `dist/server/index.js`. Site publishes the current Git commit and applies the D1 binding. Keep migrations append-only; do not rewrite an applied migration.

Set runtime secrets in the Site environment settings. Keep the default placeholders only for a controlled test environment. Back up D1 before schema changes and retain R2 PDFs under `orders/<order-id>.pdf`; the database stores the PDF key and historical item snapshots.

## Custom domain

The requested hostname has been registered with Site as `www.agrodg.com`. Add the returned DNS records at the domain provider:

- CNAME `www` → `custom-domains.chatgpt.site.`
- TXT `_openai-site-verification.www` → `openai-site-verification=7WbBziIyqZFXv2c0pjxk2kuPtxzi8brQqfpj2mfgrEU`
- TXT `_cf-custom-hostname.www` → `2bc1b696-73ff-4446-a4a6-e3a04321e415`

Once DNS validation and SSL finish, the application will serve at `https://www.agrodg.com/orderform`; the Worker already handles `/orderform` and `/orderform/`.

## Testing checklist

The published portal was checked through the Site URL for the unauthenticated login screen, placeholder SOCIAL login, assigned outlet visibility, dynamic product loading, admin login, default Today view, and responsive single-page routing. Before using real accounts, verify invalid passwords, disabled accounts, past dates, zero/negative quantities, duplicate Idempotency-Key submissions, PDF retrieval, R2 failure retry, customer-to-admin isolation, and custom-domain HTTPS after DNS propagation.
