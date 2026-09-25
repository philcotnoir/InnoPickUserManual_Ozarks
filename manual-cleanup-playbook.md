# InnoPick Manual — Template Cleanup & Cross-Site Audit Playbook

## Why this exists

Each site's InnoPick manual repo is cloned from a prior customer's manual and rebranded. That leaves two kinds of residue behind:

1. **Leftover content from the source site** — orphaned images, unused logos, stray brand/customer names in text, and (less obviously) baked-in UI data inside screenshots that still reflects the old site.
2. **Drift between text and screenshots** — even after screenshots get refreshed for the new site, the surrounding prose often doesn't get updated to match (e.g. a heading says "Level 1 to 5" next to a screenshot that now shows 6 levels).

This playbook is the checklist used to audit and clean up the Ozarks manual. Run it against any other site-specific manual repo cloned from the same template.

Work through the phases in order. **Phases 1–6 are read-only investigation — do not delete or edit anything until Phase 7's report has been shown to the user and they've told you what to do with it.** Phase 8 is the only phase that changes files, and only for items the user explicitly approved.

---

## Phase 0: Establish ground truth for this site

Before you can spot what's wrong, learn what's *right* for this specific site:

- Read the manual's home/index page for the site name, customer, and location.
- Find the site's actual physical configuration: number of levels, number of spiral/destination points, max lane count, etc. Two sources, in order of trust:
  1. **Ask the user directly** if anything is ambiguous.
  2. **Recently-modified screenshots.** Run `git log --stat` on the images folder — if this repo already went through a "screenshot refresh" pass, the most recently touched images are almost certainly showing the correct current configuration, even if the surrounding text still lags behind. Treat those images as ground truth and check everything else against them.
- Identify the *source* site's identity (the one this repo was cloned from) — check `package.json`'s `name` field, CI/deploy config, and any old logos — this is the string/name to search for as leftover residue.

---

## Phase 1: Repo/config identity leftovers

Check these for a name or path that still points at the source site instead of this one:

- `package.json` → `name` field
- `package-lock.json` → top-level `name` and `packages[""].name` (must match `package.json` exactly)
- CI/deploy workflow files (e.g. `.github/workflows/*.yml`) — look for hardcoded base paths, env vars, or site names (e.g. an `RS_PRESS_BASE`-style variable pointing at the wrong repo name — this kind of mismatch actually breaks deployed asset paths, not just cosmetic)
- Site config (e.g. `rspress.config.ts`) — title/site name
- `README.md`, if present

Fix these to match the actual repo/site naming convention.

---

## Phase 2: Orphaned/unreferenced images

1. First, determine what image-reference syntax this repo actually uses — **don't assume markdown `![]()` syntax.** Grep for both `![` and `<img src=` across the content files; use whichever one gets hits (this template uses JSX `<img src="...">`, not markdown image syntax).
2. Glob every image file under the docs image folder(s) (and check for stray images sitting outside the images folder too, e.g. loose files next to a content page).
3. For every image file, grep its exact basename across all content files. Zero hits = orphaned.
4. Pay special attention to a `logos/` (or similarly named) folder — these often contain unused vendor/integrator/customer logos left over from the source site that nothing currently references.
5. Compile the confirmed-orphaned list. **Do not delete anything yet** — this goes into the Phase 7 report.

---

## Phase 3: Text scan for source-site leftovers

Grep all content files (case-insensitive) for:

- The source site's name/customer name and its city/location
- Any vendor/integrator/other-company names found in logos or elsewhere
- Product/brand names that don't belong to the current customer's actual product line
- Generic markers: `deprecated`, `TODO`, `FIXME`, `legacy`, `outdated`, `placeholder`, `do not use`
- Generic system/vendor names used throughout (e.g. a downstream WMS/WCS system name) — these are often intentional and reused across sites, so **flag for user judgment rather than auto-fixing.**

Also check for internal inconsistency within a single page — e.g. a page banner naming the manual one thing and body text naming it something else.

---

## Phase 4: Visual review — brand/product data in screenshots

Open every screenshot actually referenced from content (not the orphans — those are already flagged for deletion) and look for:

- Product/brand names in tables, tooltips, or alert messages that don't match the target customer's real product catalog
- Other company logos, site names, or watermarks visible inside the screenshot itself (not just the filename)

For each hit, record the exact file path, the doc/line that references it, and exactly what text/branding you saw and where in the image.

**Don't assume every mismatch needs fixing** — ask the user. A screenshot whose purpose is to illustrate a UI flow (not the product data) may be fine to leave even with mismatched sample data, if the user decides that's acceptable.

---

## Phase 5: Visual review — physical configuration facts

This is where text/image drift shows up most often. For every screenshot that displays a count (levels, spiral/destination points, lanes per level, buffer zones, etc.):

- Cross-check the displayed count against the Phase 0 ground truth.
- Cross-check the **heading or prose sitting immediately above/below the image** — e.g. if the image shows "Spiral A / B / C" but the heading above it says "Spiral A / B", that's drift, even if you don't know the site's real spiral count.
- Cross-check screenshots against each other even without ground truth: if five screenshots of the same kind of data all show a count of 6 and one shows 5, the outlier is almost certainly stale (not recaptured during the last screenshot refresh).

List every mismatch with exact file path, line, and what's inconsistent with what.

---

## Phase 6: Visual review — infra details, PII, and staleness signals

Separately from product/count checks, look for:

- IP addresses, hostnames, URLs, or ports baked into a screenshot
- Employee/personal names — note whether they're already blurred/redacted in the image (open the image directly and look; don't take a first-pass summary's word for it — verify yourself)
- Timestamps that are internally inconsistent (not necessarily wrong, just sanity-check they're plausible relative to each other)
- Any screenshot using a visibly different UI color scheme, logo, or layout than the rest — a sign it wasn't recaptured during the last refresh

---

## Phase 7: Compile and present the audit report

Write a single markdown report at the repo root (e.g. `docs-legacy-audit.md`) organized by category:

- Config identity fixes needed/made (Phase 1)
- Orphaned files found (Phase 2) — full list, not deleted yet
- Text leftovers found (Phase 3) — split into "clear leftover" vs "needs your judgment"
- Screenshot brand/product mismatches (Phase 4)
- Screenshot configuration-count mismatches (Phase 5)
- Screenshot infra/PII/staleness findings (Phase 6)

Present this to the user and ask what they want done with each category before touching anything. Expect them to keep some items as-is (e.g. generic system names, screenshots where the mismatched data isn't the point).

---

## Phase 8: Apply only what's approved

- Delete orphaned files with `git rm` (not a plain filesystem delete) so the removal is staged and visible in `git status`.
- Apply text edits only to the specific lines identified — don't rewrite surrounding content.
- Apply config renames consistently across all files that reference the old name (e.g. both `package.json` and `package-lock.json`).
- Re-run the relevant Phase 2/3 greps after edits to confirm nothing was missed and nothing broke.
- Update the audit report to mark each item resolved, left-as-is, or deferred (e.g. "needs a real screenshot recapture — can't be fixed by text edit").

---

## Things that tripped us up on the first pass — don't skip these

- **Don't assume markdown image syntax.** This template uses JSX `<img src="...">`. A grep for `![...]` alone will silently miss everything.
- **Screenshots recently touched in git are usually already correct for the new site**; the stale ones are the untouched ones. Use `git log --stat -- <image folder>` to tell them apart before assuming a screenshot is wrong.
- **A heading directly next to an image can drift independently of the image.** Always read the count/wording in the prose against what the adjacent screenshot actually shows — they're edited separately and go out of sync easily.
- **Config leftovers live outside `docs/`** (package.json, CI YAML) and are easy to skip if you only search the content folder — but a wrong CI base path can silently break the deployed site's asset paths.
- **Verify visual findings yourself before reporting them as fact**, especially anything sensitive like PII — a first-pass description can misread a blurred/redacted image as exposed data. Open the image and look before writing it into the report.
- **Not everything flagged needs fixing.** Some mismatches (generic system names, sample data in screenshots not focused on that data) are legitimately fine to leave — present findings and let the user decide per category rather than auto-fixing everything.
