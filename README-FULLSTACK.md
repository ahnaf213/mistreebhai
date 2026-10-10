# Mistree Bhai — full-stack upgrade

## What changed
- Separate admin URL: open `/admin` (or `/admin/`) to enter the admin area; the role-switching demo control is hidden there.
- Express JSON API in `server/server.js` with persistent file-backed CRUD endpoints for customers, mistris, services, areas, orders, notifications, complaints and payouts.
- Progressive Web App setup: manifest, service worker, app icon and an install prompt on the landing page. Chrome/Edge show a native install action when eligible; iOS explains Add to Home Screen.
- Project/package renamed to `mistreebhai`.

## Run locally
Requires Node.js 20.19+ (Vite 8 requirement).
1. Extract the ZIP.
2. In this folder, run `npm install`.
3. Terminal 1: `npm run server` (API on http://localhost:4000).
4. Terminal 2: `npm run dev` (website on http://localhost:3000).
5. Open http://localhost:3000 for customers/providers, or http://localhost:3000/admin for admin.

The API stores records in `server/data.json`, created automatically. Back up this file before moving the project. API health check: `http://localhost:4000/api/health`.

## API routes
For each collection in `customers`, `mistris`, `services`, `areas`, `orders`, `notifications`, `complaints`, `payouts`:
- `GET /api/<collection>` — list records
- `GET /api/<collection>/<id>` — fetch one
- `POST /api/<collection>` — create
- `PUT /api/<collection>/<id>` — update
- `DELETE /api/<collection>/<id>` — delete

## Important production checklist
This upgrade gives the project a persistent backend API foundation, but the existing demo UI still has localStorage-based demo state and is not yet fully wired to every API endpoint. Before taking real customers or payments, connect each UI action to the API, add a real database (PostgreSQL is recommended), authentication and server-enforced admin authorization, validation/rate limits, secure file uploads, HTTPS, backups, and deployment configuration. The `/admin` route is separated as a page but is not secure authentication by itself; do not expose it publicly until admin login and server-side authorization are implemented. Never store payment credentials or identity documents in the JSON file.
