---
name: bxt-static-export
description: "Use when exporting a Bricks site as static HTML with Bricks Extensions, or explaining what an export does: export mode, which URLs are exported or left out, the export options and the destination, the ZIP and GitHub targets, runs and retention, what the ZIP holds, a page re-export, and the WP-CLI commands."
---

# Static Export: how an export works

Read `bxt-start-here` first. Static Export lives on **Bricks Extensions > Static Export**: the Preflight, Export,
Destination, GitHub, Runs and Releases cards.

## A run, step by step

1. **Preflight.** Every export runs it first; a blocker stops the start (`bxt_preflight_blocked`). See
   `bxt-preflight-findings`.
2. **Discovery** lists the jobs from the database, not by following links:
   - published posts of public post types (not attachments, not Bricks templates); password-protected posts are left
     out and listed;
   - term archives with their pagination, post type archives, author and date archives (both on by default);
   - the front page, the posts page, a 404 page, `robots.txt`, `favicon.ico`, and `wp-sitemap.xml` with its children
     when the site exposes sitemaps;
   - the pagination pages of every paginated loop in Bricks data; a loop in a template is expanded once per post the
     template applies to; a loop inside a component or global element is reported, not expanded;
   - for load more and infinite scroll past 100 items, the extra pages under `/<page>/bxt/<loop>/<n>/`;
   - on a multilingual site, every translation at its own language address (Polylang and plugins like it), or every
     page once per language under the language's folder (TranslatePress); the preflight's `languages` row lists them.
   Left out: search results, login, `wp-admin`, the REST API, WooCommerce cart, checkout and account with every
   address under them, author archives of authors without a user, drafts, and the owner's **Leave out** patterns.
   `wp bricks-extensions discover` prints the job list.
3. **Pages** are fetched in export mode: anonymously (no admin bar, no edit links), each fetch carrying
   `?bxt-nocache=<run id>` so page caches let it through and never keep it (WordPress never sees the argument).
   Each page proves export mode by its answer headers or, when a host strips those, by a proof inside the page made
   with the run's token. A stored copy from an earlier export is refused. When three pages in a row come back without
   export mode, or a cache answers the export's fetches before the first page, the run stops with one sentence that
   names the cause, instead of failing page after page.
   **The link safety net:** after the listed pages, links on them to this site that no job covers (no query string,
   not left out, not a file) are fetched too, at most 500 in two passes, and listed as found by link only.
   A page or file larger than the host accepts in one request goes up in parts.
4. **Rewriting.** Links to this site become relative, so the export works from any folder; `404.html` keeps
   root-relative links; sitemaps, `robots.txt`, `canonical`, `hreflang` links and `og:url` use the destination base
   URL.
5. **Assets**: only the files the pages use are exported (images, `srcset`, CSS `url()` and `@import`, icon fonts,
   Bricks' CSS files), copied from disk or fetched.
6. **Google Fonts** are copied into the export by default, linked ones and ones a stylesheet imports, so the static
   site never calls Google.
7. **Verify** checks the tree: every internal link exists, every referenced file resolves, no reference to the
   WordPress site is left, every page carried the export header. Failures are listed, never rounded away.
8. **The archive**, `site.zip`, is built in a fixed order with its sha256 recorded. For the GitHub target the push
   follows (`bxt-github-deploy`).

## What export mode changes in a page

Only while a page renders for the export; Bricks' theme files and settings are never edited.

| Feature | In the export |
|---|---|
| Lazy load | turned off, with `loading="lazy"` on images unless the image sets its own loading or high fetch priority |
| Several loops on a page | the paginated loop moves to its page; every other loop stays on page 1 |
| AJAX pagination | plain links to pre-rendered pages |
| Load more, infinite scroll | up to 100 items pre-rendered and revealed in batches by a small script; past that a "See all" link to separately exported pages |
| AJAX popups | rendered into the page, so they open without a request |
| Query filters, live search, search form | not rendered; the loop exports unfiltered with plain pagination |
| Comments | the whole comments area removed; existing comments are not exported |
| Relative dates | rendered once at export time, then they stand still |
| WordPress runtime | `ajaxUrl`, REST URL and nonces blanked (kept only where a form sends to this WordPress site); REST discovery, oEmbed, feed links, emoji script and generator removed |
| Forms | as each form's Static Export group says (`bxt-forms-routes`) |
| WooCommerce | a catalogue: products, the shop and categories keep prices and descriptions; add-to-cart buttons and forms, quantity fields, the mini cart, the sort menu and the scripts that call the cart are removed; menu items to cart, checkout and account are left out |

## The options

Option `bxt_static_export`, saved from the screen:

| Card, label | Key | Default |
|---|---|---|
| Export, Google Fonts local | `google_fonts_local` | on |
| Export, Author archives | `include_author` | on |
| Export, Date archives | `include_date` | on |
| Export, Leave out (one path per line; a plain path leaves out that page and everything under it; `*` and `?` match like a shell pattern) | `exclude_patterns` | none |
| Export, Parallel requests (pages fetched at once, 1 to 6) | `parallel` | 3 |
| Export, Keep files of the last N runs (1 to 10) | `retention_runs` | 3 |
| Destination, Base URL (where the static site will live) | `base_url` | none |
| Destination, Static origins | `static_origins` | none |
| Destination, WordPress address for forms | `forms_origin_url` | this site |

Without a base URL, sitemaps and canonical links keep this site's address. Set it to the static site's address.

## Targets

- **ZIP**: always available. Download ZIP on the Export card or the Runs card.
- **GitHub**: available once a repository and a token are saved on the GitHub card; until then the picker shows it
  disabled with its reason. See `bxt-github-deploy`.

## Runs and retention

A run is a folder under the uploads folder holding `run.json`, the tree and `site.zip`. The Runs card lists the last
20 runs (Started, Pages, Files, Size, Status, Check); the files of the newest N finished runs are kept (setting
above), older ones keep only their summary. A run left for more than six hours cannot be resumed. On the screen a run
in progress is resumed after a reload, or discarded. Delete removes a run with its files; a run in progress cannot be
deleted, unless it is abandoned (not the current run, or silent for more than six hours): then Delete cancels it first.

## What the ZIP holds

Each page as `<path>/index.html`, `404.html` at the root, `robots.txt`, `favicon.ico`, the sitemaps when exposed, the
files the pages use at their WordPress paths, and localised Google Fonts under `assets/fonts/google/`. It never holds
`wp-config.php`, the theme folder, or any file no page referenced. A leaked export token exposes only what a visitor
could already see.

## A page re-export

Re-exports one page and the files it uses: the Releases card's **Re-export this page**, `export-start` with `only`,
or `wp bricks-extensions export --only=<url>`. For ZIP, the archive is rebuilt from the last complete export with the
page replaced; for GitHub, only that page's changed files are committed.

## Where an export runs

- **The owner's browser**, from Start export: the default, and it works on any host because PHP never waits on
  itself. The tab must stay open (Pause and Cancel are on the panel).
- **WP-CLI**, server-side: `wp bricks-extensions export [--target=zip|github] [--parallel=<n>] [--only=<url>]
  [--base-url=<url>] [--skip-fonts] [--force] [--format=table|json]`. A CLI process is not a PHP worker, so it suits
  large sites. `--parallel` is 1 to 6, default 1; only a host with free PHP workers gains from more: above 1 the
  worker probe runs first and the number is capped at the free workers it saw (a line says so).
- **The abilities**, server-side in steps, only where at least two PHP workers are free (`bxt-start-here`).

## WP-CLI commands

| Command | Does |
|---|---|
| `wp bricks-extensions status` | the plugin, Bricks and licence state, every extension's switch |
| `wp bricks-extensions extension <id> on\|off` | switches `static-export` or `breakpoint-canvas` |
| `wp bricks-extensions preflight [--format=table\|json]` | runs and stores the preflight; exit 1 on a blocker |
| `wp bricks-extensions discover [--format] [--kind=<kind>]` | the job list; kinds page, post, archive, pagination, reveal, 404, sitemap, robots, favicon |
| `wp bricks-extensions regenerate-css` | Bricks' CSS files regenerated with Bricks' own generator, then the preflight again |
| `wp bricks-extensions export-token issue [--ttl=<seconds>]` | prints a new export token once, for a crawl driven from outside the site |
| `wp bricks-extensions export-token revoke` | revokes it |
| `wp bricks-extensions export ...` | the export (above) |
| `wp bricks-extensions runs list [--format]` | the remembered runs |
| `wp bricks-extensions runs delete <id>` | deletes a run's files and summary |
| `wp bricks-extensions workers [--max=<n>] [--timeout=<seconds>] [--format]` | how many PHP workers answer the probe right now; from WP-CLI every worker of the pool can answer, inside a web request its own worker is busy |
| `wp bricks-extensions abilities list [--format]` | the five abilities and whether they are registered (the AI agents switch) |
| `wp bricks-extensions abilities run <name> [--input=<json>] --user=<administrator>` | runs one ability as that user and prints its answer as JSON |

The GitHub and release commands are in `bxt-github-deploy`. Ask the owner before switching an extension, deleting a
run or issuing a token: an export token switches export mode on for any request that carries it until it expires.
