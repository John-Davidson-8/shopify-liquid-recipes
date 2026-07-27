# Freemote Task 2 — Open Social Links in New Tabs

**Store/theme:** Dawn (v2-Dawn-Colours-Development, Hebrides dev store)
**File touched:** `snippets/social-icons.liquid`

## Goal
Client wants the footer's social media icons (Facebook, Instagram, etc.) to open in a new tab, so visitors don't get taken away from the site.

## Investigation
- Dev store had no social URLs configured, so none of the icons were rendering.
  Added test URLs via the Theme Editor (Settings → Social media) to make the
  icons visible for inspection/testing.
- Since Theme Editor changes update `settings_data.json` remotely, restarted
  `shopify theme dev` with the `--theme-editor-sync` flag so that change synced back down to the local files automatically.
- Inspected a social icon in DevTools — found real `<a>` tags in the rendered
  HTML, with class `list-social__item` / `list-social__link`.
- Searched the codebase for that class and for the setting id
  (`social_facebook_link`, etc.) and found `footer.liquid` calling:

```liquid
  {%- render 'social-icons', class: 'footer__list-social' -%}
```
- **Key lesson:** DevTools shows the *rendered* HTML — after Liquid has already run and any `{% render %}` tags have been replaced by their snippet's output. It does not show which file contains the source code. `footer.liquid` itself has no `<a>` tags for social links at all — it just calls the snippet.
  The actual markup lives in `snippets/social-icons.liquid`, which is reused
  across multiple places (footer, password page, announcement bar).

## Fix
Added `target="_blank" rel="noopener"` to each of the 9 platform `<a>` tags in `snippets/social-icons.liquid`, e.g.:

```liquid
<a href="{{ settings.social_facebook_link }}" class="link list-social__link" target="_blank" rel="noopener">
```

- `target="_blank"` opens the link in a new tab
- `rel="noopener"` is a required security pairing — without it, the new tab can access the original page via `window.opener`

Because it's one shared snippet, this single edit fixes social links
everywhere the snippet is used, not just the footer.

## Workflow
- Branch: `task-2-social-links` off `main`
- Deliberately excluded `config/settings_data.json` (test social URLs used only for local testing) from the commit — kept the commit scoped to the code fix only, since which URLs are configured is a content decision, not code
- `git add snippets/social-icons.liquid` → commit → push → PR → merge on GitHub
- After merging, `git checkout main` + `git pull` to bring the merge down
  locally (merges on GitHub don't automatically update your local branch)

## Technique note
Used Option+Click (Mac) for multi-cursor editing across the 9 near-identical
`<a>` tags — better suited than Cmd+D here since the surrounding text
(setting id, icon filename) differs slightly per platform, so Cmd+D's
"select next matching occurrence" wouldn't line up cleanly.