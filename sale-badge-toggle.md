# Sale Badge Toggle (extend existing feature, not build from scratch)

**Use case:** Let a merchant turn the "Sale" badge on product cards on/off, 
independent of the sold-out badge. Common request when a merchant wants 
discounts active without visually calling attention to them.

**Where used:** Dawn theme — badge logic lives in `snippets/card-product.liquid` 
(appears twice, once per card layout variant), controlled by a global setting 
in `config/settings_schema.json` under the "Badges" group.

**Key lesson:** Dawn already had sale badge logic built in. The job wasn't 
building something new — it was finding existing logic and adding a 
merchant-facing on/off switch to it. Always check for existing settings/logic 
before building from scratch.

## 1. Schema setting (add inside the "Badges" settings group)

```json
{
  "type": "checkbox",
  "id": "show_sale_badge",
  "default": true,
  "label": "Show sale badge on product cards"
}
```

Note: `"default": true` here, unlike an opt-in feature — sale badges are 
generally wanted by default, so the merchant is choosing to turn it *off*, 
not turn it on from nothing.

## 2. Markup (extend the existing conditional, don't replace it)

Original condition, before change:
```liquid
{%- elsif card_product.compare_at_price > card_product.price and card_product.available -%}
```

Updated condition, after change:
```liquid
{%- elsif card_product.compare_at_price > card_product.price and card_product.available and settings.show_sale_badge -%}
```

Just adds `and settings.show_sale_badge` to the existing check — the badge 
now requires all three things to be true: a real discount, availability, 
and the merchant's toggle being on.

**Important:** this same line appeared twice in `card-product.liquid` 
(once per card layout). Both occurrences needed the same edit for the 
toggle to work consistently everywhere the badge could appear.

## Why it works

- `settings.show_sale_badge` reads from **global** theme settings (not 
  section settings), since this setting lives in `settings_schema.json`, 
  not inside a section's own `{% schema %}` block.
- Found in theme editor under **Theme Settings > Badges**, not inside 
  any individual section's settings panel.
- Using `and` in the existing `elsif` keeps all prior logic intact while 
  adding one more required condition — minimal, safe change to existing 
  working code.