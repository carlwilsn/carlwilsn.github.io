# carlwilsn.github.io

Personal blog / summer log. Jekyll (Poole theme). The site source lives in
`.jekyll/`; the content you edit day-to-day lives in three top-level folders,
**one file per entry**. The build copies all three into Jekyll
(`_posts/`, `_open_questions/`, `_revelations/`) before it runs, so you never
have to touch `.jekyll/` to add content.

## Content folders

- `posts/` — blog posts (the center column).
- `open-questions/` — the left **Open Questions** rail.
- `revelations/` — the right **Recent Revelations** rail.

### Write a post

Drop `YYYY-MM-DD-slug.md` in `posts/`:

```yaml
---
layout: post
title: Title
date: YYYY-MM-DD
summary: One-line summary shown on the home page (optional).
---
Body in Markdown.
```

### Add an open question

Drop any `slug.md` in `open-questions/`. The date goes in the front matter; the
question itself is the body:

```markdown
---
since: 2026-05-30
---
Your question goes here.
```

### Add a recent revelation

Drop any `slug.md` in `revelations/` (same shape, `on:` instead of `since:`):

```markdown
---
on: 2026-05-30
---
Your insight goes here.
```

Both rails sort **newest date first** automatically. When a question gets
answered, delete it from `open-questions/` and add the takeaway to
`revelations/`.

## Deploy

`git push` to `main`. The GitHub Action builds and publishes to
<https://carlwilsn.github.io>. (CSS links are versioned with the build time, so
a deploy always busts the browser cache — no more stale styles.)
