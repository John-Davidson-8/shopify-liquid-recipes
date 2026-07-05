# Custom Color Override (fallback to color scheme)

**Use case:** Let a merchant pick a one-off custom background color for a section, while still defaulting to the theme's standard color scheme when no custom color is chosen. Common for special promotions/sales banners.

**Where used:** Dawn theme, `sections/rich-text.liquid` (also applicable to Image Banner, Multicolumn, or any section using `color_scheme`).

## 1. Schema setting (add after the existing `color_scheme` setting)

```json
{
  "type": "color",
  "id": "custom_background_color",
  "label": "Custom background color (overrides color scheme)"
}
```

## 2. Markup (add conditional inline style to the section wrapper)

```liquid
<div
  class="... color-{{ section.settings.color_scheme }} ..."
  {% if section.settings.custom_background_color != blank %}
    style="background-color: {{ section.settings.custom_background_color }};"
  {% endif %}
>
```

## Why it works

- No `"default"` on the schema setting means it returns `blank` when unset.
- `!= blank` check means the inline style only renders when a merchant actively picks a color.
- Inline `style` always beats class-based CSS specificity, so it cleanly overrides the scheme's background without touching the scheme system itself.