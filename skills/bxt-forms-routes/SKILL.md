---
name: bxt-forms-routes
description: "Use when a Bricks form must keep working on a site exported with Bricks Extensions Static Export: choosing per form between This WordPress site and Another service, the form's Static Export group in the builder (bxtExport* settings), static origins and the WordPress address for forms, Check forms, and what each route needs and cannot do."
---

# Static Export: where each form sends

Read `bxt-start-here` first. On a static page there is no WordPress to answer a form. Each Bricks form on an exported
page chooses, in the builder, one of two destinations. There is never an endpoint hosted by Built for Bricks.

## The form's Static Export group

On Bricks' form element, Content tab, group **Static Export** (after Bricks' own form groups). Bricks keeps the
values in the element's settings, so they travel with the page and change through Bricks like any form setting.

| Label | Setting | Values |
|---|---|---|
| Destination | `bxtExportRoute` | `origin`, "This WordPress site" (default) or `endpoint`, "Another service" |
| Endpoint URL | `bxtExportEndpoint` | the service's address, http or https (Another service only) |
| Encoding | `bxtExportEncoding` | `form`, "Form fields" (default) or `json`, "JSON" (Another service only) |
| Field names | `bxtExportNames` | `simple`, "Simple keys (full_name)" from each label (default), or `labels`, "Labels as written" (Another service only). A field's own custom name, when set, is sent as is. |
| Success message | `bxtExportSuccess` | empty: the form's own success message (Another service only) |
| Error message | `bxtExportError` | default "Sorry, your message could not be sent. Please try again later." |
| My service verifies captcha tokens | `bxtExportCaptcha` | keeps the captcha widget on the Another service route |

With nothing set, a form takes This WordPress site. Another service without a usable http(s) endpoint URL falls back
to This WordPress site, and the preflight warns (`forms_endpoint_missing`).

## This WordPress site (`origin`)

The static page posts to this WordPress install's `admin-ajax.php`, and Bricks runs the form exactly as it does on
the live site: its actions (email, webhook, newsletter, create post, redirect), the honeypot and the captcha check.

- **Needs** WordPress online and publicly reachable at `/wp-admin/admin-ajax.php`, at the **WordPress address for
  forms** (Destination card, `bxt_static_export.forms_origin_url`; this site by default; another address when the
  export is made from a staging copy). No password, access rule, redirect or IP allowlist on that address.
- **Needs** the static site's origin, so WordPress accepts the post: the **Base URL**'s origin counts on its own;
  other addresses go in **Static origins** (`bxt_static_export.static_origins`), one per line, exact origins such as
  `https://www.example.com` or one wildcard level such as `https://*.preview.example` for preview deploys. Only the two
  Bricks form actions answer those origins; nothing else on WordPress changes.
- **Needs** a re-export after the form's fields change.
- A redirect to a page of this site lands on the same page of the static site.
- Captcha keys are tied to host names: the static site's host name goes on the key's domain list.
- When WordPress cannot be reached, the form shows Bricks' error markup with the Error message, never a silent
  failure.

## Another service (`endpoint`)

The static page posts the fields to the Endpoint URL; WordPress is not involved and nothing is added to it.

- Field names are simple keys (`full_name`) or the labels as written; a field's own custom name wins. Encoding is
  form fields or JSON.
- The honeypot is renamed `_gotcha`; a filled honeypot shows the success message and sends nothing.
- The form shows Bricks' own success and error markup and fires Bricks' form events. Without JavaScript it still
  posts to the endpoint as a plain form.
- **Runs only what the service does**: Bricks' form actions do not run. Spam filtering beyond the honeypot is the
  service's job, and the captcha widget stays only when **My service verifies captcha tokens** is ticked.
- `{{field}}` tokens in a success message cannot be filled on a static page.

## Check forms

Under Destination, **Check forms** posts from WordPress to itself as each static origin and confirms the cross-origin
header, a fresh form nonce and the preflight answer for each. "Not checked ... the server could not reach itself" is
a host that cannot call itself during a request (often a one PHP worker host); it does not mean the form fails: test
it from the static site. Check forms does not post to services.

## Choosing, with the owner

1. Run the preflight; the `forms` row lists every form with its route.
2. For each form ask: must Bricks' actions run (the site's email, a webhook, a created post)? Then This WordPress
   site, and WordPress stays online. Is WordPress going away, or must the static site stand alone? Then Another
   service, with its endpoint URL from the owner.
3. Change the Static Export group through Bricks, on the owner's word. Then set the static origins under Destination
   for the WordPress-site forms, save, and run Check forms.
4. Re-export, then the owner submits each form once from the static site. Only that proves a route.

## Not measured

Forms inside loops, components, global elements or popups, file uploads, a page with one form on each route, and
real captcha keys and real services were not measured by the plugin's checks. Say so when a form is one of these.
