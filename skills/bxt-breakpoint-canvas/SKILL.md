---
name: bxt-breakpoint-canvas
description: "Use when the owner asks about Bricks Extensions Breakpoint Canvas, the builder mode that shows every breakpoint of the page side by side: how to open it (the toolbar button, Shift+B), what the read-only mirrors show and leave out, click-to-select, the bar, the Defaults (bxt_canvas), the Bricks line, and what is not built yet."
---

# Breakpoint Canvas

Read `bxt-start-here` first. Breakpoint Canvas shows every breakpoint of the page side by side in the Bricks builder,
refreshed as you edit. It is a builder feature for the person editing: no ability opens it, and it changes nothing
on the site. Switch it on or off under **Bricks Extensions > Extensions**.

## Opening it

In the builder, the toolbar button in the breakpoints group ("Show every breakpoint side by side"), or **Shift+B**
when the focus is not in a field. The same button or "Close the canvas" leaves the mode and puts the builder back as
it was.

## What it shows

- Bricks' own canvas stays where it is and keeps working; Bricks' breakpoint switch and its Styles popup too.
- Under it, one **mirror** per non-base breakpoint that is switched on, each sized to the widest viewport where that
  breakpoint's rules apply, with a label (name and width), scaled to fit.
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

## The bar

Above the mirrors: Layout (Row, one row that scrolls sideways; Grid, wraps onto rows), Size (Fit width, or Fixed
zoom with Zoom), Synced scroll (scrolling the canvas scrolls every mirror to the same place), Labels, one toggle per
breakpoint, and Close. The bar's choices are kept for this browser only.

## The defaults

**Bricks Extensions > Breakpoint Canvas > Defaults** is what the mode opens with (option `bxt_canvas`):

| Label | Key | Default |
|---|---|---|
| Breakpoints shown | `breakpoints` | all (with every breakpoint off, all of them open) |
| Layout | `layout` | row |
| Size | `fit` | fit the stage width (`width`), or `scale` for Fixed zoom |
| Fixed zoom | `scale` | 0.5, from 0.2 to 1 |
| Frame height | `frame_height` | 900 px, from 400 to 3000; the page scrolls inside it |
| Synced scroll | `sync_scroll` | on |
| Labels | `show_labels` | on |
| Editable canvases | `editable` | off, disabled: "Arrives in 1.1." |

## The Bricks line

The plugin verified the mirrors on Bricks 2.3.10 and 2.4.2 (the 2.3 and 2.4 lines). On an unverified line the
bar says "Bricks <version> is not verified yet. The mirrors are read-only and should work; check them against the
canvas." If Bricks no longer offers what the mirrors rely on, the button says "Canvas mirrors need a Bricks version we
have verified." and the mode does not open, rather than break the builder.

## Not measured

Safari and Firefox, sites built mobile-first, builder windows narrower than 1440 px, right-to-left sites, dragging
into the canvas while the mode is open, Bricks' preview mode while open, and memory over a long session.
