# MISTREE BHAI — GitHub Pages public preview

This repository configuration deploys a **static UI demo** to GitHub Pages so you can share a public URL and test the interface on your phone. GitHub Pages does not run the Express API or PostgreSQL. Registration, bookings, payments, notifications, and admin data are not production-connected in this preview. Do not enter real customer data or treat the demo admin page as secure.

## Upload and publish
1. Create a GitHub repository named `mistreebhai` (or another name).
2. Extract the ZIP and upload the *contents* of the folder to the repository root. `package.json` must be at the root.
3. In GitHub, open **Settings → Pages**. Under **Build and deployment → Source**, select **GitHub Actions**.
4. Open the **Actions** tab. The workflow named **Deploy static demo to GitHub Pages** runs after you push to the `main` branch. Wait for it to finish successfully.
5. Open **Settings → Pages** to find the public website URL. For a repository named `mistreebhai`, it will usually be `https://YOUR-GITHUB-USERNAME.github.io/mistreebhai/`.
6. The main preview is that URL. The admin UI preview is that URL plus `admin/` (e.g. `https://YOUR-GITHUB-USERNAME.github.io/mistreebhai/admin/`). The admin screen here is **demo-only and not protected by production authentication**; it contains no real server data.

## Local preview
Install Node.js 22, then run `npm install` and `npm run dev`. Open the URL shown in the terminal.

## Before real launch
Deploy the API to a serverless/backend host and connect PostgreSQL; then implement and test real login, role permissions, persistent bookings, technician verification, account recovery, backups, and monitoring. GitHub Pages alone cannot host the full-stack application.
