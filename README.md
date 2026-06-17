# Kitchen Recipes

A recipe app for the kitchen line. Cooks browse and present recipes on an iPad;
the chef adds and edits them from any device. Recipes and dish photos sync
through the cloud, so an edit made on a laptop shows up on the iPad automatically.

It's a single HTML file backed by [Supabase](https://supabase.com). No build
step, no framework, drop it on GitHub Pages and it runs.

## Features

- **Searchable recipe index** with category filters
- **Full recipe view**: ingredients, method, chef's notes, dish photo
- **Present mode**: a clean fullscreen slideshow for the line, swipe or tap through
- **PDF import**: upload a recipe PDF and the app pulls in the text and the
  dish photo automatically, with an editable review step before saving
- **Cloud sync**: edit on a laptop, changes appear on the iPad
- **Offline-first**: the iPad shows recipes with no internet; edits made offline
  queue and push when WiFi returns
- **Chef sign-in**: only a signed-in chef can add/edit/delete; cooks browse freely

## Setup

This needs a free Supabase project (about 10 minutes, one time). Full
step-by-step instructions, creating the project, the database table, the chef
login, and where to paste your keys, are in **[CLOUD-SETUP-GUIDE.md](CLOUD-SETUP-GUIDE.md)**.

Short version:

1. Create a free project at [supabase.com](https://supabase.com)
2. Run the SQL from the setup guide to create the `recipes` table
3. Create your chef login under Authentication → Users
4. Paste your Project URL and key into the top of `kitchen-recipes-cloud.html`
5. Commit and enable GitHub Pages

Until the keys are added, the app runs in offline-only mode on whatever device
opens it, so it's never broken mid-setup.

## Deploying on GitHub Pages

1. Put `kitchen-recipes-cloud.html` in this repo
2. **Settings → Pages → Source:** deploy from your branch (e.g. `main`)
3. Open the published URL on the iPad

To make it feel like a native app on the iPad: open the page in Safari, tap
**Share → Add to Home Screen**. It then launches fullscreen from an icon.

## How sync works

| Situation | What happens |
|-----------|--------------|
| Cook opens the iPad | Recipes load instantly from the local cache, then refresh from the cloud |
| Chef signs in and edits | Change saves to the cloud and appears on other devices |
| WiFi drops | App keeps working from cache; any edits queue locally |
| WiFi returns | Queued edits push automatically; latest recipes pull down |

The status pill by the title shows the current state, **Live**, **Synced**,
**Syncing…**, or **Offline**.

## Security

Editing is protected at the database level with Supabase Row-Level Security:
the public can only read recipes, and only the signed-in chef account can make
changes. The publishable key in the HTML is designed to be public and safe to
commit, the database rules are what actually guard the data.

## Tech

- Single static HTML file (HTML/CSS/vanilla JS)
- [Supabase](https://supabase.com) for database, auth, and sync
- IndexedDB for the offline cache
- [pdf.js](https://mozilla.github.io/pdf.js/) for in-browser PDF import

## Files

- `kitchen-recipes-cloud.html`: the app
- `CLOUD-SETUP-GUIDE.md`: one-time Supabase setup walkthrough
