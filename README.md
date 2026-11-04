# NORG Edge Router — Fastly Compute

This repo holds NORG's edge router for sites served through **Fastly Compute**. It installs on **your own** Fastly service and runs in your Fastly account. NORG never receives your credentials.

**Install guide, configuration and troubleshooting: [`fastly/README.md`](fastly/README.md)**

> **On Cloudflare?** Use the [Cloudflare Worker](https://github.com/norgai/norg-edge-mirror-cloudflare-deploy) instead. Other platforms are listed under [Other platforms](#other-platforms).

Current version: **0.2.2**, set by `EDGE_SCRIPT_VERSION` in `fastly/src/lib/config.js`.

---

## What it does

| Visitor | What they get |
|---|---|
| A person in a browser | Your site, unchanged. No call is made to NORG. |
| Googlebot, Bingbot and other search crawlers | Your site, unchanged |
| An AI agent, on a page NORG has rendered | The NORG render, at your own URL, with no redirect |
| An AI agent, on a page not rendered yet | Your page stripped to its facts, while NORG renders it |

Pages are never cached at the edge, so every page request reaches your origin. Only static assets keep Fastly's cache. The router also tells your origin the visitor's hostname in `X-Norg-Public-Host`.

---

## Layout

```
fastly/
  src/index.js             the only file that touches the Fastly SDK
  src/router.js            the request pipeline
  src/lib/                 Fastly adapters: config, origin, request
  tests/                   node:test suites
  fastly.toml              the Compute service manifest
core/                      provider-neutral router logic
workers/, aws/, src/, build.mjs, wrangler.jsonc, README.shopify.md
                           files from other platforms, carried over from the original repo
```

The Fastly code imports `core/` and `workers/lib/`, so those have to stay. The other platforms' files here are older copies, not their releases.

## Development

```bash
node --test fastly/tests/*.test.mjs   # the decision path, nothing to install

cd fastly
npm install                           # @fastly/js-compute
npm run build                         # bin/main.wasm
fastly compute serve                  # Viceroy, on http://127.0.0.1:7676
```

Run it under Viceroy before you trust it: the tests run under Node, and the Wasm runs on a smaller set of web APIs.

## Releasing

1. Bump `EDGE_SCRIPT_VERSION` in `fastly/src/lib/config.js`, then run the tests.
2. Tag `fastly-vX.Y.Z` and publish a GitHub release with `main.wasm` attached.
3. In content-craft, update `fastly_package.py` and `EDGE_WORKER_VERSION_FASTLY`.

---

## Other platforms

| Platform | Where it lives |
|---|---|
| **Fastly Compute** | This repo. Install guide: [`fastly/README.md`](fastly/README.md) |
| **Cloudflare** | [`norg-edge-mirror-cloudflare-deploy`](https://github.com/norgai/norg-edge-mirror-cloudflare-deploy) |
| **Next.js and Vercel** | [`norg-edge-mirror-nextjs-deploy`](https://github.com/norgai/norg-edge-mirror-nextjs-deploy) |
| **AWS CloudFront** | [`norg-edge-mirror-cloudfront-deploy`](https://github.com/norgai/norg-edge-mirror-cloudfront-deploy) |
| **Bunny Edge Scripting** | [`norg-edge-mirror-bunny-deploy`](https://github.com/norgai/norg-edge-mirror-bunny-deploy) |

## Support

For questions about your install, your Site ID or Site Key, contact your NORG account contact.
