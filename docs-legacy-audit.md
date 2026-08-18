# Legacy content audit — InnoPick Manual (Ozarks)

Generated 2026-08-18. Every path below was re-verified with a fresh grep across all `docs/**/*.mdx` files and confirmed to have **zero references**, and confirmed to still exist on disk. Nothing has been deleted — this is a list for manual review.

This repo shows signs of having been cloned from an earlier "Suncoast" customer's InnoPick manual: the docs content was rebranded to Ozarks, but some screenshots/logos from that original site were never cleaned out. (The `package.json` name and the GitHub Pages deploy base path had the same issue — those have already been fixed to `innopick-manual-ozarks` / `/InnoPickUserManual_Ozarks/`.)

## Unreferenced screenshots (safe to delete — 27 files)

None of these appear in any `<img src="...">` tag anywhere in `docs/`.

**`docs/main-screens/`**
- `image.png` — stray file sitting next to `image-1.png` (which *is* used in `home-page.mdx`)

**`docs/images/home/`**
- `automode.png`
- `hoverinfo5.png`
- `image6.png`, `image8.png`, `image9.png`, `image11.png`, `image17.png`, `image18.png`, `image19.png`
- `outfeedcase5.png`, `outfeedcase7.png`
- `pauseinfeed1.png`
- `rdy2transfer1.png`, `rdy2transfer2.png`
- `waitingreplen1.png`, `waitingreplen7.png`

**`docs/images/administration/`**
- `buffer3.png`
- `buffer4.png`

**`docs/images/inventory/`**
- `image26.png`, `image27.png`, `image29.png`, `image30.png`, `image37.png`

## Unused logo/vendor files (3 of 4 files in `docs/images/logos/`)

Only `image1.png` (the InnoPick logo shown on the homepage) is referenced. The other three look like true cross-customer leftovers, not Ozarks-specific content:

- **`image2.png`** — warehouse photo of beverage cases carrying a different customer's branding ("Hop Valley IPA" / a red "R" mark) — not Ozarks Coca-Cola product.
- **`image3.jpeg`** — "NūMove Robotics & Vision" vendor logo.
- **`image4.png`** — "DRL Systems" (integrator) logo.

## Flagged for your judgment (not touched)

These aren't clear-cut deletions — they need a decision from someone who knows the current Ozarks setup:

1. **`docs/index.mdx` title inconsistency** — the page banner (line 3) reads "InnoPick Manager **Operations** Manual" but the welcome paragraph (line 18) calls it the "InnoPick Manager **Technical** Manual." Worth picking one name.
2. **"MixMaster" references** — appears repeatedly as "MixMaster (or equivalent WMS/WCS)" in `home-page.mdx`, `inventory-section.mdx`, `case-sequence.mdx`, `common-issues.mdx`, `user-manager.mdx`, and `alert-guidelines.mdx`. This may be intentional generic terminology, or it may be a holdover from the Suncoast site's actual WMS — worth confirming it's still correct for Ozarks.
3. **`to be added/` folder** (repo root) — `image (107).png`, `image (108).png`. Not legacy, but staged/unintegrated content sitting outside `docs/` that nothing currently links to.

## Already fixed (Suncoast config leftovers)

- `package.json` (and `package-lock.json`) name: `suncoast-mixmaster-docs` → `innopick-manual-ozarks`
- `.github/workflows/deploy.yml`: GitHub Pages base path `/InnoPickUserManual_Suncoast/` → `/InnoPickUserManual_Ozarks/` (this mismatch would have broken deployed asset paths)

## Out of scope

The ~50 images currently modified/added/deleted in your uncommitted working tree (the in-progress screenshot refresh) were left untouched — that's active work, not legacy content.
