# ADR-0003: Multi-ZIP Comparison Table via Client-Side State Map

**Date:** 2026-05-13
**Status:** Accepted

## Context
The original ZIP demographics tool was single-purpose: one lookup at a time, replacing all output on each request. Users wanting to compare demographics across multiple ZCTAs (e.g., comparing neighborhoods or cities) had to mentally track previous results or open multiple tabs. The single-card output model also meant any error would destroy the current result.

## Decision
Replace the single-lookup, single-card rendering model with a persistent, multi-ZIP comparison table backed by an insertion-ordered `Map`. Key design choices:

- **`Map<zip, {state, raw?, error?}>`** as the single source of truth. All rendering is derived from this map, making add/remove operations trivially composable and the UI deterministically reproducible from state.
- **Optimistic UI with per-cell status**: Zips are added to the map immediately in a `loading` state and the table re-renders before the fetch resolves. Each cell independently shows `loading`, `err`, or a formatted value — partial failures don't block the rest of the comparison.
- **Declarative `ROWS` array**: Row definitions are extracted into a static config array, each holding a label and a `render(getter)` function. This decouples the table structure from both the data-fetching logic and the DOM manipulation, making it trivial to add or reorder rows.
- **Non-destructive error surface**: Errors flash in a transient dismissible bar (`flashError`) rather than replacing table content, preserving the comparison state on bad input.
- **Remove per-column and "Clear all"**: Removal is O(1) via `Map.delete()` followed by a full re-render, which is acceptable given the small number of ZIPs a user would realistically compare.

The HTML is still generated via template literals and `innerHTML`, consistent with the existing pattern — no framework dependency is introduced.

## Consequences
- **Benefit:** Users can build side-by-side comparisons incrementally without losing previous results.
- **Benefit:** State isolation between ZIPs means a failed Census API call for one ZIP doesn't degrade others.
- **Risk:** Full DOM replacement on every `render()` call loses focus and any in-flight animations; acceptable at this scale but would not suit large datasets.
- **Risk:** Duplicate-ZIP silently no-ops (input cleared, no feedback), which could confuse users.
- **Limitation:** State is ephemeral in-memory; a page refresh clears all ZIPs (only the API key persists via `localStorage`).

## Files Changed
- **`zip/index.html`**: All changes are contained here. CSS additions for `.compare-wrap`, `.compare`, `.key-steps`, and `.key-note`; HTML restructured to add the comparison table shell and "Clear all" control; JavaScript refactored from `lookup()`/`render(zip, raw)` to `addZip()`, `removeZip()`, `clearAll()`, and a stateful `render()` driven by the `entries` Map.