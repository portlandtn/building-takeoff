# Cloudflare Pages deployment

Cloudflare Pages hosts the application's static files; a single Pages Function implements `/healthz`. `_routes.json` invokes Functions only for that endpoint, so ordinary calculator requests use static hosting. The app needs no database, secrets, KV, R2, or runtime bindings.

## Native GitHub setup

Connect only the intended repository using the Cloudflare Pages GitHub connection. Use Pages (not Workers Builds), a project name of your choice, framework preset None, repository root, and build variable `NODE_VERSION=24`.

Build command: `npm ci --ignore-scripts && npm test && npm run check:vendor && npm run check:public && npm run build:cloudflare`.

Build output directory: `.local/pages-assets`.

There is no deploy command, GitHub deployment secret, separate user-created API token, or production route variable. Pages automatically builds the repository-root `functions/` directory and deploys the build output. The build generates `_headers`, `_routes.json`, and a `404.html` to prevent accidental SPA fallback. Application files in `dist/` are copied without modification. GitHub Actions runs verification only.

Use `main` as the Pages production branch. Stage on the assigned pages.dev URL with no custom domain, verify it, then add the custom domain. Later pushes to `main` deploy automatically. Native Pages domain association persists across builds; no Wrangler route reconciliation is involved.

## Verify before adding a domain

On the real pages.dev deployment verify root and all assets, security headers, GET/HEAD health, POST rejection, and missing-path 404. Test default calculations, single-slope/gable switching, workbook export and reopen. Compare asset bytes with the reviewed commit. Pages normally redirects `/index.html` to `/`; this preserves access but differs from the local server's direct 200. Missing paths use an HTML content type with the text `Not found`.

For local verification: `npm run build:cloudflare`, `npm run check:cloudflare`, and `npx wrangler pages dev .local/pages-assets --compatibility-date=2026-09-30`. `check:cloudflare` compiles the health Function without deploying.

## Custom domain

Add the hostname under the Pages project's Custom domains and follow the dashboard's activation flow. Do not point DNS at pages.dev before associating the custom domain, because this can produce a 522. Inspect any DNS record the dashboard proposes before accepting it, and wait for the domain to show as active before sharing the URL. Then verify public HTTPS, health, asset hashes, and the browser workflows on the custom hostname.

To take the site down, remove the custom-domain association and its DNS record. Other hostnames and DNS records are not involved.

## Verification and limits

The build generates `/deployment.json` containing only the source commit SHA. Compare `source_commit` from that endpoint with the commit on `main` to confirm that a Git-triggered build reached production. Local and deployed checks actually run are recorded in [Verification](VERIFICATION.md).

Static requests use Pages hosting; health Function invocations use the account's Functions quota. Check the account plan and limits before going live.

The dependency audit is nonzero; the current counts are recorded in [Verification](VERIFICATION.md) and the ExcelJS/uuid finding is described in [Security](../SECURITY.md). This is not a clean dependency audit or an independent review.

References: [Pages Git integration](https://developers.cloudflare.com/pages/configuration/git-integration/), [Function routing](https://developers.cloudflare.com/pages/functions/routing/), [custom domains](https://developers.cloudflare.com/pages/configuration/custom-domains/).
