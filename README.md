# MISTREE BHAI — GitHub Test Build

A testable preview of the MISTREE BHAI home-services marketplace. The existing React/Vite design is preserved, including customer and technician screens, a separate `/admin` route, and the install-app prompt.

> **TEST BUILD ONLY — NOT APPROVED FOR REAL CUSTOMERS.** The frontend still uses demo/local browser data for some workflows. The PostgreSQL API foundation is included, but not every screen is wired to it. Do not accept real bookings or store real customer information until the launch checklist is complete.

## Fastest way to test it

### Option A — Run on your computer

1. Install Node.js 22 LTS from <https://nodejs.org/>.
2. Extract this ZIP into a folder.
3. Open that folder in a terminal (Windows: open the folder, click the address bar, type `cmd`, and press Enter).
4. Run:

   ```bash
   npm install
   npm run dev
   ```

5. Open <http://localhost:3000>.
6. Open the separate admin route at <http://localhost:3000/admin>.

### Option B — Upload to GitHub and preview on Vercel

1. Sign in to <https://github.com/> and create a **new empty repository** named `mistreebhai`.
2. Extract this ZIP on your computer. Upload the **contents inside the extracted folder** to the repository root, not the ZIP file itself and not an extra enclosing folder.
3. Commit the files to the `main` branch.
4. Sign in to <https://vercel.com/> and choose **Add New → Project**.
5. Import the `mistreebhai` GitHub repository.
6. Keep the detected Vite settings: build command `npm run build`, output directory `dist`. Use Node.js 22.x in Project Settings if prompted.
7. Click **Deploy**. Wait for the deployment result, then open the provided URL and test the main page and `/admin`.

The included GitHub Actions workflow runs server syntax/configuration tests and attempts a production build on pushes and pull requests. A green workflow is a useful check, not a complete security or end-to-end test.

## API and managed database (optional for the UI preview)

The Vercel API entry point is `api/[...route].js`. Database schema is `server/schema.sql`. To enable API account/booking routes, create a managed PostgreSQL database (for example, on Neon), then set the environment variables in Vercel Project Settings using `.env.example` as a guide. Never upload a real `.env` file or secrets to GitHub. Apply `server/schema.sql` to the database before testing database-backed endpoints.

Required production variables include `DATABASE_URL`, `JWT_SECRET`, `CLIENT_ORIGIN`, and a temporary `ADMIN_BOOTSTRAP_KEY`. Use unique strong secrets. Create the initial admin through the documented one-time bootstrap endpoint and immediately remove `ADMIN_BOOTSTRAP_KEY` afterwards. Do not expose the bootstrap key in frontend code or commit it.

If no database is configured, the API health endpoint may report `database: "not-configured"` and database-backed routes will return HTTP 503. That does not prevent you from previewing the front-end design, but it does mean the backend is not operational.

## Quick checks

```bash
npm test
npm run build
```

`npm test` checks JavaScript syntax and a small set of configuration/schema invariants. A successful build only verifies compilation; it does not prove booking flows, role permissions, persistence, or production security.

## Before accepting real bookings

- [ ] Wire registration, login, booking, technician actions, and admin actions to the API.
- [ ] Test database persistence across reloads and devices.
- [ ] Verify every customer/technician/admin permission server-side, including IDOR tests.
- [ ] Add account verification, password reset, session revocation, and abuse monitoring.
- [ ] Test booking state transitions, cancellation rules, and cash collection reconciliation.
- [ ] Configure database backups, restore tests, logging, alerting, and privacy/terms pages.
- [ ] Run dependency/security scans and browser/device end-to-end tests.

This repository is a GitHub/Vercel **test candidate**, not a declaration of production readiness.
