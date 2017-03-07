---
layout: blog
title: Gumbel-Max Trick
category: Mathmetics
---

- toc
{:toc}

# Vanilla Softmax Multinomial Sampling

Given logits $x_1, x_2, \dots, x_n$, the probability of sampling index $i$ is

$$
p_i = \frac{e^{x_i}}{\sum_{j=1}^n e^{x_j}}
$$

To sample from this categorical distribution, we typically use inverse CDF sampling: draw $u \sim \text{Uniform}(0,1)$ and find the smallest $i$ such that $\sum_{j=1}^i p_j \geq u$.

This approach is sequential and slow for batched sampling. Moreover, argmax is not differentiable, making it unsuitable for gradient-based learning.

# Gumbel Distribution

The standard Gumbel distribution $\text{Gumbel}(0,1)$ has CDF

$$
F(g) = e^{-e^{-g}}
$$

To sample $g \sim \text{Gumbel}(0,1)$, use inverse CDF transform:

$$
g = -\log(-\log(u)), \quad u \sim \text{Uniform}(0,1)
$$

# Gumbel-Max Trick

**Claim**: Let $g_i \sim \text{Gumbel}(0,1)$ be i.i.d. Then

$$
\arg\max_i (x_i + g_i) \sim \text{Categorical}(p)
$$

**Proof**: We compute the probability that index $k$ is selected by integrating over all possible values of $g_k$.

Recall the Gumbel PDF and CDF:

$$
f(g) = e^{-g} e^{-e^{-g}}, \quad F(g) = e^{-e^{-g}}
$$

Index $k$ wins iff $x_k + g_k > x_j + g_j$ for all $j \neq k$, i.e., $g_j < g_k + x_k - x_j$.

Since $g_j$ are independent, for a fixed $g_k$:

$$
P(g_j < g_k + x_k - x_j) = F(g_k + x_k - x_j) = e^{-e^{-(g_k + x_k - x_j)}}
$$

Integrating over $g_k$ with its PDF and multiplying the CDFs for all $j \neq k$:

$$
\begin{equation*}
\begin{array}{rcl}
P(k = \arg\max_i (x_i + g_i))
& = & \int_{-\infty}^{\infty} \underbrace{e^{-g_k} e^{-e^{-g_k}}}_{\text{PDF of } g_k} \prod_{j \neq k} \underbrace{e^{-e^{-(g_k + x_k - x_j)}}}_{\text{CDF for } g_j} \, dg_k
\end{array}
\end{equation*}
$$

Combining all exponentials:

$$
= \int_{-\infty}^{\infty} e^{-g_k} \exp\left(-e^{-g_k} - \sum_{j \neq k} e^{-(g_k + x_k - x_j)}\right) dg_k
$$

$$
= \int_{-\infty}^{\infty} e^{-g_k} \exp\left(-e^{-g_k} \left(1 + \sum_{j \neq k} e^{x_j - x_k}\right)\right) dg_k
$$

$$
= \int_{-\infty}^{\infty} e^{-g_k} \exp\left(-e^{-g_k} \sum_{j=1}^n e^{x_j - x_k}\right) dg_k
$$

Substitute $t = e^{-g_k}$, so $dt = -e^{-g_k} dg_k$. When $g_k \to -\infty$, $t \to \infty$; when $g_k \to \infty$, $t \to 0$:

$$
= \int_{\infty}^{0} \exp\left(-t \sum_{j=1}^n e^{x_j - x_k}\right) (-dt) = \int_0^{\infty} e^{-t \cdot s} \, dt
$$

where $s = \sum_{j=1}^n e^{x_j - x_k}$. This is $\int_0^\infty e^{-st} dt = \frac{1}{s}$:

$$
= \frac{1}{\sum_{j=1}^n e^{x_j - x_k}} = \frac{e^{x_k}}{\sum_{j=1}^n e^{x_j}} = p_k \quad \blacksquare
$$

# From Log to Exponential Form

Substituting the Gumbel sample:

$$
\arg\max_i \left(x_i - \log(-\log(u_i))\right) = \arg\max_i \left(\frac{e^{x_i}}{-\log(u_i)}\right)
$$

Since $-\log(u) \sim \text{Exponential}(1)$ when $u \sim \text{Uniform}(0,1)$:

$$
\arg\max_i \frac{p_i}{e_i}, \quad e_i \sim \text{Exponential}(1)
$$

This leads to an efficient implementation:

```python
def forward(self, logits: torch.Tensor, temperatures: torch.Tensor):
    logits = logits.float().div_(temperatures.unsqueeze(dim=1))
    probs = torch.softmax(logits, dim=-1)
    sample_tokens = probs.div_(torch.empty_like(probs).exponential_(1).clamp_min_(1e-10)).argmax(dim=-1)
    return sample_tokens
```

# Reference

- [Gumbel distribution](https://en.wikipedia.org/wiki/Gumbel_distribution)
- [Categorical Reparametrization with Gumbel-Softmax](https://arxiv.org/abs/1611.01144)
