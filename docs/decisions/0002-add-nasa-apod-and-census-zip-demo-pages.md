# ADR-0002: Introduce Standalone Demo Pages as Portfolio Sub-Applications

**Date:** 2026-05-12
**Status:** Accepted

## Context
The portfolio site needed a way to demonstrate practical API integration skills beyond static content. Two public APIs — NASA's Astronomy Picture of the Day (APOD) via `api.data.gov` and the U.S. Census Bureau's ACS API — were identified as good targets for small, self-contained learning exercises. The challenge was how to surface these demos without overcomplicating the existing single-page portfolio structure.

## Decision
Each demo is implemented as an independent, zero-dependency HTML page living in its own subdirectory (`/apod/`, `/zip/`), served via directory-index routing. No build tooling, frameworks, or shared component system is introduced. Each page:

- **Owns its full CSS** using a shared design token vocabulary (CSS custom properties) duplicated across files rather than extracted into a shared stylesheet.
- **Fetches directly from public APIs** using the browser's native `fetch`, with graceful fallback to `DEMO_KEY` for NASA's API when no personal key is configured.
- **Uses vanilla JS only**, keeping the pages portable and auditable with no npm dependency surface.

The main portfolio page links to the demos via inline anchor tags in the contact section, framed as "currently tinkering" — positioning them as living experiments rather than finished products.

## Consequences

**Benefits:**
- Zero deployment complexity — no bundler, no SSR, no CI changes needed.
- Each page is fully self-contained and independently deployable or shareable.
- Consistent visual identity is maintained through duplicated design tokens without requiring a build step.

**Risks & Trade-offs:**
- CSS design tokens are duplicated across both demo pages and will drift from each other (and from the main site) over time with no enforcement mechanism.
- The hardcoded `API_KEY = ''` placeholder means the NASA demo silently degrades to a rate-limited `DEMO_KEY`; a misconfigured key produces a runtime error rather than a build-time warning.
- No HTML templating means the shared topbar and footer markup must be manually kept in sync across pages.
- The Census ZIP page receives no API key handling at all, relying entirely on the Census Bureau's unauthenticated endpoint, which may break if rate limits are hit.

## Files Changed
- `apod/index.html` — New NASA APOD viewer; date picker UI, `fetch` against `api.nasa.gov`, renders image or iframe for video media type.
- `zip/index.html` — New Census ZIP demographics lookup; calls ACS API, renders demographic stat cards.
- `index.html` — Added "currently tinkering" links in the contact section pointing to both demos.
- `.gitignore` — Added `.idea/` and `.DS_Store` exclusions.