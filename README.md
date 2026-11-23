# M Wireless Order Portal (Netlify)

## Files
- `public/index.html` - the portal page
- `netlify/functions/api.mjs` - shared storage (orders, IASR file, budgets, stores, submitted orders) and the order email
- `netlify.toml`, `package.json` - Netlify settings

## Deploy (Git or CLI, needed for the shared storage and email)
1. Push this folder to a GitHub repo, then in Netlify choose "Add new site > Import an existing project" and pick the repo. Leave the build command empty. Publish directory is `public` (already in netlify.toml).
   Or with the CLI: `npm install` then `npx netlify deploy --prod`.
2. Add these in Site settings > Environment variables:
   - `GMAIL_APP_PASSWORD` - a Google App Password for mwirelesssupportcentre@gmail.com (see below). Required for the Submit button.
   - `ADMIN_PASSWORD` - your own admin password. Optional `ADMIN_USER` (default `Telenor`). If you skip these, the defaults are `Telenor` / `orders`.
   - Optional: `MAIL_USER` and `MAIL_TO` (both default to mwirelesssupportcentre@gmail.com), `MAIL_TZ` (default `America/Chicago`).
3. Redeploy once after adding the variables.

## Google App Password
1. Sign in to mwirelesssupportcentre@gmail.com and turn on 2-Step Verification (Google Account > Security).
2. Go to Google Account > Security > 2-Step Verification > App passwords, create one (name it "Order Portal") and copy the 16 characters.
3. Paste it as `GMAIL_APP_PASSWORD` in Netlify. Do not put it in any file.

## Submit button
- At the bottom of the order table, an employee presses "Submit order" for the selected store.
- An email goes from mwirelesssupportcentre@gmail.com to mwirelesssupportcentre@gmail.com with the order table in the body and an Excel attachment in the same format as "Download Excel" (sheets: Order Report, Store Summary, All Items), filtered to that store.
- A store can submit again only after 15 seconds.

## Notes
- Anyone can open the site and enter orders. Only the signed-in admin can update the IASR file, edit budgets, clear orders, import codes and download Excel.
- The server checks the admin password for IASR and budget updates.
- If you only drag-and-drop `public/index.html`, the page opens but the shared storage and email function are missing.

## Preloaded
- The six stores in the 11-23-2026 IASR file (101 El Camino, 102 Salinas, 103 Tully Rd, 104 Senter, 105 Winchester, 106 San Pablo Ave) and that IASR file's items are built in, so the portal works straight after deploy. Budgets start at $0: set them under "Edit monthly budget".
- Duplicate store/SKU rows in the IASR file are loaded once. Max qty = Allocation minus On Order.
- Orders are emailed to mwirelesssupportcentre@gmail.com. The Gmail App Password must belong to the sending account (`MAIL_USER`, default mwirelesssupportcentre@gmail.com).

## Store setup (admin)
- Sign in, open "Store setup (store names and IASR names)". Each store has a Loc ID (auto-numbered), a store name, the IASR name, an optional Pool and a Manager tick.
- The IASR name must match the Location column of the IASR file (capitals, punctuation and extra spaces are ignored). Leave it blank to use the store name. Use `;` for more than one IASR name per store.
- You can paste a two-column list (store name, IASR name) straight from Excel, then press "Save stores".
- When the IASR file is uploaded, rows are matched to stores by IASR name. Names that match no store are listed after the upload. Add them in Store setup and press Save stores: no re-upload is needed.
- The first time stores are saved, the built-in sample IASR data is cleared, so upload the IASR file next.
- New stores start with a $0 budget. Set budgets under "Edit monthly budget".

## Order tracking
- Every time a store presses "Submit order", the order is also saved and appears in the "Order tracking" tab as "Awaiting confirmation".
- Admin: press "Confirm order placed" when you have placed it, then type the tracking number and press Save (several numbers can be separated by commas). Adding a tracking number also confirms the order. "Undo confirm" works until a tracking number exists. "Delete" removes a record.
- Everyone can see the status and tracking number (read-only). Each store also sees the status of its last order under the Submit button.
