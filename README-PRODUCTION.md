# MISTREE BHAI — Vercel + managed PostgreSQL deployment candidate

## Release status: NOT CLEARED FOR PUBLIC LAUNCH

This package contains the existing Vite/React UI, a Vercel catch-all Node function for the Express API, a PostgreSQL schema, and an authentication/API foundation. I have not been able to install dependencies or run the complete frontend build in this environment. Do not treat this package as a verified production release.

The key remaining blocker is that the UI's `AppContext` and most customer, technician, and admin screens still use demo records and browser `localStorage`. In particular, registration, booking, provider acceptance, profile edits, verification, admin edits, notifications, complaints and earnings are not all wired end-to-end to the API. The generic JSONB records are an interim data model, not a substitute for reviewed marketplace-specific tables and business rules. Payment method is intended to be cash after service; collection confirmation still needs workflow review.

## Architecture

- Frontend: Vite + React, deployed to Vercel static output (`dist`).
- API: Express app exported as a Vercel Node.js function at `api/[...route].js`.
- Database: managed PostgreSQL, e.g. Neon. Keep the database URL server-only.
- Admin: `/admin` is a client route; access must be confirmed by the backend-authenticated session. A URL is never authorization.
- Payments: cash after service; do not collect card or wallet details in this version.

## Beginner-friendly setup

### 1. Prepare accounts

1. Create a Vercel account and import this project from a private GitHub repository.
2. Create a managed PostgreSQL database (for example, Neon). Create a separate production database; do not use the development database for live customer records.
3. Copy the provider's PostgreSQL connection string. Use the provider's pooled URL if recommended for serverless applications.

### 2. Configure environment variables in Vercel

Open **Vercel → Project → Settings → Environment Variables** and add these for Production (and Preview only with separate test credentials):

- `DATABASE_URL`: the full managed PostgreSQL connection string.
- `DATABASE_SSL`: `true` when required by your database provider.
- `NODE_ENV`: `production`.
- `CLIENT_ORIGIN`: the exact deployed origin, e.g. `https://your-project.vercel.app` (no trailing slash).
- `JWT_SECRET`: a unique random value of at least 32 characters. Never reuse the example value.
- `ADMIN_BOOTSTRAP_KEY`: a unique, temporary random value used only to create the first admin.
- `TRUST_PROXY`: `true` only for the Vercel deployment so rate limiting can respect the trusted platform proxy. Re-check this against current Vercel runtime behavior before launch.

Do not add database credentials or JWT secrets with a `VITE_` prefix. Variables with `VITE_` are shipped to browsers.

### 3. Install and build locally

Install Node.js 22 LTS, extract the project, then open a terminal in its root folder:

```bash
npm install
npm run check:server
npm run build
```

The full build must succeed on your machine and in Vercel before proceeding. This environment could not complete `npm install`, so no successful build is claimed here.

### 4. Create the database schema

The schema setup script is designed to run from a trusted local terminal against your managed database:

```bash
npm run db:setup
```

For this command, temporarily set `DATABASE_URL` and `DATABASE_SSL` in a local `.env` file. Do not commit `.env`. Check the provider's connection-string and SSL requirements first. Confirm the schema exists before testing the API.

### 5. Deploy to Vercel

1. Push the project to a private GitHub repository.
2. In Vercel, choose **Add New → Project** and import that repository.
3. Keep the Vite framework preset, build command `npm run build`, and output directory `dist` (these are also in `vercel.json`).
4. Add the environment variables from step 2.
5. Deploy to a Preview environment first. Do not connect real customer data yet.
6. Test `https://YOUR-DEPLOYMENT/api/health`. It should return `ok: true` and `database: "connected"`.

### 6. Create the first admin (temporary bootstrap)

Call the bootstrap endpoint once, replacing the URL, setup key, email, and password. Use a unique password of at least 14 characters:

```bash
curl -X POST "https://YOUR-DEPLOYMENT/api/setup/bootstrap-admin" \
  -H "Content-Type: application/json" \
  -H "X-Setup-Key: YOUR_ONE_TIME_SETUP_KEY" \
  -d '{"name":"Marketplace Admin","email":"admin@yourdomain.com","password":"use-a-unique-long-password"}'
```

After success, immediately remove `ADMIN_BOOTSTRAP_KEY` from Vercel environment variables and redeploy. Confirm that `/admin` does not open admin screens without a valid admin session. Do not create admin accounts through public registration.

### 7. Mandatory pre-launch testing

- Run `npm run build` successfully on a clean checkout and on Vercel.
- Verify database connection and database backup/restore.
- Test registration, login, logout, duplicate accounts, suspended users, wrong-role access and expired tokens.
- Verify a customer cannot read or change another customer's bookings.
- Verify a technician can only access assigned bookings and only make valid status transitions.
- Verify all admin APIs reject non-admin users even when called directly, not only through hidden UI controls.
- Verify booking prices, technician assignment, status transitions, cash collection, cancellation, refunds/complaints and earnings are calculated on the server.
- Remove demo data, role-switcher shortcuts, sample credentials and production localStorage persistence for business records.
- Configure monitoring, error alerts, database backups and restore drills, privacy policy, terms, cancellation/refund rules, customer support details and a data retention process.
- Perform a security review before accepting NID documents or bank/mobile-wallet details.

## API currently present

- `GET /api/health`
- `POST /api/auth/register` (customer or technician account; technicians start pending)
- `POST /api/auth/login`
- `GET /api/auth/me`
- Collection endpoints for interim JSONB records
- `POST /api/setup/bootstrap-admin` (one-time setup key)

These endpoints are a foundation, not proof that all marketplace workflows are implemented. Do not advertise the site as production-ready until the UI is migrated from demo storage and all mandatory tests pass.
