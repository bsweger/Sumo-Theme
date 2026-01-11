# AI Agent Guide

This document provides context for AI coding agents working on this Micro.blog theme.

## Project Overview

This is a customized fork of the [Micro.blog Sumo Theme](https://github.com/microdotblog/Sumo-Theme), deployed on Micro.blog as a custom theme. It's built on Hugo, a static site generator.

### Key Technologies

- **Hugo** - Static site generator (used by Micro.blog)
- **Go Templates** - Hugo's templating language
- **CSS Custom Properties** - For theming (light/dark mode support)

## Project Structure

```text
layouts/
  _default/
    baseof.html           # Base template with DOCTYPE
    list.html             # Category/taxonomy list pages
    list.archivehtml.html # Main archive page
    single.html           # Individual posts
  partials/
    head.html             # Meta tags, social media previews
    header.html           # Site header/navigation
    footer.html           # Site footer
    custom/
      socialcardimage.html # Social media image meta tags
  post/
    single.html           # Post-specific template
static/
  css/
    main.css              # Theme styles
i18n/
  en.json               # English translations
config.json             # Theme configuration
```

## Important Conventions

### Hugo Date Formatting

Hugo uses Go's reference date for formatting: **Mon Jan 2 15:04:05 MST 2006**

- `"2006"` = 4-digit year
- `"January"` = full month name
- `"Jan"` = abbreviated month
- `"01"` = 2-digit month
- `"02"` = day of month

Example: `.GroupByDate "2006"` groups posts by year, not by the literal year 2006.

### CSS Variables

The theme uses CSS custom properties for colors to support light/dark modes automatically:

- Always use CSS variables (e.g., `var(--main-text)`) instead of hardcoded colors
- Variables are defined in `static/css/main.css` with automatic dark mode overrides via `@media (prefers-color-scheme: dark)`
- New components should reuse existing variables when possible

### Translations

All user-facing text should use Hugo's `T` function for translation support:

```html
<h3>{{ T "Archive" }}</h3>
```

Add new strings to `i18n/en.json` (and other language files if they exist).

## Hugo Limitations on Micro.blog

### Date-Based Archives

Hugo does **not** automatically create listing pages for date-based URLs like `/2025/12/`. Options:

1. **Taxonomies** - Requires adding year/month metadata to every post (complex)
2. **Anchor links** - Links to sections on a single page (current approach)
3. **JavaScript** - Client-side filtering (adds complexity)

The archive page uses anchor links (`#2025`) for simplicity.

### Relative vs Absolute URLs

- Image paths in posts may be relative (e.g., `/uploads/image.jpg`)
- Use `| absURL` filter when URLs need to be absolute (e.g., for social media meta tags)
- However, check if Micro.blog already provides absolute URLs in `.Params.photos` before adding filters

## Theme Deployment

This theme is deployed on Micro.blog:

1. Changes are pushed to GitHub (main branch)
2. User goes to **Posts → Design → Edit Custom Themes**
3. User reloads/updates the theme from GitHub
4. Changes appear on the live site

**Important:** Always test template syntax carefully - Hugo template errors will break the site.

## Social Media Previews

### Current Behavior

- Posts **with photos**: Photos appear in social media previews via `.Params.photos`
- Posts **without photos**: No image (profile image fallback was removed)
- Custom image: Can be set via `.Site.Params.socialCardImage` parameter

### Meta Tags

- Uses **Open Graph** tags (`og:image`, `og:title`, etc.) for broad compatibility

## Testing Considerations

- **Dark mode**: Test all new components in both light and dark modes
- **Social previews**: Use validators like [OpenGraph.xyz](https://www.opengraph.xyz/)
- **Template errors**: Hugo templates fail silently in some cases - check site builds carefully
- **Micro.blog specifics**: Some Hugo features may not work on Micro.blog's infrastructure

## Common Patterns

### Conditional Content

```html
{{ if .Title }}
  <h2>{{ .Title }}</h2>
{{ else }}
  <p>Untitled post</p>
{{ end }}
```

### Iterating Posts

```html
{{ $posts := (where .Site.Pages "Type" "post") }}
{{ range $posts }}
  <a href="{{ .Permalink }}">{{ .Title }}</a>
{{ end }}
```

### Grouping by Date

```html
{{ range ($posts.GroupByDate "2006") }}
  <h4>{{ .Key }}</h4>
  {{ range .Pages }}
    <p>{{ .Title }}</p>
  {{ end }}
{{ end }}
```

## Gotchas

- **Micro.blog hooks**: The theme uses `templates.Exists` to check for optional "microhook" partials that users can add
- **Pagination**: Set in `config.json` - affects list pages
- **Category sorting**: Categories are sorted by title alphabetically
- **Performance**: Avoid unnecessary filters and loops - Hugo rebuilds can be slow on large sites

## Questions?

If you're unsure about Micro.blog-specific behavior, check:

- [Micro.blog Theme Documentation](https://help.micro.blog/t/custom-themes/59)
- [Hugo Documentation](https://gohugo.io/documentation/)
- The [official Sumo Theme repository](https://github.com/microdotblog/Sumo-Theme) for reference
