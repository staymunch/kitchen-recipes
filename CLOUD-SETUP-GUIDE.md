# Kitchen Recipes — Cloud Version Setup

This is a one-time setup, about 10 minutes, no coding. Once it's done, recipes
sync between the iPad and your laptop automatically: edit anywhere, and the
changes show up everywhere. Cooks can browse without signing in; only you (the
chef) can add or edit.

Until you complete this, the app still works — it just runs in offline-only mode
on whatever device you open it on (same as the non-cloud version).

---

## Step 1 — Create a free Supabase project

1. Go to https://supabase.com and click **Start your project**.
2. Sign up (GitHub or email).
3. Click **New project**. Give it a name like "kitchen-recipes".
4. Set a database password (save it somewhere — you won't need it day to day).
5. Pick the region closest to the restaurant.
6. Click **Create new project**. Wait ~2 minutes for it to spin up.

## Step 2 — Create the recipes table

1. In the left sidebar click **SQL Editor**.
2. Click **New query**, paste in everything below, and click **Run**.

```sql
create table recipes (
  id text primary key,
  title text not null,
  category text,
  description text,
  servings text,
  prep_time text,
  cook_time text,
  ingredients jsonb default '[]',
  steps jsonb default '[]',
  notes text,
  image text
);

-- Turn on row-level security
alter table recipes enable row level security;

-- Anyone can READ recipes (cooks browsing, no login needed)
create policy "Public read" on recipes
  for select using (true);

-- Only signed-in users can ADD / EDIT / DELETE
create policy "Authed write" on recipes
  for all using (auth.role() = 'authenticated')
  with check (auth.role() = 'authenticated');
```

You should see "Success. No rows returned." That's correct.

## Step 3 — Create your chef login

1. In the sidebar click **Authentication** → **Users**.
2. Click **Add user** → **Create new user**.
3. Enter your email and a password. This is what you'll sign in with in the app.
4. (Optional but easier: **Authentication → Providers → Email**, and turn OFF
   "Confirm email" so the account works immediately without a confirmation click.)

## Step 4 — Get your two keys

1. In the sidebar click **Project Settings** (gear icon) → **API**.
2. Copy two values:
   - **Project URL** (looks like `https://abcdxyz.supabase.co`)
   - **anon public** key (a long string starting with `eyJ...`)

## Step 5 — Put the keys in the app

1. Open `kitchen-recipes-cloud.html` in any text editor.
2. Near the top you'll see:

   ```js
   const SUPABASE_URL = '';
   const SUPABASE_ANON_KEY = '';
   ```

3. Paste your values between the quotes:

   ```js
   const SUPABASE_URL = 'https://abcdxyz.supabase.co';
   const SUPABASE_ANON_KEY = 'eyJ...your-long-key...';
   ```

4. Save the file, then push it to your GitHub repo like the other versions.

## Done

- Open the app on the iPad — it shows recipes to everyone, no login.
- Tap **Sign In**, enter your chef email/password — now you can add/edit/delete.
- Edit on your laptop (same URL, sign in), and the iPad picks up changes.
- The little pill by the title shows status: **Live** (online, browsing),
  **Synced** (signed in), or **Offline** if WiFi drops. Offline edits queue and
  push automatically when WiFi returns.

---

## Notes on the "anon public" key

It's safe to put this key in the file and on GitHub — it's designed to be public.
The row-level security rules above are what actually protect your data: the public
can only read, and only your signed-in account can make changes. Nobody can edit
recipes without your email and password.

## A note on photos

Right now dish photos are stored directly in the database as compressed image data,
which keeps setup simple and works fine for a normal recipe library. If you ever
load hundreds of high-res photos and want to optimize, Supabase has a separate
Storage feature for files — that's a future tweak, not needed to start.
