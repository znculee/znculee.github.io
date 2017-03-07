---
layout: blog
title: Online Softmax
category: Mathmetics
---

- toc
{:toc}

# Vanilla Softmax

Given logits: $x_1, x_2, \dots, x_n$,

$$
\mathrm{softmax}(x)_i = \frac{e^{x_1}}{\sum _{j=1} ^n e^{x_j}}
$$

However, if $x_j$ were very large, $e^{x_j}$ could overflow. A common trick to mitigate this issue is to subtract $m=\max_j x_j$, so that

$$
\mathrm{softmax}(x)_i = \frac{e^{x_i - m}}{\sum _{j=1} ^n e^{x_j - m}}
$$

But this requires finding $m$ from all the logics, which could be a lot. Online softmax is to enable block-wise compuation of softmax.

# Online Softmax Derivation

Assuming only $t$ logits $x_1,\dots,x_t$ are available, we have

$$
m_t = max(x_1,\dots,x_t)
$$

$$
d_t = \sum _{j=1} ^t e^{x_j - m_t}
$$

For $x_{t+1}$, we could compute $m$ and $d$ incrementally as

$$
m_t = max(m_t, x_{t+1})
$$

$$
\begin{equation*}
	\begin{array}{rcl}
    d_t 
    & = & \sum \limits _{j=1} ^{t+1} e^{x_j - m_{t+1}} \\
    & = & \sum \limits _{j=1} ^{t} e^{x_j - m_{t}} + e^{x_{t+1} - m_{t+1}} \\
    & = & \sum \limits _{j=1} ^{t} e^{x_j - m_{t}} \cdot e^{m_t - m_{t+1}} + e^{x_{t+1} - m_{t+1}} \\
    & = & d_t \cdot e^{m_t - m_{t+1}} + e^{x_{t+1} - m_{t+1}}
	\end{array}
\end{equation*}
$$

So, when $t=n$,

$$
\mathrm{softmax}(x)_i = \frac{e^{x_i - m_n}}{d_n}
$$

WLOG, merging two blocks is as

$$
m = max(m_A, m_B)
$$

$$
d = d_A e^{m_A-m} + d_B e^{m_B-m}
$$

This is the foundation of [flash attention](https://github.com/Dao-AILab/flash-attention), where it does

```
for each K/V block:
  compute block-wise Q @ K^T
  get block-wise m_B and d_B
  merge to global m and d
```

# Reference

- [flash attention: simplified #61](https://github.com/karpathy/nn-zero-to-hero/issues/61)
