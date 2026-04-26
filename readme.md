# tiger-bridge

A tiny static GitHub Pages site that redirects `https://` requests to the
Tiger Trade custom URL scheme so Telegram (which only renders
http/https hrefs as clickable) can deep-link to a specific stock in the
iOS/Android app.

## How it works

1. Telegram message contains `<a href="https://angelystor.github.io/tiger-bridge/AAPL">AAPL</a>`.
2. User taps. Browser opens `…/tiger-bridge/AAPL`. No file at that path,
   so GitHub Pages serves `404.html` (the [documented fallback][gh-404]).
3. `404.html`'s JS reads `AAPL` from `window.location.pathname` and runs
   `window.location.replace("tigerbrokers://stockDetail?symbol=AAPL&market=US")`.
4. iOS/Android route the custom scheme to Tiger Trade, which opens at
   the symbol.

If the app isn't installed, the redirect fails and the page shows a link
to the Tiger SG website fallback.

[gh-404]: https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-custom-404-page-for-your-github-pages-site

## Setup

1. Create a public repo (e.g. `angelystor/tiger-bridge`).
2. Drop `404.html`, `index.html`, this README at the root. Push.
3. Repo → Settings → Pages → enable from `main` branch root.
4. Wait ~1 min for the first deploy. Visit `https://<user>.github.io/tiger-bridge/AAPL`
   to confirm — the page should briefly show "Opening AAPL…" and prompt
   to open in Tiger Trade.
5. In trade-bot's `.env`:
   ```
   TIGER_QUOTE_URL_TEMPLATE=https://<user>.github.io/tiger-bridge/{symbol}
   ```

## Editing

If Tiger changes their custom scheme, edit `SCHEME_TEMPLATE` at the top
of the `<script>` block in `404.html`. The redirector handles every
symbol via the URL path, so there are no per-symbol files to regenerate.
