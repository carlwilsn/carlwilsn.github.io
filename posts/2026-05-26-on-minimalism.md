---
layout: post
title: On minimalism
date: 2026-05-26
summary: A blog should be a flat list of writing. Title, date, body. Everything else is maintenance debt.
---

A blog should be a flat list of writing. No sidebar, no widgets, no related-posts carousel, no cookie banner. Title, date, body. That's the whole job.

The Lanyon theme this site started with had a sliding sidebar, a hamburger toggle, JavaScript for the toggle, custom typography overrides, and a pagination plugin to chunk posts into pages of ten. For a blog with one post.

I deleted all of it. What's left is the parent theme (Poole), trimmed further. Every post is one HTML file. The homepage is a `<ul>` of links. There is no JS.

The point isn't aesthetic purity. The point is that every piece of complexity you add is a piece you have to maintain, debug, and explain to yourself in six months. Most of it earns nothing.
