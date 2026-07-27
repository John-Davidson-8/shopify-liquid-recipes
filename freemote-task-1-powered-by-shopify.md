# Freemote Task 1 — Comment Out "Powered by Shopify" Link

**Store/theme:** Dawn (v2-Dawn-Colours-Development, Hebrides dev store)
**File touched:** `sections/footer.liquid`

## Goal
Client wants the default "Powered by Shopify" footer link removed from the storefront.

## Investigation
- Started by inspecting the rendered footer in the browser (Inspect Element) to find the actual link markup.
- Searched the codebase for the literal text ("Powered by Shopify") and for "poweredby" — both came back empty.
- Reason: the text isn't hardcoded in any theme file. Dawn (like most themes) renders it via Shopify's built-in `{{ powered_by_link }}` object, which the Liquid engine expands into the full `<a>` tag server-side.
- Searched instead for the CSS class from DevTools (`copyright__content`), which led straight to `sections/footer.liquid`.

## Fix
Found the line:
```liquid
<small class="copyright__content">{{ powered_by_link }}</small>
```
Wrapped it in Liquid's comment tag:
```liquid
{% comment %}
<small class="copyright__content">{{ powered_by_link }}</small>
{% endcomment %}
```

## Gotcha
Liquid comments use `{% comment %} ... {% endcomment %}`, not JS-style `//` or `/* */`. The CLI's live sync showed a transient "comment tag was never closed" error mid-edit — this was just the auto-sync catching an incomplete save while typing, and resolved once the file was fully saved.

## Workflow
- Branch: `task-1-powered-by-shopify` off `main`
- `git add sections/footer.liquid` (deliberately excluded an unrelated untracked file — `assets/overlap-image-text.css` — left over from a different in-progress branch)
- Committed, pushed, PR opened and merged into `main` on GitHub
- Confirmed with `git pull` locally, then `shopify theme push` as a final sync check (not strictly necessary since `theme dev` auto-syncs on save — noted for future reference)

## Note
`{{ powered_by_link }}` is a global Shopify Liquid object, not custom theme code — this is why text-search strategies fail on it and DevTools/class-based search is the better first move for anything rendered by a built-in Shopify object rather than static theme markup.