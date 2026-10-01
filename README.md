# Data Probe — R1 aircraft-data test

Proves plane-position data can flow: **R1 -> Cloudflare Worker -> adsb.fi -> R1**.
(adsb.fi blocks direct browser fetching, so the worker is the middleman.)

## What's in here

- `worker.js` — the Cloudflare Worker. Forwards `?lat=&lon=&dist=` to adsb.fi,
  adds CORS headers, slims the JSON to what the R1 needs.
- `index.html` — the probe. Fetches through your worker, shows aircraft count
  and nearest-first list (callsign, altitude, distance). Side button refreshes,
  scroll moves through the list. Center is hardcoded to San Francisco, 25nm.
- `install.html` — open in a desktop/phone browser; renders the R1 install QR.
- `icon.svg` — creation icon.

## Steps

1. **Deploy the worker:** dash.cloudflare.com -> Workers & Pages -> Create ->
   Create Worker -> name it (e.g. `r1-adsb-proxy`) -> Deploy -> Edit code ->
   paste `worker.js` -> Save and deploy. Copy the `workers.dev` URL.
2. **Point the probe at it:** put that URL into `WORKER_URL` at the top of
   `index.html`.
3. **Host** `index.html` + `icon.svg` on GitHub Pages (same dance as the audio
   probe: new public repo, upload, Settings -> Pages -> Deploy from branch).
4. **Install:** set `creationUrl` in `install.html`, open it, scan the QR via
   R1 Creations -> add via QR.

## What to report back

- Aircraft count over the Bay Area (expect dozens at 25nm).
- If it fails: the status pill text (e.g. `ERROR http 502`, `timeout`).

## Notes

- Free Cloudflare tier: 100,000 worker requests/day, hard stop (no charges).
  This app uses a few hundred/day at most.
- The worker caches upstream responses for 5s (`Cache-Control: public, max-age=5`).
