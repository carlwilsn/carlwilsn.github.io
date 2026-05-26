---
layout: post
title: Reading the BitNet b1.58 paper
date: 2026-06-09
summary: Notes from a slow read of the BitNet b1.58 paper. What the {-1, 0, 1} weight quantization actually buys you, and where I think the catch is.
---

Spent a few evenings reading [BitNet b1.58](https://arxiv.org/abs/2402.17764) carefully instead of skimming. Three things stuck.

**The weights are ternary, not binary.** The "1.58" is $$\log_2 3 \approx 1.58$$ bits per weight — each weight is in $$\{-1, 0, 1\}$$. The zero is the whole trick. A pure binary net has to encode "this connection doesn't matter" by making the weight small in magnitude, which you can't do with one bit. Ternary gives you a free "off" state, and most weights end up there.

**Matmul becomes addition.** If activations are 8-bit and weights are in $$\{-1, 0, 1\}$$, then `W @ x` has no multiplications — only signed adds and skips. That's the hardware story. On current GPUs you don't get the speedup because the kernels aren't there, but on dedicated silicon the throughput-per-watt math is dramatic.

**The catch is training.** You can't train directly in ternary space — the gradient is zero almost everywhere. The paper uses a straight-through estimator: forward pass quantizes, backward pass pretends the quantization was identity. This works, but it means you're still paying full fp16/bf16 cost during training. The win is purely at inference.

That's the part I want to reproduce. Train a small one in bf16 with STE quantization, dump the ternary weights, and benchmark inference against an fp16 baseline of the same architecture. If the loss gap is what the paper claims (basically zero past ~3B params, noticeably worse below), the reproduction is honest.

Open question I haven't resolved from one read: how sensitive is final loss to the quantization schedule? They quantize from step zero. I'd expect a warmup — fp16 for the first N steps, then switch on STE — to help. Will check.
