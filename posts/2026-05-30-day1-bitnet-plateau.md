---
layout: post
title: "Day 1: the BitNet plateau that wasn't (and the wall that was)"
date: 2026-05-30
summary: "I built the whole loop — paper to code to a GPU in the cloud — then watched my ternary model flatline. Splitting 'my setup is broken' from 'the method is limited' with one controlled A/B turned a dead run into a real result."
---

The goal this summer is to reproduce [BitNet b1.58](https://arxiv.org/abs/2402.17764) — ternary weights, native quantization — inside a real training framework, and to do it in public. Day one was supposed to be plumbing: get a tiny version training end to end so the pipeline exists. It turned into something better — a result I didn't expect and had to work to understand.

## The loop

Paper → code → public GitHub repo → `ssh` to a Lambda A10 → training. Every link got touched. A 3.2M-param nanoGPT-style model on char-level Shakespeare, with one knob: each `nn.Linear` swapped for a `BitLinear` (ternary weights, straight-through estimator). Fifteen seconds a run on the A10.

Then I read the numbers, and they were ugly.

## The plateau

```
Step   0 | val_loss=3.71   (random init; ln(65)=4.17)
Step 100 | val_loss=3.15
Step 500 | val_loss=3.11
Step 900 | val_loss=3.07
```

It dropped fast for 100 steps, then **flatlined at ~3.0 and stopped learning.** For char-level Shakespeare a healthy model should grind down toward ~1.5. Stuck at 3.0 means it learned letter frequencies — spaces, vowels — and then basically nothing. No words, no structure.

The temptation here is to start theorizing about why ternary networks are hard. That's a trap. When a run dies you don't *know* anything yet — the bug could be in my harness, my learning rate, my init, or the method itself. So before any theory, one experiment: **change exactly one variable.**

## The A/B that splits the world

Same script, same model, same learning rate, same everything — `BitLinear` → `nn.Linear`. If FP16 *also* dies at 3.0, my harness is broken and BitNet is innocent. If FP16 sails down while BitNet sticks, the plateau lives inside the quantization.

```
FP16  (nn.Linear) : 1.78 at 1000 steps, smooth monotonic descent
BitNet (BitLinear): 3.01, flat
```

FP16 hit 1.78 — textbook for this model. **The harness is healthy.** The plateau is BitNet's.

That single run changed what I had from "I ran a thing" to "I isolated a thing." Different, better object.

## Artifact vs. wall

Now the real question: *why* does BitLinear stall? Leading suspect — the learning rate. `1e-3` is fine for FP16, but the straight-through estimator is an *approximation* of the gradient; too hot a LR and the latent weights thrash across the `round()` boundary every step and never settle. So: drop to `3e-4`.

```
BitNet @ 3e-4: 2.79 at 1000 steps — and still descending
```

The plateau broke. So it *was* partly an optimization artifact — the hot LR was the immediate culprit. But the curve was still falling when I stopped, which left the actual question open: **is it just slow, or is it capped?** One more run answers it — 3× the budget:

```
FP16   @ 1e-3, 3000 steps: 1.55   (kept descending)
BitNet @ 3e-4, 3000 steps: 2.82   (floored ~2.80 by step ~1400, then flat)
```

There it is. Given three times the steps, BitNet **did not keep falling.** It floored around 2.80 and stayed — even drifted slightly up — while FP16 used the same steps to glide from 1.78 to 1.55.

## What day one actually was

Two findings, and they're different things:

1. **The `1e-3` plateau (3.01) was an optimization artifact.** The STE can't tolerate that LR at this scale. Lowering it fixed *that*.
2. **Underneath the artifact is a real wall.** At 3.2M params, BitNet floors ~1.25 nats above the FP16 baseline (2.80 vs 1.55) and won't close the gap with more steps.

And #2 is the interesting part — it's not a bug, it's the paper's own story. BitNet b1.58 *underperforms* full precision below ~3B params (Table 1); ternary needs scale to pay off, and I'm 1000× below that. I reproduced the small-scale ternary gap on my own controlled, single-variable toy. That's exactly the thing worth knowing before I spend real money training at 0.5B.

The lesson I'm keeping: a dead-flat loss curve isn't a failure, it's a question. The work is splitting "my setup is broken" from "the method is limited" — and the tool for that is a clean A/B where you change one variable and trust the comparison. The receipts (commands, full table) are in the [repo](https://github.com/carlwilsn/bitnet-repro).

Next: does the gap close as I scale toward 0.5B — or is small-scale ternary just capped? That's the whole game.
