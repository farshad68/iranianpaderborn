# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Jekyll 4 static site for **iranianpaderborn.de** (see `CNAME`): a bilingual directory of Iranian businesses, services, doctors and blog posts in Paderborn. Every piece of content exists in two languages — German (`de`) and Persian/Farsi (`fa`, right-to-left).

## Commands

```bash
bundle install                 # install Jekyll, minima theme, jekyll-feed
bundle exec jekyll serve       # local dev server at http://localhost:4000 (restart after editing _config.yml)
bundle exec jekyll build       # build into _site/ (gitignored)
```

There are no tests or linters.

## Architecture

**Language split is done with parallel collections, not an i18n plugin.** `_config.yml` defines a `_de` and `_fa` collection for each content type, each with its own permalink prefix:

| Collection dirs | URL |
|---|---|
| `_geschaefte_de` / `_geschaefte_fa` (businesses) | `/de/geschaefte/:name/`, `/fa/geschaefte/:name/` |
| `_dienstleistungen_de` / `_dienstleistungen_fa` (services) | `/de/dienstleistungen/:name/`, ... |
| `_doctors_de` / `_doctors_fa` | `/de/doctors/:name/`, ... |
| `_posts_de` / `_posts_fa` (blog) | `/de/blog/:title`, `/fa/blog/:title` |

Note `posts_de`/`posts_fa` are custom collections, not Jekyll's built-in `_posts` — they're listed via `site.posts_de` / `site.posts_fa`, not `site.posts`.

**Adding an entry means creating two files** with the same filename, one in each language's collection dir. Front matter convention for directory entries:

```yaml
layout: default        # default_fa for Persian
title: ...
name: ...
category: ...          # shown as a badge on listing pages (translated in fa)
selected_option: true  # surfaces it on the home page (filtered in home*.html)
lang: de               # or fa — drives <html lang> and font choice
```

Blog posts use `layout: post_de` / `post_fa` plus `date`.

**Layouts** (`_layouts/`) are paired by language:
- `default.html` / `default_fa.html` — page shells (Bootstrap 5 from CDN, inline CSS; fa uses the Vazir font and RTL). They include `header.html`/`footer_de.html` and `header_fa.html`/`footer_fa.html` respectively; nav links are hardcoded in those header includes.
- `home.html` / `home_fa.html` — language home pages; pull the latest/selected items from each collection.
- `geschaefte_*`, `dienstleistungen_*`, `doctors_*`, `blog_*` — listing layouts that loop over the matching collection. The index pages under `de/<section>/index.md` and `fa/<section>/index.md` are just front matter selecting these layouts.

The root `index.markdown` (layout `landing`) is a language picker linking to `/fa/` and `/de/`. The minima theme is declared but the site's own layouts override it.

`templates/pagesmdfile.md` is a fa/de content skeleton for new business pages.

## Gotchas

- Changes to a nav item, layout, or listing usually need to be mirrored in both the `de` and `fa` variant.
- Collection files need a `.md` extension to be rendered as Markdown.
- `lang` must match the page's language: `default.html` loads the Vazir font when `lang: fa`, and `<html lang>` falls back to `de` when it's missing.
