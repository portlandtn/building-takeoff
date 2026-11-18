# Verification record

Preparation date: 2026-11-17. These results describe automated and scripted checks run on the initial contents of this repository. They are not an independent review, penetration test, manufacturer approval, or engineering certification.

## Automated checks

| Check | Result |
| --- | --- |
| Node runtime | Local checks: 22.23.4; npm 10.9.9, below the `engines` requirement of Node 24. The GitHub Actions workflow then repeated install, tests, vendor, publication, and Cloudflare build checks on Node 24 for the first application commit, and passed |
| Clean installation | `npm ci --ignore-scripts` completed |
| Unit and integration tests | 30 passing, zero failures |
| Bundled library consistency | Six library/license files match installed pinned packages byte-for-byte |
| Publication policy | 39 working-tree candidate files and the staged index passed the path policy and heuristic rules; no credential-pattern findings |
| Cloudflare build | `build:cloudflare` produced the static assets; `check:cloudflare` compiled the health Function |
| Dependency audit | Not clean: 7 entries (2 moderate, 5 high). See below |

Tests include geometry, measurement round trips, asymmetric linked edits, openings, panel splits, hardware and length overrides, saved-job import/export, malformed job structures, static-server method/path boundaries, symlink escape rejection, staged-secret detection, and a check that the catalog, takeoff output, and exported workbook contain no links, part codes, or source fields. Security tests use synthetic fixtures and a temporary Git index.

## Browser check

Headless Chrome against `node server.mjs`, 1500 × 1000 viewport: the page loaded with no console errors and no requests outside its own origin. Roof and wall selections, the takeoff tabs, Excel export, and reopening the exported file worked. The rendered page contained no external links and the exported workbook contained no URLs.

Not exercised: narrow and mobile layouts, keyboard-only use, browsers other than Chrome, and the deployed Pages site.

## Dependency audit

`npm audit` reports 2 moderate entries (`exceljs` and its dependency `uuid`) and 5 high entries (`brace-expansion`, and `wrangler` with its dependencies `miniflare`, `sharp`, and `undici`). The ExcelJS/uuid finding is described in `SECURITY.md`; the checked-in ExcelJS browser bundle contains the affected code. `wrangler` is a development dependency used to compile the health Function and is not part of the served application. None of these has been remediated, and their reachability has not been analyzed beyond the note in `SECURITY.md`.

## Remaining limits

No separate secret scanner was run; the publication check is a heuristic. No independent code review, manufacturer review, load test, or engineering certification was performed. Archive guards bound declared and actual expansion as described in `SECURITY.md` but are not a sandbox.

The repository's history began as the hosting service's stub initial commit (a one-line README) followed by one commit adding the application, so there is no earlier history to scan. An original-code license has not been selected. Repeat staged-content and history checks for future changes.
