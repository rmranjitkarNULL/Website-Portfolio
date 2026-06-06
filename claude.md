# Website Change Notes for Claude Code

## 1. Activity Dashboard Header
- Replace the current 2×2 activity grid in the banner with a full-width ticker/marquee bar pinned to the top of the page
- One horizontal row that slowly scrolls/rotates across the top showing: github, music (full song + artist name, not truncated), steam, and repos stats
- Style it like a dashboard — monospace font, teal accents, subtle separator dots or pipes between items
- Should feel like a live status bar, not a static grid

## 2. Clickable Name → About Page
- "ryan ranjitkar" on the homepage should link to `about.html`
- Add a subtle visual indicator so visitors know it's clickable — ideas: a small underline on hover, a faint `↗` icon after the name, or a soft teal glow/color shift on hover
- Maybe a tiny tooltip or label like "about me" that appears on hover beneath the name

## 3. Project Template Back Buttons
- Top button (in the back-bar just below the header): label it **"go back"** — uses `history.back()`
- Bottom button (in the `#three` section at the end of the page): label it **"also go back"** — also uses `history.back()`
- Keep the same minimal monospace button style already in place

## 4. Unified Contact Section Across All Pages
- Make the contact section identical on every page: `index.html`, `about.html`, and all project pages
- Use the project template style (icon + label + link) but **centered** horizontally
- Same three entries everywhere: email, linkedin, github
- Consistent spacing, font size, and color across all pages