# Changelog

Each release of this pack names the Bricks Static Export version it was checked against (`verifiedUpTo` in
`skills/bse-update/references/index.json`). Skills and plugin are released together; a pack never describes a
plugin that has not shipped.

## 0.1.0, 2026-10-12

Pre-release, verified up to Bricks Static Export 1.0.0-rc.8. It goes out with Bricks Static Export 1.0.0.

- First pack: `bse-start-here`, `bse-static-export`, `bse-preflight-findings`, `bse-forms-routes`,
  `bse-github-deploy` and `bse-update`.
- 2026-10-15: the plugin was "Bricks Extensions" with a second extension, Breakpoint Canvas, until this date; the canvas
  moved to BFB Elements Pro and its skill to the BFB Elements pack, and every name here moved to Bricks Static Export
  (`bricks-static-export`, skills `bse-*`).
- Brought up to the release candidate: the header probe's cases, the
  Woo, language and JSON-with-a-file rows in `bse-preflight-findings`; caches, the link safety net, languages and the
  Woo catalogue in `bse-static-export`; popups, loops, components, file fields and `{{field}}` tokens in
  `bse-forms-routes`.
- Brought up to rc.5: the Static Export screen's three tabs and three steps in every skill that names a place on it;
  the download link on the screen's own address, the http:// note, the from-disk link script and the Google hints in
  `bse-static-export`.
- Brought up to rc.7 (the 1.0 additions, 2026-10-16): updating a page from Bricks and the changed files ZIP, site
  search, redirects, extra pages and files, SEO plugin sitemaps, redirected pages skipped, in `bse-static-export`; the
  `search_forms`, `redirects`, `extras` and `sitemaps` rows in `bse-preflight-findings`.
- Corrected after the review of rc.7 (2026-10-17): site search serves the Bricks Search element only, and its 3 MB
  holds up to about 7,500 pages; redirects come from renamed posts, not pages, a prefix redirect works only on a host
  that reads `_redirects`, and on GitHub the export's lines are a marked block below the owner's own; a page update
  fetches the sitemaps again, refuses after the address or archive settings changed, and removes pages now left out
  (a changed files ZIP lists them for deletion by hand); Extra pages and files name their new refusals (the whole
  site, wp-content or uploads folder, folders closed to visitors, the export's own files, a file under Extra pages,
  404 and redirected pages), and files in `.well-known` may have no extension; the new `search_engines` row and the
  no-sitemap note in `bse-preflight-findings`.
