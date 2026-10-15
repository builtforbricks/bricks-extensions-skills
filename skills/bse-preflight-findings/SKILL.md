---
name: bse-preflight-findings
description: "Use when reading or explaining a Bricks Static Export preflight report (the bricks-static-export/preflight ability, wp bricks-static-export preflight, or step 1, Check the site, on the Static Export screen): every row id with its level, what it costs the static copy and the fix, in the plugin's own words."
---

# Static Export: the preflight findings

Read `bse-start-here` first. The preflight lists what on this site cannot go static and how to fix it. Running it
changes nothing; it stores the last report, which step 1, **Check the site**, and the dashboard show (the counts in view, the rows folded under
**Show what the check found**, open by themselves when there is a blocker). Every export runs it
first.

Each row has an `id` (stable, use it), a `level`, the `feature`, `where` (the pages, templates or menus it was found
on), the `consequence` and the `fix`. Levels: **blocker** (the export will not start until it is fixed), **warn**
(the export runs, but the static copy differs from the live site in a way the owner should decide on), **info** (a
note: what the export does on its own). Blockers sort first. Through the `bricks-static-export/preflight` ability the
info level is called `note`, and `where` is text: the titles with their addresses, ten at most.

Quote the consequence and the fix as the plugin words them. Propose a fix only in the plugin's terms below, and make
it only on the owner's word. A fix in a page or a form happens in Bricks; a fix of a setting happens on the screen
the fix names.

## Blockers

- **`permalinks`** (Permalinks). Every URL carries a query string, so the export cannot write one file per page.
  Fix: Choose a permalink structure under Settings > Permalinks, for example Post name.
- **`header_probe`** (Export header round trip). The server fetches its own front page with an export token (and a
  cookie page caches step aside for) and expects export mode; when it is missing, a second fetch asks past any cache.
  The row's `probe.case` names what was seen. Blockers:
  - Case `cache`: "A page cache answers even the export's fetches with stored copies (it ignores query strings and
    cookies), so export mode never runs and the cache may keep export pages for visitors." Fix: Make the cache step
    aside for requests that carry the X-BSE-Export header (on nginx: `fastcgi_cache_bypass $http_x_bse_export;` and
    `fastcgi_no_cache $http_x_bse_export;`), or let it cache by query string, or switch it off during the export.
  - Case `request`: "Export mode never ran: a firewall or proxy in front of the site removes the X-BSE-Export header
    from requests, so the export would ship live pages with lazy placeholders and REST calls." Fix: Allow the
    X-BSE-Export request header in the firewall or proxy (it carries a one-time token), or export from a copy of the
    site that is not behind it.
  - Another status: "The front page answered <status> to the probe instead of 200." Fix: Make sure the site address
    answers directly, without a redirect, and that no firewall blocks requests from the server.
  - No token: "No export token could be issued, so export mode cannot be switched on for the fetch." Fix: Check that
    the site can write options, then run the preflight again.

  Not blockers:
  - A request error is a **warn**: "The server could not fetch its own front page (<error>). The export from this
    screen is not affected: your browser fetches the pages. WP-CLI exports and agent abilities fetch from the server
    and would fail." Fix: Nothing to do for exports from this screen. For WP-CLI, ask the host why the site cannot
    reach itself; Site Health under Tools shows the loopback state.
  - Case `stripped`, **info**: "The host removes the export headers from answers. Each page carries its own proof of
    export mode, so the export goes on." Fix: Nothing to do.
  - Case `busted`, **info**: "A page cache answers ordinary requests to this site with stored copies. The export asks
    for every page past the cache, so it is not affected, and the cache never keeps an export page." Fix: Nothing to do.

  The browser export's own preflight skips this probe. Before the first page it checks that no cache answers its
  fetches, and it stops with one sentence when three pages in a row come back without export mode.

## Warnings

- **`render_wordpress`** (Pages that render with WordPress). These pages are set to render with the WordPress
  editor; their Bricks data is ignored and the export gets the WordPress output. Fix: Open each page in Bricks and set
  it to render with Bricks, or leave it if the WordPress output is what you want.
- **`menu_unpublished`** (Unpublished pages in a menu). The menu links to pages that are not published; on the static
  site those links lead to a 404. Fix: Publish the page or remove it from the menu under Appearance > Menus. Each
  place names the menu and the page's status.
- **`css_version`** (CSS files from another Bricks version). "The CSS files were generated by Bricks <old>; this site
  runs Bricks <current>.", or "Bricks has no record of generating its CSS files; pages may export without styles."
  Fix: Regenerate the CSS files so every page gets its styles from the current Bricks build.
- **`css_missing`** (Missing CSS files). These pages have no CSS file on disk, so they export without their styles.
  Fix: Regenerate the CSS files.
- **`css_stale`** (Stale CSS files). These CSS files lag the page content (after an import, a migration or a search
  and replace), so the export would carry old styles. Fix: Regenerate the CSS files.

  The three CSS rows appear only when Bricks loads its CSS as files, and carry the action **Regenerate CSS files** on
  step 1, Check the site (`wp bricks-static-export regenerate-css`). It writes Bricks' CSS files with Bricks' own generator
  and runs the preflight again; it refuses when the CSS loading method is inline.
- **`filters`** (Query filters). Query filters need WordPress to answer; the export leaves them out and exports the
  loop unfiltered with plain pagination. Fix: Remove the filter elements from exported pages, or keep them and accept
  that the static copy shows the unfiltered loop.
- **`live_search`** (Live search). Live search asks WordPress for results as you type; the export leaves it out. Fix:
  Remove the live search, or replace it with a search service that works from static pages.
- **`search_forms`** (Search forms). The search form sends its query to WordPress; on the static site a search leads
  nowhere, so the form is left out. Fix: Remove the search form, or point it at an external search service.
- **`woo_buying`** (Buying on the static site). "The static copy cannot sell: products, the shop and categories keep
  their prices and descriptions, but add-to-cart buttons and forms, quantity fields, the mini cart and the sort menu are
  removed, with the scripts that call the cart." Fix: Keep the shop on WordPress, or leave the shop out of the export
  with patterns (the row lists them, such as `/shop/`, `/product/`).
- **`woo`** (WooCommerce cart, checkout and account elements). These elements need a shopper's cart or account; on an
  exported page they show an empty or broken state. Fix: Keep them on the cart, checkout and account pages, which the
  export leaves out, or exclude the pages they are on.
- **`login`** (Login and account forms). Login, registration and account forms cannot work on a static site. Fix:
  Remove them from exported pages, or exclude those pages.
- **`popups_ajax_loop`** (AJAX popups inside loops). An AJAX popup opened from inside a loop is inlined once per item,
  which multiplies the page weight. Fix: Set the popup template to render without AJAX, or move the trigger out of the
  loop.
- **`loops_in_components`** (Paginated loops in components). A loop inside a component or global element is not
  expanded; only the page it sits on is exported, not its pagination pages. Fix: Move the paginated loop into the page
  itself, or give the component a loop without pagination.
- **`third_party_calls`** (Calls to admin-ajax or the REST API). These elements reference admin-ajax or the REST API;
  on a static site those calls fail. Fix: Replace the call with a service that works from static pages, or remove the
  element from exported pages.
- **`forms_endpoint_missing`** (Endpoint route without an address). These forms are set to send to another service but
  have no endpoint URL, so on the static site they have nowhere to send. Fix: Open the form in Bricks and paste the
  service's endpoint URL in the Static Export group, or set its destination to This WordPress site.
- **`forms_json_file`** (JSON with a file field). "These forms send to a service as JSON but have a file field. JSON
  cannot carry files, so the static page sends them as form fields (multipart) with the files instead; a service that
  reads JSON only may refuse those submissions." Fix: Choose Form fields as the encoding in the form's Static Export
  group, or remove the file field, then send one test submission with a file from the static site.
- **`forms_no_origin`** (No static origin for forms). "<n> forms post to this WordPress site, but no static origin is
  set, so browsers on the static site cannot read the answers and the forms show an error." Fix: Enter the static
  site's origin under Destination (the base URL or a static origin), or set the forms to send to a service.
- **`forms_reachability`** as a warning (Forms origin reachability): "admin-ajax.php at <address> answered <status>
  instead of 400, so forms posting to this WordPress site may fail." Fix: Make /wp-admin/admin-ajax.php publicly
  reachable at the WordPress address for forms (no password, access rule, redirect or IP allowlist on it), or correct
  that address under Destination.

## Notes

- **`woo_pages`** (WooCommerce cart, checkout and account). "These pages answer each shopper from WordPress, so the
  export leaves them and every address under them out, and leaves menu items that lead to them out of the exported
  pages." Each place names the page and its endpoints. Fix: Nothing to do. Link to the shop on the WordPress site if
  shoppers still need these pages.
- **`languages`** (Languages). One place per language with its address. Polylang (and plugins like it): "Each
  translation is exported at its own language address, with its own archives and pagination; the language switcher
  links stay inside the export." Fix: Nothing to do. TranslatePress: "Every page is exported once per language, under
  that language's folder, with the translations TranslatePress has stored; a string without a translation stays in the
  default language." (With automatic translation on, it adds that TranslatePress may send untranslated strings to its
  translation service while the export renders each page.) Fix: Translate what should be translated in TranslatePress
  before exporting.
- **`comments`** (Comments). The comments area (form and the closed-comments line) is removed; existing comments are
  not exported. Fix: Nothing to do. Keep WordPress online if readers must still comment.
- **`password_posts`** (Password-protected posts). Password-protected posts are left out of the export. Fix: Remove
  the password to export a post, or leave it out.
- **`reveal_over_cap`** (Load more and infinite scroll past the cap). A loop with load more or infinite scroll
  pre-renders up to 100 items; past that, a See all link leads to separately exported pages. Fix: Nothing to do,
  unless you prefer plain pagination for a loop this large.
- **`popups_ajax`** (AJAX popups). AJAX popups are rendered into the page at export time, so they open without a
  request. Fix: Nothing to do.
- **`loops_in_templates`** (Paginated loops in templates). A paginated loop in a template is exported once per post
  the template applies to, with every one of its pages. Fix: Nothing to do. A larger page size keeps the export
  smaller.
- **`relative_dates`** (Relative dates). A relative date (3 days ago) is rendered once at export time and then stands
  still. Fix: Use an absolute date format where the age of a post matters.
- **`remote_fonts`** (Google Fonts). Google Fonts are copied into the export, so the static site never calls Google.
  Fix: Nothing to do. Switch Google Fonts local off in the export options to keep the Google links instead.
- **`avatars`** (Avatars). Avatars load from Gravatar on the static site too. Fix: Nothing to do, or switch the avatar
  off in the element.
- **`code_elements`** (Code elements that run PHP). Code elements run their PHP once at export time; the output is
  frozen in the page. Fix: Nothing to do, unless the code expects to run on every visit.
- **`pagination_ajax`** (AJAX pagination). AJAX pagination becomes plain links to pre-rendered pages. Fix: Nothing to
  do.
- **`pagination_main_on_page`** (Main query pagination on a page). A pagination element on a page points at the main
  query, which a page does not paginate; it exports as it renders. Fix: Give the pagination element the loop it
  belongs to.
- **`forms`** (Forms). "<n> forms found. To this WordPress site: <a>. To a service: <b>." Each place names the form's
  route ("to this WordPress site", "to a service, <host>", "to a service, no address"). Fix: Each form's destination
  is chosen in the builder, in the form's Static Export group. Forms to this WordPress site need the static site's
  origin under Destination; forms to a service need its endpoint URL. See `bse-forms-routes`.
- **`forms_reachability`** as a note: admin-ajax.php answered 400 or 200 from this server, as it should (visitors'
  browsers must reach the same address: no password, access rule or IP allowlist on it); or it could not be checked
  from the server, which on a host that runs one PHP worker does not mean visitors cannot reach it; or, during an
  export run, "Not checked during an export run."
- **`forms_captcha`** (Captcha hostname). For forms to this WordPress site: captcha keys are tied to host names, so
  the static site's host name must be on the key's domain list, or the captcha fails on the static site. For forms to
  a service: only a service that holds the captcha secret can verify the token; keep the widget only when it does.
  Fix: Add the static host name in the captcha provider's dashboard for the site key set under Bricks > Settings > API
  keys.
- **`destination`** (Destination). No destination base URL is set, so sitemaps and canonical links keep this site's
  address. Fix: Enter the static site's address under Destination.

## Reading a report

- Zero blockers means the export can start, not that the static copy behaves like the live site. Walk the owner
  through each warning and let them decide; most have two honest fixes (change the page, or accept the difference).
- A finding names where it was found. Open that page or template in Bricks to fix it; a fix in a template or a header
  changes every page that uses it.
- After a fix, run the preflight again and compare the counts.
