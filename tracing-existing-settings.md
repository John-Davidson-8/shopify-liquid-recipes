# Tracing an Existing Theme Setting
...(paste the rest of the draft content here)...
# Tracing an Existing Theme Setting

Not every client request needs new code. Many "add a setting" requests are
already built into the theme — the real skill is proving it, fast, before
writing anything.

## Workflow

1. **Find the setting in the schema.**
   `grep -n "\"name\":" config/settings_schema.json` to list setting groups,
   then locate the specific setting id (e.g. `card_corner_radius`).

2. **Check for duplicates/similar ids.**
   Dawn often repeats a setting per content type (product cards, collection
   cards, blog cards). Confirm which one is actually relevant.

3. **Find where the setting is read in Liquid.**
   `grep -rn "setting_id" --include="*.liquid" .`
   Usually surfaces in `layout/theme.liquid`, where Liquid settings get
   converted into global CSS custom properties (often with a unit
   conversion, e.g. `px` → `rem` via `divided_by: 10.0`).

4. **Find where that CSS variable is defined per component.**
   `grep -rn "--variable-name" --include="*.css" .`
   Look for a generic variable (e.g. `--border-radius`) being set from the
   specific one (e.g. `--product-card-corner-radius`) inside a scoped
   selector like `.product-card-wrapper .card`.

5. **Find where the generic variable is actually applied.**
   `grep -n "var(--variable-name)" assets/component-*.css`
   This is the final step — confirms which HTML elements (card border,
   image wrapper, etc.) the setting visually affects.

6. **Verify in the Theme Editor + live preview.**
   Navigate to a page that actually renders the component (e.g. a
   collection/catalog page for product cards — a single product page won't
   show card styling). Drag the setting and confirm the visual change.

## Key lesson

Theme setting names in code (`settings_schema.json` ids) often don't match
their UI label exactly (e.g. schema group `cards` → UI label
"Product cards"). Don't assume a missing UI match means missing
functionality — check the JSON id directly.

## Terminal commands used

- `grep -rn "term" .` — recursive search, all files, with line numbers
- `grep -rn "term" --include="*.liquid" .` — restrict search to one file type
- `grep -n "term" path/to/file` — search within a single file
- `sed -n '1,40p' path/to/file` — print only lines 1–40 of a file