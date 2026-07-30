# Website-Portfolio

Ryan Ranjitkar's personal portfolio site, deployed via GitHub Pages. Based on the "Forty" template by HTML5 UP.

## Stack (do not assume otherwise)

- **No framework.** Plain hand-authored HTML. No React/Next/Astro/etc.
- **No build tool.** No `package.json`, no bundler, no npm scripts. `assets/sass/` is leftover template source and is NOT compiled by anything in this repo — `assets/css/main.css` is the real, hand-edited stylesheet actually linked from HTML. Don't "fix" main.css by trying to recompile from sass.
- **No JS framework.** jQuery + a few HTML5UP plugin scripts (`assets/js/`) on template-derived pages, plus vanilla JS `<script>` blocks (often inline in the HTML) for custom features.
- **Routing is just files.** Each page is its own `.html` file; navigation is plain `<a href>`. There is no router.
- **Styling is plain CSS + inline `<style>` blocks per page.** Not Tailwind, not CSS Modules.

## Rules for changes in this repo

**Never introduce a build step.** No bundler, no `package.json`, no npm dependency, no framework migration — even if it would objectively make something easier. Any new library gets added via a `<script src="...">` or `<link>` tag only, either pointing at `assets/js/` / `assets/css/` (vendored file) or a CDN. If a library only ships via npm with no standalone/CDN build, it's not usable here — say so instead of reaching for a bundler.

**Keep edits diff-sized.** Don't reprint or rewrite an entire HTML/CSS file for a small change. Make the smallest edit that accomplishes the request. If a change genuinely requires touching most of a file, say so before doing it rather than defaulting to a full rewrite.

**Change one page at a time unless told otherwise.** `index.html`, `aboutme.html`, and each `projects/proj*.html` are independent documents with their own inline `<style>`/`<script>` blocks. A fix or feature requested for one page should not be silently propagated to the others — if something looks like it should apply everywhere, ask first or call it out, don't do a global sweep unprompted.

**Load new libraries conditionally, per-page.** Don't add a `<script>`/`<link>` to every page just because one page needs it. Add it only to the page(s) that use it. If a library needs setup/init code, put that init in a page-scoped inline `<script>` block (e.g., a single `initX()` called after DOMContentLoaded on that page only), not in the shared `assets/js/main.js` / `assets/js/util.js`, unless the feature is genuinely site-wide.

**Respect existing CSS specificity and naming.** This is HTML5UP template CSS with heavy custom overrides layered on top (see the inline `<style>` blocks in `index.html` for the current red-accent/dark-neutral theme — teal/`#0f0f12` are stale, don't reintroduce them). Don't restructure selectors or introduce a new CSS methodology (BEM, utility classes, etc.) to fix a small issue. When moving a repeated inline style into `assets/css/main.css` or a page-specific stylesheet, keep the visual output pixel-identical and don't rewrite unrelated selectors while you're in there.

**When something doesn't work (animation doesn't fire, style doesn't apply, etc.), diagnose narrowly before editing.** Check the specific selector, timing (DOMContentLoaded / script order), and CSS specificity conflict first. Apply the smallest fix that addresses the actual root cause — don't restructure surrounding code speculatively.

## Current visual identity (as of 2026-07-30)

Dark, neutral flat background (`#171717` family, not the old near-black/teal scheme), muted red accent (`#e2686b` / `#c14a4d`, Monkeytype-inspired), Space Grotesk font, lowercase prose. See `assets/css/main.css` and `index.html`'s inline `<style>` block for the live values — don't hardcode colors from memory, read them.
