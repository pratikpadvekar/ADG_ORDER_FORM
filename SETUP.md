# Setup on another Windows device

## What to install

- Git
- Node.js 18 or newer
- Access to the GitHub account/repository
- Access to the hosted Site project if you need to deploy changes

## Clone the repository

```powershell
git clone https://github.com/pratikpadvekar/ADG_ORDER_FORM.git
cd ADG_ORDER_FORM
```

## Validate the source

This project has no `.env` file and no npm dependencies are required for the build scripts.

```powershell
node --version
node scripts/build.mjs
node scripts/validate.mjs
```

Successful validation prints:

```text
Artifact is valid ESM and exports default.fetch
```

## Important environment and data information

The application needs the hosted runtime bindings:

- `DB`: Cloudflare D1 database
- `BUCKET`: private Cloudflare R2 bucket for order PDFs

The production seed values are runtime secrets, not GitHub files:

- `SEED_ADMIN_EMAIL`
- `SEED_ADMIN_PASSWORD`
- `SEED_SOCIAL_EMAIL`
- `SEED_SOCIAL_PASSWORD`

Do not create a real `.env` file in the public repository and do not commit passwords, tokens, database exports, or PDF files.

## Running the actual application

There is no local `npm start` command in this repository. The application runs as a hosted Worker/Site and uses the D1 and R2 bindings above. To use the existing live application on another device, open:

`https://agro-deluxe-order-form.tanishpadvekar9.chatgpt.site/orderform`

To publish code changes, the person deploying must have access to the associated Site project and must configure its runtime secrets and bindings. Cloning this GitHub repository alone does not copy the live D1 records, user accounts, sessions, or stored PDFs.

## Safe handoff checklist

1. Clone the repository.
2. Run the build and validation commands.
3. Give the new device the Site/deployment account access separately.
4. Configure runtime secrets in the Site environment settings.
5. Confirm the D1 database and R2 bucket bindings point to the existing production resources.
6. Test login, order creation, PDF retrieval, and admin access after deployment.
