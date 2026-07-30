---
name: add-library
description: Add a no-build JS/CSS library (e.g. anime.js, AOS) to one page of this static site via a script/link tag, scoped to only the pages that use it. Use when the user asks to "add", "integrate", or "wire up" a library/animation tool on this portfolio site.
---

# Add a library to this static site

This repo has no bundler and no `package.json` (see `/CLAUDE.md`). Every library
is added as a plain `<script src="...">` or `<link rel="stylesheet">` tag,
loaded only on the page(s) that actually use it.

## Steps

1. **Confirm a CDN or standalone build exists.** Check the library's site/docs
   for a `<script src="https://cdn...">` snippet or a downloadable single-file
   UMD/IIFE build. If the library is npm-only with no standalone build, stop
   and tell the user — don't reach for a bundler to make it work.

2. **Decide: CDN or vendored?**
   - Default to CDN (`<script src="https://cdn.jsdelivr.net/...">` or similar)
     for anything actively maintained — matches the existing Font Awesome CDN
     pattern in `index.html`.
   - Vendor into `assets/js/` (download the file, commit it) only if the user
     wants offline/no-CDN reliability, matching how jQuery and the HTML5UP
     plugins are already vendored there.

3. **Add the tag to exactly one page**, right before `</body>` (after the
   existing vendored scripts, if any), unless the user names multiple pages.
   Do not add it to a shared partial or every page "just in case."

4. **Write a single page-scoped init function**, e.g.:

   ```html
   <script src="https://cdn.jsdelivr.net/npm/animejs@.../lib/anime.iife.min.js"></script>
   <script>
     function initPCBAnimation() {
       // all state for this feature lives in this closure
       anime({ targets: '.pcb-trace', /* ... */ });
     }
     document.addEventListener('DOMContentLoaded', initPCBAnimation);
   </script>
   ```

   Put this inline on the page it belongs to (matching how this repo already
   inlines page-specific `<script>` blocks), not in `assets/js/main.js`. Name
   the init function for what it does (`initPCBAnimation`, not `init` or
   `setup`) so a second feature added later doesn't collide with it.

5. **Prefer `data-*` attributes over hardcoded selectors/values** for anything
   the user will want to tune per-element — e.g. `data-anim="trace"
   data-delay="200"` — and read those in the init function, rather than
   listing values inline in JS. Keep the whole config in one small object at
   the top of the init function rather than scattered through the code.

6. **If replacing/overlapping existing behavior** (e.g. the current
   `.reveal`/`.visible` scroll-reveal system), reconcile explicitly: either
   fully replace the old mechanism on that page, or clearly namespace the new
   one so both don't fire on the same elements. Never leave two systems
   silently competing for the same class name.

7. **Verify before reporting done.** Load the page (or ask the user to) and
   confirm the feature fires — a missing element, wrong script order, or
   selector typo is the most common failure and is cheap to catch by checking
   the browser console for errors.
