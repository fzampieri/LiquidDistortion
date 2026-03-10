# Copilot Instructions for Liquid Distortion Slideshow

This project is a **static front‑end demo** featuring a liquid‑distortion image
slideshow built with PixiJS and GSAP. There is no build pipeline, packaging,
backend, or tests. The entire application lives in HTML/CSS/JS files; `main.js`
is the heart of the logic.

## Big Picture Architecture

1. **HTML demos** – there are five entry points (`index.html` .. `index5.html`).
   Each page is a slightly different demonstration of the same slideshow.
   - Images live under `img/` and are referenced directly in the markup.
   - Each demo collects `.slide-item__image` elements in an inline script and
     passes their `src` attributes into a `new CanvasSlideshow({...})` call.
   - Variation between demos is achieved by changing the options object and/or
     the HTML structure (e.g. extra text, different displacement maps).

2. **Core JavaScript** – `js/main.js` defines `window.CanvasSlideshow`.  This
   constructor handles:
   - defaulting and normalising options (stage size, sprites, autoplay, etc.)
   - initialising PixiJS (`renderer`, `stage`, `slidesContainer`,
     `displacementFilter`)
   - loading images into PIXI sprites, optionally centring and adding text
   - the animation ticker and transitions driven by GSAP (`TimelineMax`)
   - navigation event handlers wired to elements with `.scene-nav`

   External libraries are vendor files in `js/` and are loaded with `<script>`
   tags in the demos.  The order matters: `pixi.min.js` → `TweenMax.min.js`
   → `main.js` → `imagesloaded.pkgd.min.js`.

3. **Styles** – `css/normalize.css`, `demo.css`, and `component.css` provide the
   visual styling common to all demos.  `pater/pater.css` styles the affiliate
   banner.  Modify these files when adjusting layout or responsive behaviour.

## Developer Workflows

- **Running locally:** there is no build step.  Serve the directory via a simple
  HTTP server so that image loading works correctly.  For example:
  ```bash
  cd /project/workspace
  python3 -m http.server 8000    # or `npx serve` etc.
  # then open http://localhost:8000/index.html
  ```
- **Editing:** change HTML/JS/CSS directly.  After edits, refresh the browser.
- **Adding a new demo:** duplicate an existing `indexX.html`, adjust the
  markup/options, and update navigation links.
- **Debugging:** open DevTools in the browser; breakpoint in `main.js`
  (the `CanvasSlideshow` methods are the most important).  Console logs and
  network panel help inspect asset loading.

## Project‑Specific Conventions & Patterns

- **Option object** – the constructor relies on `options.hasOwnProperty` checks
  with sensible defaults. When introducing new configuration, follow the same
  pattern and update all demo pages.
- **Global names** – `CanvasSlideshow`, `spriteImagesSrc`, `initCanvasSlideshow`
  etc. are globals.  Keep new public APIs off the `window` unless necessary.
- **Demo structure** – each demo page includes SVG symbols, navigation markup,
  and the slideshow container.  Reuse the same class names when adding
  features to avoid breaking CSS selectors.
- **Image arrays** – the slideshow expects an array of URLs (`options.sprites`).
  The demo code collects these from the DOM but you can also pass a static
  array if you build the page dynamically.
- **Displacement maps** live in `img/dmaps/`; sizes (`2048x2048` vs `512x512`)
  influence performance.  Changing the map requires updating the `displacementImage`
  option in the demos.

## Integration Points & Dependencies

- No external services: everything is shipped locally.
- The only runtime dependencies are PixiJS, GSAP (TweenMax), and imagesloaded.
- CSS includes normalize and component styles from the original Codrops template.
- All JavaScript interactions are client-side; there is no AJAX or fetch calls.

## When Writing Code for This Repo

- **Be explicit with paths** – relative URLs in demos need to be correct when
  the site is served from the filesystem or a simple web server.
- **Maintain the order of script tags** since the slideshow relies on globals
  provided by earlier files.
- **Respect the demo API** – new features should be toggleable via options,
  so existing demos don’t break.
- **Keep copying style** – if you modify one demo, replicate the change
  across the others for consistency, or document why a variation exists.
- **Avoid adding build tooling** unless the project is intentionally being
  upgraded; keeping it simple is part of the design.

> ⚠️ There are no automated tests or linting configurations.  Use the browser
> console and manual inspection to confirm behaviour.

---

If any of the sections above are unclear, or if you notice missing patterns
(e.g. unmentioned CSS selectors or JS helpers), please let me know so I can
adjust the guidance.