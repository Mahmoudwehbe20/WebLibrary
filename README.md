# Web Library

Find, filter, and track every game across every console — plus a Spin tab that
picks a random local game for date night (2-player) or family (3+).

The app is a static front-end. The full game catalog comes from RAWG through a
tiny Cloudflare Pages Function (`functions/api/games.js`) so your API key stays
server-side and there's no CORS hassle.

## What's in here

```
index.html              the app (this is the whole UI)
functions/api/games.js  serverless proxy -> RAWG, served at /api/games
manifest.webmanifest    PWA manifest (installable)
sw.js                   service worker (offline app shell + cached data)
icons/                  app icons
```

## Deploy (Cloudflare Pages — ~5 min)

1. Get a free RAWG key at https://rawg.io/apidocs ("Get API Key").
2. Push this folder to a GitHub repo (or use `wrangler pages deploy`).
3. In Cloudflare dashboard: **Workers & Pages → Create → Pages →** connect the repo.
   - Build command: *(none)*
   - Build output directory: `/` (the repo root)
4. **Settings → Environment variables →** add `RAWG_KEY` = your key. Redeploy.
5. Open the site. It auto-calls its own `/api/games`, so the full catalog loads
   with nothing to configure. Add it to your home screen to install it.

### Local / offline behavior
If `/api/games` isn't reachable (e.g. opening `index.html` directly), the app
falls back to a small built-in starter set so it's never blank. Deploying with
the key switches it to the full 500k+ catalog automatically.

### Optional: separate Worker instead of the Pages Function
If you'd rather host the proxy elsewhere, deploy any Worker that forwards to
`api.rawg.io/api/games` (adding your key + `Access-Control-Allow-Origin`), then
paste its URL in the app's ⚙ Settings → "Custom data source".

## Notes / next ideas
- True random across the whole catalog: have the proxy pick a random `page` when
  the app asks for a random spin (RAWG paginates ~20 per page over 500k games).
- Your shelves are stored locally on the device (localStorage). A future version
  could sync them via Cloudflare KV if you want them shared across devices.
