---
layout: post
title: DeepDL log — week 1
date: 2026-05-26
summary: First week of the summer DL grind. d2l chapters 1–3, the math is easy and the plumbing is hard.
---

First week of the summer deep learning grind. Goals for the summer:

- Work through the d2l.ai book end-to-end, not just the parts I think I already know.
- Reimplement micrograd and a small transformer from scratch, no PyTorch shortcuts.
- Write one post per week, even when there's nothing impressive to show.

This week was mostly setup and the first few chapters of d2l — linear regression, softmax, the usual on-ramp. Nothing surprising, but writing the gradient updates by hand is a useful sanity check on what `loss.backward()` is actually doing.

The thing I keep noticing: the math is easy, the plumbing is hard. Getting shapes to line up, getting the data loader to not be the bottleneck, getting the training loop to log the right things — that's where the hours go. The actual learning rule is three lines.

Next week: finish the MLP chapter and start on conv nets. And reimplement micrograd from memory.
