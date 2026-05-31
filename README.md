# carlwilsn.github.io

Personal blog. Jekyll + Lanyon theme. Posts in `posts/`. Everything else in `.jekyll/`.

**Write a post:** drop `YYYY-MM-DD-slug.md` in `posts/` with frontmatter:

```yaml
---
layout: post
title: Title
date: YYYY-MM-DD
---
```

**Deploy:** `git push`. GitHub Action builds and publishes to <https://carlwilsn.github.io>.

**Homepage sidebars:** the three-column home page reads two YAML files —
edit a list, push, done:

- `.jekyll/_data/open_questions.yml` — left column. Each: `q` (question), `since` (date).
- `.jekyll/_data/revelations.yml` — right column. Each: `r` (insight), `on` (date).

Newest at the top. When a question gets answered, move it to revelations.
