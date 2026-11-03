# Where to change what

| I want to...                                     | Edit this                              |
|--------------------------------------------------|----------------------------------------|
| Change fonts, sizes, colors, page width          | `assets/css/site.css` (top block)      |
| Change the title size                            | `--title-size-post` / `--title-size-list` |
| Change the olive green                           | `--olive`, `--olive-deep`              |
| Make stories narrower again                      | `--page-width` (for example `70rem`)   |
| Change the nav, home intro and links, footer     | `_data/site.yml`                       |
| Change the shared header or footer HTML          | `_includes/header.html`, `footer.html` |
| Change how title + caption are laid out          | `_includes/story-head.html`            |
| Change the page skeleton (every page)            | `_layouts/default.html`                |

After editing `_data/` or `_config.yml`, restart `bundle exec jekyll serve`. CSS changes just need a refresh.

## A new story

Create `_posts/YYYY-MM-DD-some-title.md`:

```
---
layout: post
title: Your title
caption: "One line that says what this story is about."
---

Story text here.
```

The caption shows under the title on the home list and on the story page.

## Photos

Put pictures in `assets/photos/` on GitHub (Add file, Upload files). They show up on `/photos/`,
newest file name first, in rows that fill the full width with no gaps.
The latest 6 also show as a strip on the home page (`home_photos` in `_data/site.yml`).
Big phone photos make the page slow, so shrink them first if you can.
