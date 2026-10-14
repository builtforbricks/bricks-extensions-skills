---
name: bxt-breakpoint-canvas
description: "Use when the owner asks about Bricks Extensions Breakpoint Canvas, the builder mode that shows every breakpoint of the page side by side: how to open it (the toolbar button, Shift+B), what the read-only mirrors show and leave out, click-to-select, the infinite canvas (pan and zoom), the bar, the Defaults (bxt_canvas), the Bricks line, and what is not built yet."
---

# Breakpoint Canvas

Read `bxt-start-here` first. Breakpoint Canvas shows every breakpoint of the page side by side in the Bricks builder,
refreshed as you edit. It is a builder feature for the person editing: no ability opens it, and it changes nothing
on the site. Switch it on or off under **Bricks Extensions > Extensions**.

## Opening it

In the builder, the toolbar button right after Bricks' refresh button ("Infinite Canvas (Shift + B)"), or **Shift+B**
when the focus is not in a field. The same button or "Close the canvas" leaves the mode and puts the builder back as
it was.

## What it shows

- Bricks' own canvas stays where it is and keeps working; Bricks' breakpoint switch and its Styles popup too.
- Under it, one **mirror** per non-base breakpoint that is switched on, with a label (name and width), scaled to fit.
  On a desktop-first site each mirror is as wide as the widest viewport where its breakpoint's rules apply; on a
  mobile-first site, as wide as Bricks itself previews that breakpoint.
- Mirrors refresh as you type: a changed element is replaced in every mirror; other changes trigger a short full
  refresh ("Refreshing the previews").
- **Click an element in a mirror to select it** in Bricks: the canvas highlights and scrolls to it and the panel opens
  it, so the next edit applies to it. Hovering outlines the element in the mirrors.

## What the mirrors are not

- **Read-only.** You cannot type, drag or drop into a mirror. Editable canvases arrive in 1.1, switched off by
  default; they are not in 1.0.
- **Copies of the canvas without scripts.** Sliders, interactions, animations and anything a script draws do not run
  in a mirror; links and forms inside never navigate. Check those on the front end at each width.
- Not the front end: they show what the builder canvas renders, without the builder's own styles and outlines.
- Nothing is saved from the canvas mode.

## Infinite canvas

The default layout, **Infinite canvas** (stored as `overview`), lays every breakpoint shown plus the base one side by side as page-tall frames on a
board, widest first, each labelled with its name and width. The real canvas stays loaded behind the board.

- Pan: scroll or two-finger scroll (Shift with a mouse wheel moves sideways), or drag the empty board, drag with the
  middle button, or hold Space and drag. Zoom around the pointer: Ctrl or Cmd and scroll, or pinch (10% to 200%).
- Shift+1 fits every frame, Shift+2 shows the selected element, Shift+0 is 100%; `+`, `-` and the arrow keys with the
  board focused; a double-click on a frame's label zooms to that frame. The keys never act while typing in a field
  and never take Ctrl or Cmd with `+`, `-` or `0` (the browser's zoom).
- Click an element in a frame to select it: Bricks also switches to that frame's breakpoint (the label shows
  "Editing"), so the next edit in Bricks' panels applies there, and every frame follows.
- Typing, dragging and Bricks' right-click menu inside a frame are not available: use Row or Grid, where the canvas is
  in view. Editing inside the frames is planned for 1.1, behind a switch.
- Viewport-height units (`vh`) in a frame are measured against 900 px, so a page-tall frame does not grow with them.

## The bar

The layout as three icons (Infinite canvas, a hand; Row, one row that scrolls sideways; Grid, wraps onto rows), the
zoom (minus, the percentage that resets to 100%, plus, Fit; Row and Grid stay between 20% and 100%), Synced scroll
(Row and Grid), Labels (the name top left, the width top right of each frame), one toggle per breakpoint, a help button
on the infinite canvas, and Close. The bar wraps onto more rows in a narrow window. The bar's
choices are kept for this browser only. While Bricks' preview mode is on, the bar and the mirrors or board step aside
so the page gets the whole canvas.

## The defaults

**Bricks Extensions > Breakpoint Canvas > Defaults** is what the mode opens with (option `bxt_canvas`):

| Label | Key | Default |
|---|---|---|
| Breakpoints shown | `breakpoints` | all (with every breakpoint off, all of them open) |
| Layout | `layout` | `overview` (Infinite canvas), or `row`, `grid` |
| Size | `fit` | fit the stage width (`width`), or `scale` for Fixed zoom |
| Fixed zoom | `scale` | 0.5, from 0.2 to 1 |
| Frame height | `frame_height` | 900 px, from 400 to 3000; the page scrolls inside it (Row and Grid; infinite canvas frames are as tall as the page) |
| Synced scroll | `sync_scroll` | on |
| Labels | `show_labels` | on |
| Editable canvases | `editable` | off, disabled: "Arrives in 1.1." |

## The Bricks line

The plugin verified the mirrors on Bricks 2.3.10 and 2.4.2 (the 2.3 and 2.4 lines). On an unverified line the
bar says "Bricks <version> is not verified yet. The mirrors are read-only and should work; check them against the
canvas." If Bricks no longer offers what the mirrors rely on, the button says "Canvas mirrors need a Bricks version we
have verified." and the mode does not open, rather than break the builder.

## Not measured

Safari and Firefox (no driver; pinch there is assumed to arrive as Chrome sends it), touch pinch, and memory over a
long infinite canvas session. Measured for the release candidate: builder windows of 1280 and 1366 px, right-to-left sites, mobile-first
sites, dragging from the elements panel and in the structure while the mode is open, Bricks' preview mode, and memory
over 30 minutes of edits in Row.
