---
layout: post
title: DeepDL log — week 2
date: 2026-06-02
summary: First real training run. Loss curve, the boring kind that goes down. Notes on lr warmup and what broke.
---

Got the first end-to-end run going. Tiny transformer, 4 layers, 256 dim, on a slice of OpenWebText. Nothing novel — the point this week was to make `train.py` actually train, not to do anything clever.

![Training loss for week 2 — drops from ~3.8 to ~1.6 over 5000 steps](/images/week-2-loss.svg)

The shape is what you want it to be: fast drop, then a long boring tail. The kink around step 2000 is the lr warmup ending.

Two things broke that were worth writing down:

1. **Tokenizer fork.** I had two copies of the vocab in the repo and `train.py` loaded one while `eval.py` loaded the other. Loss looked fine; eval was garbage. Lesson: one source of truth, imported, never copied.
2. **Gradient clipping off by default.** First run diverged at step ~300. Added `clip_grad_norm_(1.0)` and it was fine. I knew this. I forgot.

Next week: scale to 12 layers and start the BitNet quantization branch.
