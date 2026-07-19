# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-page static film photography portfolio site. There is no build system, package manager, server, or test suite — the entire site is `index.html` (HTML + CSS + vanilla JS, no dependencies) plus a flat `images/` directory of JPEGs.

## Running / previewing

Open `index.html` directly in a browser, or serve the directory locally, e.g.:

```
python3 -m http.server 8000
```

There is no build, lint, or test command — none exist in this repo.

## Architecture

Everything lives in `index.html`:

- **`<style>`** — dark-theme CSS for header, sticky filter bar, responsive photo grid, hover overlays, and a click-to-open lightbox.
- **`photos` array** (in the `<script>` block) — the single source of truth for every photo. Each entry is a plain object with `title`, `src` (relative path into `images/`), and tag fields: `iso`, `film`, `year`, `season`, `subject`, `style`, `location`, `editing`. Tag fields other than `iso`/`film`/`year`/`editing` may be a single string **or an array of strings** (e.g. `subject: ["Dogs", "Water"]`) when a photo fits multiple categories.
- **`toArr()`** — normalizes any tag field (string or array) to an array; used everywhere tag values are read so multi-value and single-value fields are handled identically.
- **`buildGrid()`** — renders one `.photo-card` per entry in `photos`, stores each tag as a `data-*` attribute (multi-value fields are JSON-stringified into the dataset), and wires the click handler that opens the lightbox for that index.
- **`applyFilters()`** — reads the current value of each `<select>` filter and shows/hides cards by matching `dataset` values (exact match for single-value fields, `includes()` via `matchMulti()` for multi-value fields). Updates the visible photo count.
- **`openLightbox()` / `closeLightbox()`** — full-screen image view with flattened tag chips; closes on Escape, backdrop click, or the close button.
- **`shuffleArray()` + `buildGrid()` at the bottom** — photos are shuffled into random order on every page load, then rendered and filtered.

## Adding photos

1. Drop the image file into an existing or new subfolder under `images/` (folders are conventionally named `<year><season><FilmStock><ISO>`, e.g. `images/2026SpringKodakGold200/`).
2. Add a corresponding entry to the `photos` array in `index.html`, setting `src` to the relative path and filling in the tag fields. Use an array instead of a string for any tag field where more than one value applies.
3. Keep filter `<select>` options (in the `.filters-bar` markup) in sync — if a new tag value is introduced (new film stock, location, subject, etc.), add a matching `<option>` to the relevant `<select>` or it won't be selectable as a filter even though matching photos will still render.

## Tagging Conventions

These are subjective judgment calls to apply when tagging new photos, since they aren't inferable from the code:

- **Framing** (style): Only use when the subject is physically boxed in or surrounded by something in the image itself — a window, archway, plants, rock faces, tree canopy, etc. Do NOT use it just because the composition/crop is well-balanced or intentional.
- **Black&White** (style): Apply to all photos shot on Ilford or other B&W film stock. Also apply to any photo that is visibly black and white, regardless of film stock.
- **Dogs vs Animals** (subject): Separate tags. Use both together (["Animals", "Dogs"]) for dog photos if they should appear under both filters.
- **Portrait** (style, per live site convention): Used when a person is the clear primary subject of the photo.
- **Climbing** (subject): Used for bouldering/climbing action shots, even without a visible person (e.g. crash pads alone).
- **Nature** (style): Indicates the photo's aesthetic is primarily focused on natural elements, textures, or organic subjects.
- **Macro** (style): Tight close-up shots where the subject fills most of the frame.
- If a photo's subject doesn't fit any existing category, leave the subject field as "" rather than forcing a category.
- Titles should be descriptive but concise. Location-specific titles are encouraged when the place is identifiable (e.g. "Bridal Veil Falls", "Baden-Baden Panorama Trail"). Two photos can share the same title.

## Notes

- No image optimization/build pipeline exists — files under `images/` are served as-is; each subfolder's `placeholder.txt` is a leftover/placeholder artifact, not functional.
- All photo metadata (titles, locations, tags) is hand-authored directly in `index.html`; there is no external data file or CMS.
