# Dantu World Dental Clinic — Website

A single-page marketing site for Dantu World Dental Clinic, with a booking form on the public side and a staff-facing dashboard for managing leads and bookings.

## What's in this repo

Everything lives in one file: `index.html`. There's no build step, no framework, no `node_modules` — it's plain HTML, CSS (in a `<style>` block), and vanilla JS (in `<script>` blocks). That makes it trivial to host anywhere that serves static files, GitHub Pages included.

The page has three views that get toggled with JS (`showPublic()`, `showLogin()`, `showDashboard()`), all sitting in the same HTML file:

- **Public site** — hero, services, pricing, testimonials, about, contact, and a booking modal (name, phone, treatment, date/time, notes → simulated ₹500 advance payment or "pay at clinic").
- **Staff login** — a form gated by a hardcoded email/password check in the JS.
- **Admin dashboard** — stats (total leads, paid, dropped at payment, revenue), a service breakdown, a token queue for the day's paid bookings, a filterable/searchable leads table, and CSV export.

## Important — read this before you rely on it

The booking form and admin dashboard save data using `window.storage`, which is **not a real backend**. It's an in-browser storage API that only exists inside Claude.ai's artifact preview environment. Once this is pushed to GitHub and served as a static site (GitHub Pages or otherwise), `window.storage` won't exist — the code already checks for that (`if(!window.storage)`) and the dashboard will show "Storage unavailable" instead of crashing, but functionally:

- Bookings submitted on the live site **will not be saved anywhere**.
- The admin dashboard **will have nothing to show**.
- "Pay now" doesn't charge anyone — it's a 900ms `setTimeout` that flips a status flag. No payment gateway is wired up.
- Staff login is a client-side `if` statement comparing plaintext strings in the JS you can view in dev tools. It is not authentication in any real sense — the demo credentials (`admin@dantuworld.example` / `dantu2026`) are sitting in the page source right now.

None of this is a bug in what's built — the page itself says as much in the demo banner on the dashboard. But it means what you can deploy to GitHub Pages today is a **visual/UX prototype of the public site**, not a working booking system. To make the booking flow and dashboard actually function, you'd need:

1. A real backend (Node/Express, Django, Firebase, Supabase — anything with a database) to replace every `window.storage` call.
2. A payment gateway integration (Razorpay is the obvious fit for an Indian clinic) instead of the simulated `handlePayNow()`.
3. Server-side authentication (hashed passwords, sessions or JWT, ideally 2FA) instead of the hardcoded login check.
4. An SMS/WhatsApp integration if you want the token number actually delivered to patients, since right now the success message just claims it was sent.

If you only need the marketing site live and don't need bookings to actually persist yet, deploying as-is is fine — just know that's what you're shipping.

## Running it locally

No install needed. Either:

```bash
# just open it directly
open index.html      # macOS
xdg-open index.html  # Linux
```

or serve it so relative paths and any future fetches behave like a real site:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Deploying to Vercel

Since this is a static `index.html` at the repo root, Vercel needs no config file, no build command, no framework preset:

1. Push this repo to GitHub if it isn't there yet (`git init`, `git add .`, `git commit -m "Initial commit"`, `git remote add origin <your-repo-url>`, `git push -u origin main`).
2. On [vercel.com](https://vercel.com), click **Add New → Project** and import the repo.
3. On the import screen, set **Framework Preset** to **Other**. Leave **Build Command** and **Output Directory** blank — there's nothing to build.
4. Click **Deploy**. Vercel serves `index.html` as-is and gives you a `<project>.vercel.app` URL.

Every push to `main` after that auto-redeploys. If you want a custom domain, that's **Project → Settings → Domains** in the Vercel dashboard.

One Vercel-specific thing worth knowing: Vercel's free tier deploys are public by default, so anyone with the URL can view page source and see the hardcoded staff login credentials mentioned above. That's true on GitHub Pages too — it's not a Vercel issue — but worth flagging since a real deploy makes it more likely someone actually looks.

## Deploying to GitHub Pages

1. Push this repo to GitHub (if you haven't already — see step 1 above).
2. In the repo, go to **Settings → Pages**.
3. Under **Source**, pick the `main` branch and `/ (root)` folder.
4. Save. GitHub will give you a URL like `https://<username>.github.io/<repo-name>/` within a minute or two.

Since `index.html` is at the repo root, no extra config is needed here either.

## Project structure

```
.
├── index.html   # entire site — markup, styles, and JS
├── README.md
└── .gitignore
```

## Editing

Everything you'd want to change is in `index.html`:

- Copy/pricing/services text — in the HTML markup, roughly lines 1–930.
- Colors and layout — CSS variables at the top of the `<style>` block (`--teal-900`, `--coral`, etc.) control the whole palette.
- Booking logic, dashboard rendering, CSV export — the `<script>` block at the bottom.

## License

This is client work for Dantu World Dental Clinic, not an open-source project — no license is attached. Treat the code and content as belonging to the clinic.
