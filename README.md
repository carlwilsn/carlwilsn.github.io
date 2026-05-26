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

**Local preview:** `cd .jekyll && bundle install && cp ../posts/*.md _posts/ && bundle exec jekyll serve` → <http://localhost:4000>.
