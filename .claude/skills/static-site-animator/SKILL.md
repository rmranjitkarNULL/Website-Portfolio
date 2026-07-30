---
name: static-site-animator
description: Use when adding or fixing scroll-reveal, entrance, or SVG "drawing" animations (e.g. PCB-trace style visuals) on a static HTML/CSS/JS site via Anime.js loaded from CDN. Covers a single initAnimations()/initPCBAnimations() entry point, data-* attribute driven configuration instead of hardcoded values, SVG-first techniques (stroke-dashoffset, transform, opacity), and reconciling a new animation solution with any existing .reveal/.visible scroll logic so triggers don't double-fire. Trigger for "add anime.js", "animate this SVG", "make it draw like a circuit board", "add scroll reveal", "AOS integration", "why doesn't my animation trigger". Assumes the add-library skill for CDN loading and per-page scoping mechanics.
---

# Static Site Animator

Covers the animation-specific parts of adding Anime.js/SVG/scroll-reveal behavior
to this static site. For loading the library itself (CDN vs vendored, which
page gets the `<script>` tag, the init-function-called-once pattern), use the
`add-library` skill — that mechanic isn't animation-specific and shouldn't be
duplicated here. Use `initAnimations()` or `initPCBAnimations()` as the entry
point name so it reads as an animation feature.

Also follow `/CLAUDE.md`'s diff-only, page-scoping, and drop-in-library rules
for how the change is applied to files.

## 1. State lives in one place

Keep animation instances, timelines, and any "has this already played" flags
in a single object (e.g. `window.__pcbAnimState`, or a module-scope `const
state = {}` if the script isn't otherwise global), not as loose variables
scattered across the file. This makes it possible to tear down / re-init
cleanly if the page needs it (e.g. on a soft page transition), and avoids
global side effects.

## 2. Data-driven configuration

Define *what* animates and *how* via a small config object plus `data-*`
attributes on the elements themselves, rather than hardcoding selectors and
values inline in JS.

```html
<path class="trace" data-animate="draw" data-duration="1200" data-delay="200"></path>
```

```js
const ANIMATION_CONFIG = {
  selector: '[data-animate]',
  defaults: { duration: 800, delay: 0, easing: 'easeInOutSine' },
};

function initPCBAnimations() {
  document.querySelectorAll(ANIMATION_CONFIG.selector).forEach((el, i) => {
    const kind = el.dataset.animate;
    const duration = Number(el.dataset.duration) || ANIMATION_CONFIG.defaults.duration;
    const delay = Number(el.dataset.delay) || (i * 100);
    if (kind === 'draw') animateDraw(el, { duration, delay });
  });
}
```

This keeps per-element tuning in the markup (where designers/content-editors
can adjust it) and keeps the JS generic and reusable across pages, instead of
one-off `anime({ targets: '.trace-3', ... })` calls per element.

## 3. SVG-first technique (PCB-style "drawing" visuals)

Prefer animating the SVG itself over CSS/JS position hacks:

- **Line/trace drawing**: animate `stroke-dashoffset` from the path's total
  length to `0`. Get the length with `path.getTotalLength()`, set
  `stroke-dasharray` to that length, then animate `stroke-dashoffset`.
  ```js
  function animateDraw(path, { duration, delay }) {
    const length = path.getTotalLength();
    path.style.strokeDasharray = length;
    path.style.strokeDashoffset = length;
    anime({
      targets: path,
      strokeDashoffset: [length, 0],
      easing: 'easeInOutSine',
      duration,
      delay,
    });
  }
  ```
- **Node/pad "pulse"**: animate `opacity` and/or `transform: scale()` on the
  group, not on individual style properties that force layout.
- **Convert flat shapes into animatable paths**: if a "track" is currently a
  `<rect>` or a raster image, convert it to a `<path>` (or group of paths)
  first — dasharray/dashoffset tricks only work on path-like elements (`path`,
  `line`, `polyline`, `circle`).
- Keep transforms on `transform`/`opacity` wherever possible
  (compositor-friendly) rather than animating `width`/`height`/`top`/`left`,
  which force layout on every frame.

## 4. Reconciling with existing scroll-reveal logic

Before adding a new reveal-on-scroll solution (Anime.js-driven, or AOS), check
whether the page already has a `.reveal`/`.visible` IntersectionObserver (or
scroll-listener) pattern. Two systems watching the same elements causes
double-triggering or fighting animations.

- **Decide: replace or adapt — never both.**
  - *Replace*: if the existing logic is simple (a basic IntersectionObserver
    toggling `.visible`), swap it for the new system entirely and remove the
    old listener/class toggle.
  - *Adapt*: if the existing logic is load-bearing elsewhere (other CSS
    depends on `.visible` for non-animation reasons), keep the existing
    observer as the trigger and hook the new animation into the same
    class-toggle callback, rather than adding a second observer on the same
    elements.
- **If introducing AOS**: don't run it alongside a custom observer targeting
  the same elements. Either AOS owns all `data-aos` elements, or the existing
  custom system does — pick one per element/section, not both.
- **Guard against double-fire**: whichever system you keep, make sure the
  "already animated" state is tracked (a class, a data attribute, or a Set of
  animated element refs) so scrolling back up and down doesn't re-trigger a
  one-shot entrance animation, unless the user explicitly wants a
  repeat-on-scroll effect.

## Checklist before shipping an animation change

- [ ] Library loaded and scoped per `add-library`
- [ ] Exactly one `initAnimations()`/`initPCBAnimations()` call, guarded
      against double-init, after `DOMContentLoaded`
- [ ] Animation state kept in one object, not scattered globals
- [ ] What-animates-and-how driven by `data-*` + a config object, not
      hardcoded per element
- [ ] SVG animations use `stroke-dashoffset`/`transform`/`opacity`, not
      layout-triggering properties
- [ ] Existing `.reveal`/`.visible` logic explicitly replaced or adapted —
      not left running in parallel with the new system
- [ ] Output is a diff/changed-block, scoped to the page(s) in question, per
      `/CLAUDE.md`
