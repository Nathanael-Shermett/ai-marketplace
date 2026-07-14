---
name: portal-branding
description: Replicate a brand or existing website's design on Kong Konnect Dev Portal pages. Use to match colors, fonts, imagery, and spacing through MDC components and page-level styling for visual parity.
license: MIT
metadata:
  product: konnect
  category: dev-portal-branding
  tags:
    - kong
    - konnect
    - dev-portal
    - branding
    - styling
---

# Konnect Dev Portal page branding

## Goal

Reproduce a brand or an existing website's look on Dev Portal MDC pages: colors,
fonts, imagery, and spacing, expressed through MDC components and page-level
styling. Work in MDC page files, locally or in a dedicated repository. Do not
push destructive changes to a live portal through the Konnect API unless the
user explicitly asks.

## Clarify First

Batch two or three high-impact questions with a sensible default to confirm; do
not interrogate. Clarify:

- the source to match, as a URL or a short brand description
- which page or pages are the target
- which details must be exact and which can be approximate
- whether the base should read light or dark

## Tool Selection

- Author, format, validate, and preview MDC with the `portal-page-design`
  toolchain, which relies on the Konnect MCP server for verified components,
  design tokens, validation, and preview. This skill adds the brand-specific
  steps below.
- If the MCP server is not connected, recommend installing and connecting it.
  Without it you cannot render a preview or run the parity loop, so work from
  general MDC knowledge and the user's existing files, ask the user to preview in
  the Portal Editor, and say plainly that the result is less reliable.
- Capturing the source and comparing the result need a browser. If no browser
  automation such as Playwright or an agent browser is available, recommend the
  user install one first.
- If a local preview extension is available, use it for live local preview while
  editing.

## Workflow

1. Confirm the source (a URL or brand cues) and the target MDC page.
2. Capture the brand cues (see Capture the brand).
3. Build the page's look with components and page-level styling (see Apply the
   brand).
4. Preview the page and compare it to the source across widths, then iterate
   (see Parity loop).
5. Check contrast so text stays readable against every background.
6. Report what matched. For anything the page cannot express, offer a concrete
   alternative instead of a silent miss.

## Capture the brand

Aim for a small set of concrete cues, not a pile of screenshots. Capture:

1. Brand and accent colors: the dominant non-neutral colors on primary buttons,
   links, and highlights. Record hex values.
2. Base tone: whether the design reads light or dark.
3. Typography: the heading, body, and monospace fonts, plus the weights actually
   used.
4. Imagery: logos, icons, and hero images with their source URLs. Prefer SVG
   when offered.
5. Spacing and shape: whether the layout is dense or airy, and the corner-radius
   and border feel.

Screenshot the source with Playwright at desktop and mobile widths and keep it
open beside your work. Match the source you were given; do not substitute a
default brand.

## Apply the brand

Two levers:

- Portal theme: anything portal-wide (brand color, fonts, layout, logo,
  favicon), set through the MCP server's customization operations. The `primary`
  token family is generated from the theme's primary color, so setting it once
  applies the brand consistently. Change the theme only when the user asks.
- Page-level styling: one-page shades and effects beyond the primary palette,
  such as a `full-width` hero background.

For one page, use a component's dedicated appearance props (`background-color`,
`padding`, `border`, `border-radius`) and inline span styles, for example
`[text]{ style="color:#6f28ff;" }`. Prefer dedicated props over a catch-all
styles prop, which is only for what props cannot express, such as a gradient
background.

Set fonts portal-wide through the theme so every page matches. For a one-page
exception, set `font-family` on component style props with a sensible fallback
stack.

## Parity loop

Run this only with a real portal and a configured token. Without them there is
no rendered preview and no parity loop, so say so and stop.

1. Format and validate the MDC, look up the target portal's origin (its
   canonical or default domain), and generate a preview through the MCP server.
2. Set the viewport before opening the URL, wait for full hydration (network
   idle, no pending animations), then screenshot the preview and the source at
   the same widths (mobile, tablet, and desktop).
3. Compare in order: base tone, brand colors, fonts, spacing and shape, then
   fine detail.
4. Fix the largest visible gap first, then repeat. Drive changes from the
   screenshots, not from assumptions about the styles.

When editing an existing page rather than building fresh, first screenshot the
live page as a "before" at each width, then compare it against the "after."

Preview URLs are single-use and time-limited. Regenerate one only when the MDC
changes; once a page is loaded, resize the browser in place to test widths.

The preview uses the target portal's theme, so `--kui-*-primary` surfaces show
that portal's brand color, not the source's. To judge brand parity, either set
the portal's primary color in the theme first, or use explicit hex on
brand-critical surfaces so the preview is theme-independent.

Contrast: confirm brand color against its background on buttons and links, and
body text against its background. Meet WCAG AA: at least 4.5:1 for normal text
and 3:1 for large text. If the true brand color fails, use it for large surfaces
and pick a contrast-safe variant for text.

When an element has no page equivalent, do not drop it silently: offer the
closest achievable approximation, or state plainly that the page cannot express
the effect.

Stop when tone, colors, fonts, and imagery match and the remaining differences
are cosmetic and below the user's fidelity bar. Report the residual differences
rather than chasing pixels.

## Dev Portal Gotchas

- Pull `--kui-*` values from the server's design tokens, not memory. Use
  explicit hex only for shades beyond the primary palette.
- Logo, favicon, and API images have dedicated upload operations in the MCP
  server. There is no general uploader for arbitrary inline content images;
  hotlink those over HTTPS with an image component, or embed SVG markup inline.
- The portal's internal class names are not a stable contract; use component
  props and inline styles.
- Style only what the brand needs, with the smallest set of theme settings and
  styles that gets there.

## Validation Checklist

Before finishing, confirm:

- the source and the target pages
- which colors, fonts, imagery, and spacing you applied, and how
- that logo, favicon, and API images use the server's upload operations, and
  inline content images are hotlinked or embedded SVGs
- that text and buttons meet WCAG AA contrast against every background
- how parity was confirmed, including a preview screenshot comparison
- that the work stayed in MDC page files
- which elements could not be matched, and the alternatives offered

## Handoffs

- Use `portal-page-design` for page structure and component choice.
- Use `technical-writing` for the wording of the page.
- Use `terraform-konnect` or `kongctl-declarative` to encode pages as code.
- For portal-wide customization (theme, navigation menus, custom domain, page
  visibility), use the MCP server's portal customization operations directly. No
  page-scoped skill here owns that workflow.
- Use `konnect-api-publish` or `konnect-app-auth` when the real issue is API
  visibility, publication, or developer application auth.
