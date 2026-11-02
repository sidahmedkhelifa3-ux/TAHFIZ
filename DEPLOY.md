# Putting Tahfiz online (Vercel + Supabase)

Do the steps in this order. Step 1 must be done before the first visit to the site.

## 1. Prepare the database (Supabase, once)

1. Open https://supabase.com/dashboard and select the **tahfiz** project.
2. In the left menu, open **SQL Editor** → **New query**.
3. Open `supabase/migrations/20261101000000_init.sql`, copy all of it, paste, click **Run**.
   You should see "Success. No rows returned".

This creates the tables, locks them (nothing is readable with the public key), and registers the
fingerprint of the server secret. The demo company, news and questions are added automatically on the first visit.

## 2. Put the code on GitHub

1. Create an empty **private** repository on https://github.com/new (no README).
2. In a terminal, in the `tahfiz` folder:

   ```
   git remote add origin https://github.com/<you>/tahfiz.git
   git push -u origin main
   ```

## 3. Deploy on Vercel

1. https://vercel.com/new → **Import** the GitHub repository. Framework: Next.js (detected).
2. Open **Environment Variables**, and paste the whole content of the file `.env.vercel`
   (it is on your computer only, never in git). Four variables:
   `SUPABASE_URL`, `SUPABASE_PUBLISHABLE_KEY`, `TAHFIZ_DB_SECRET`, `TAHFIZ_ADMIN_PASSWORD`.
3. Click **Deploy**. Functions run in Paris (`vercel.json`), next to the database.

## 4. Check it works

- `https://<your-app>.vercel.app/` → the opening animation, then the splash.
- `/database` → asks for the admin password (`TAHFIZ_ADMIN_PASSWORD`), then shows 40 employees.
- In Supabase → **Table Editor**, the `teams`, `employees`, `news` and `cards` tables are now filled.

## 5. Install it on a phone

- **Android (Chrome):** open the site → menu ⋮ → **Install app**.
- **iPhone (Safari):** open the site → Share → **Add to Home Screen**.

## Good to know

- Without the Supabase variables, the site still runs but keeps data in `/tmp`, which Vercel wipes. Always set them.
- Real people's latest scores are readable in Supabase: **Table Editor → v_motivation**.
- To change the admin password: edit `TAHFIZ_ADMIN_PASSWORD` in Vercel → Settings → Environment Variables, then **Redeploy**.
- If `TAHFIZ_DB_SECRET` is ever exposed: generate a new one, update it in Vercel, and in Supabase run
  `update app_secrets set value_hash = encode(extensions.digest('<new secret>', 'sha256'), 'hex') where name = 'server';`
- On your laptop, `npm run demo` still works with no internet database: data goes to `.data/tahfiz.json`.
