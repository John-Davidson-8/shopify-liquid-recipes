# Inventory Status Block (Low Stock Indicator)

Dawn already ships a fully-built inventory status feature — this recipe covers
how to find, enable, and configure it, plus the gotchas that can make it look
broken when it isn't.

## The brief

"Add a low-stock warning on product pages — e.g. 'Only 3 left in stock!'"

## Workflow

1. **Search for existing inventory logic before writing anything.**
   `grep -rn "inventory" --include="*.liquid" .`
   Surfaces `sections/main-product.liquid`, which already defines a full
   `inventory` block type — in stock, low stock, out of stock, and
   "continue selling when out of stock" are all handled.

2. **Enable the block.**
   Theme Editor → Product template → Product information section →
   Add block → **Inventory status** (if not already present).

3. **Configure its settings.**
   - **Low inventory threshold** — merchant-configurable number (e.g. 3).
     Below or equal to this, the "low stock" message shows instead of
     a plain "in stock" message.
   - **Inventory count** toggle — shows the exact number left
     ("Only 3 left") vs. a generic low-stock message.

## Key gotchas

- **`inventory_management` must equal `'shopify'`.**
  The block is conditionally hidden for products managed by external
  apps (e.g. Gelato/print-on-demand), since those don't track stock
  the way native Shopify inventory does. A POD product will never show
  this block, regardless of quantity set.

- **Inventory is checked per-location, not per-product.**
  A variant can show real stock in the admin while still displaying
  "Sold out" on the storefront, if that stock was added under a
  *different* location than the one the storefront's default sales
  channel checks against (Settings → Locations → Default location).
  This isn't a code bug — it's a data/store-configuration issue, and
  it won't show up by reading theme files at all.

## Key lesson

Same lesson as the corner-radius job: not every "add a feature" request
needs new code. The real skill is tracing whether Dawn already has it,
confirming *why* it isn't behaving as expected (code logic vs. store
data vs. location config), and reporting back accurately — rather than
building a redundant custom version.