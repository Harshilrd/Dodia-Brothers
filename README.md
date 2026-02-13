# Metal Cutting & Straightening – Web App

Single-page web app: select or add clients, enter original plate size, add multiple cutting plate sizes, and save jobs. Data can be stored in the browser (IndexedDB) or in a shared cloud database (Supabase) so multiple devices see the same data.

## Where is the HTML file?

**Path:** `metal-cutting-app/index.html`

## How to open

- **On your computer:** Double-click `index.html` or open it in your browser.
- **On your phone:** Host this folder on the web (e.g. Netlify, GitHub Pages) and open the URL in your phone’s browser.

## Features

- **Client master:** Dropdown of clients; add new clients with Name, Address, Mobile, GST number. Stored in database.
- **Original plate size:** Length, width, thickness (mm), quantity. **Max allowed: L 4000, W 4000, T 17 mm** – values above this are capped.
- **Original thickness → Cutting thickness:** When you change original plate thickness, it is copied to all cutting rows so labour doesn’t re-enter it.
- **Multiple cutting sizes:** Add several plate sizes with “+ Add plate size”. Each row: L, W, T (from original), Qty. **Total weight** is the sum of all rows.
- **Gauge cutting** and **Pricing** (Rs. per Kg / Rs. per cut); total cost is calculated.
- **Save record:** Saves the job and all line items to the database. With Supabase configured, all devices share the same database.

## Multi-device database (Supabase)

To use one shared database from multiple devices (phones, tablets, PCs), set up Supabase and add your project URL and anon key in `index.html`. See **SUPABASE_SETUP.md** for step-by-step instructions and the SQL to create the tables.

If Supabase is not configured, the app uses **IndexedDB** in the browser (data is per device only).
