# Deploying kay2.trailos.uk from GitHub

Cloudflare Workers runs `npx wrangler deploy` on every push to `main`.

## Why the first deploy failed

GitHub only had `README.md`: no `wrangler.toml`, no `package.json`, and no site files under `public/`. Wrangler had nothing to deploy.

This branch adds the missing scaffold.

## Repo layout

```
├── README.md          GitHub profile (not served by the Worker)
├── wrangler.toml      Cloudflare Worker config
├── package.json       Wrangler dependency
├── src/worker.js      Serves static files from public/
└── public/            ← your live site lives here
    ├── index.html
    └── brand/
```

## Sync your real site from the Pi

On your Raspberry Pi, find the portfolio folder Claude Code created, then:

```bash
cd /path/to/kay2stack   # your local clone of github.com/kay2stack/kay2stack

# Copy site files into public/ (adjust paths to match your Pi layout)
cp ~/path/to/site/index.html public/
cp -r ~/path/to/site/brand public/   # if you have a brand/ folder

# If your Pi keeps index.html at the repo root instead:
# cp ~/path/to/site/index.html public/
# cp -r ~/path/to/site/brand public/

git add public/
git commit -m "Add portfolio site from Pi"
git push origin main
```

Cloudflare will rebuild automatically.

## Worker name

`wrangler.toml` uses `name = "kay2"`. If your Pi uses a different worker name, update it to match; otherwise Cloudflare may create a new Worker instead of updating the existing one.

Check on the Pi:

```bash
grep '^name' wrangler.toml
```

## Cloudflare dashboard settings

| Setting | Value |
|---|---|
| Repository | `kay2stack/kay2stack` |
| Build command | `npm install` (recommended) or leave empty |
| Deploy command | `npx wrangler deploy` |
| Root directory | `/` |

Build token must have **Workers Scripts Edit** permission.

## Test locally (on Pi or any machine)

```bash
npm install
npm run dev
# open http://localhost:8787
```
