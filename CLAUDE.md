# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

A collection of HTML integration examples for **TronDesigner Editor** (a product personalization editor by PromoTron). Each `src/examples/*.html` file is a standalone, self-contained demo of one integration scenario (data-attribute buttons, JS builder API, JSON payloads, URL integration, inline embeds, events, edit/view modes, logo features, etc.). The `index.html` is the catalogue page linking to all examples.

There is no application logic — this is a thin Express server that templates HTML and serves it. Integration logic lives in the example pages themselves and is exercised against a remote TronDesigner instance (`{TD_URL}`) via its `website-integration.js` SDK.

## Common commands

All commands run from `src/`:

```bash
cd src
npm ci          # install
npm run run     # start server on http://localhost:3000 (requires .env)
```

Docker compose (from `deployments/docker/`): `docker compose up -d --build`.

There is no test suite, no linter, and no build step — the server serves source HTML directly.

## Required environment

The server **fails to start** without these (see `src/server.mjs:14-16`):

- `TD_URL` — TronDesigner app URL (e.g. `https://app-dev.trondesigner.com`)
- `TD_URL_INTEGRATION` — integration host URL (used for URL-based integration links)
- `TD_API_KEY` — API key

Loaded via `dotenv` from `src/.env` (also `.env.local`, `.env.prod` exist for different targets). Note: `.gitignore` excludes `/**/.env**`, but the existing `.env` files in `src/` are tracked because they predate the rule — do not commit new credentials.

## Architecture

**`src/server.mjs`** — single-file Express 5 app, ~50 lines. Two responsibilities:

1. `GET /examples/:file` — reads the requested HTML, replaces three placeholders (`{TD_URL}`, `{TD_URL_INTEGRATION}`, `{TD_API_KEY}`) via `replaceAll`, returns it. Only `.html` is templated; directory traversal is blocked.
2. `express.static(root)` — serves everything else (CSS, images) without templating.

`/` redirects to `/examples/index.html`.

**`src/examples/`** — each file is an independent demo. The placeholders `{TD_URL}`, `{TD_URL_INTEGRATION}`, `{TD_API_KEY}` are substituted at request time, so you'll see them as literal strings in source. Examples typically:

- Load `{TD_URL}/js/website-integration.js` with `data-td-api` and `data-td-api-key` attributes, then either
- Use **data attributes** on a button (`data-td-action="OpenCreate"`, `data-td-supplier-code`, etc.) — see `attributes_overview.html`, or
- Use the **JS builder API** (`window.tronDesigner.setExactProduct(...).addExactPrinting(...).open(...)`) — see `quick_start.html`, or
- Use **URL integration** (`{TD_URL_INTEGRATION}/editor?apiKey=...&supplierCode=...`) — see `url_integration.html`.

The script tag often sets `data-td-script-initialized-callback="<fnName>"` to auto-trigger logic once the SDK loads.

**`_template.html`** is the boilerplate skeleton (header, doc links, layout) used as a starting point for new examples. When adding a new example, also add a `<div class="example">` entry to `index.html`.

**`src/assets/style.css`** — shared styling for all example pages. Served statically.

## Conventions for new examples

- Keep the standard header block (logo, "Back to examples" link, doc links) for visual consistency with siblings — see any existing `examples/*.html`.
- Use the three placeholders rather than hardcoding URLs/keys; the server substitutes them.
- Add the new example to `src/examples/index.html` so it shows up in the catalogue.
- The file extension served is `.html` only — other extensions return 415.

## Deployment

`Dockerfile` (in `src/`) is a standard `node:20-alpine` image; `npm ci --omit=dev` then runs `node server.mjs`. The compose file in `deployments/docker/` builds from `../../src` and forwards the three required env vars.