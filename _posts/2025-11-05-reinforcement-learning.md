---
layout: blog
title: Reinforcement Learning
---

- toc
{:toc}

# Policy Gradient

The objective in reinforcement learning is to optimize a policy $\pi$ by maximizing the expected return $J(\pi)$.

$$
\pi ^* = \arg \max \limits _{\pi} J (\pi)
$$

The return of a policy is defined over all possible trajectories $\tau$, which is a sequence of states and actions $(s_0, a_0, \dots, s_T, a_T)$:

$$
J (\pi _{\theta}) = \int _{\tau} P(\tau | \pi) R(\tau) d \tau = \mathbb{E} _{\tau \sim \pi} \left[ R(\tau) \right]
$$

where

$$
P(\tau | \pi) = \rho _0 (s_0) \prod \limits _{t=0} ^{T-1} P (s _{t+1} | s_t, a_t) \pi (a_t | s_t)
$$

$$
R(\tau) = \sum \limits _{t=0} ^{T} \gamma ^t r_t
$$

where $\gamma \in [0, 1]$ is the discount factor and $r_t$ is the reward received at time $t$.

To maximize the return, we use stochastic gradient ascent to update the policy:

$$
\theta _{k+1} = \theta _k + \alpha \nabla _{\theta} J (\pi _{\theta}) | _{\theta _k}
$$

Here, $\nabla _{\theta} J (\pi _{\theta})$ is known as the policy gradient.

$$
	\begin{array}{rcl}
    \nabla _{\theta} J (\pi _{\theta})
    & = & \nabla _{\theta} \mathbb{E} _{\tau \sim \pi _{\theta}} \left[ R(\tau) \right] \\
    & = & \nabla _{\theta} \int _{\tau} P(\tau | \theta) R(\tau) d \tau \\
    & = & \int _{\tau} \nabla _{\theta} P(\tau | \theta) R(\tau) d \tau \\
    & = & \int _{\tau} P(\tau | \theta) \nabla _{\theta} \log P(\tau | \theta) \cdot R(\tau) d \tau \\
    & = & \mathbb{E} _{\tau \sim \pi _{\theta}} \left[ \nabla _{\theta} \log P(\tau | \theta) \cdot R(\tau) \right] \\
    & = & \mathbb{E} _{\tau \sim \pi _{\theta}} \left[ \sum \limits _{t=0} ^T \nabla _{\theta} \log \pi _{\theta} (a_t | s_t) \cdot R(\tau) \right] \\
    & \approx & \frac{1}{|\mathcal{D}|} \sum \limits _{\tau \in \mathcal{D}} \sum \limits _{t=0} ^T \nabla _{\theta} \log \pi _{\theta} (a_t | s_t) \cdot R(\tau)
	\end{array}
$$

# Baseline

The policy gradient estimator is unbiased but often has high variance.
To reduce this variance, we introduce a baseline $b(s_t)$ and subtract it from the return:

$$
\nabla _{\theta} J(\pi _{\theta})
\approx
\frac{1}{|\mathcal{D}|} \sum _{\tau \in \mathcal{D}} \sum _{t=0} ^T
\nabla _{\theta} \log \pi _{\theta} (a_t|s_t) \left( R(\tau) - b(s_t) \right)
$$

Subtracting a baseline does not change the expectation because

$$
	\begin{array}{rcl}
    \mathbb{E} _{a \sim \pi _{\theta}} \left[ \nabla_{\theta} \log \pi_{\theta}(a|s) b(s) \right]
    & = & b(s) \mathbb{E} _{a \sim \pi _{\theta}} \left[ \nabla_{\theta} \log \pi_{\theta}(a|s) \right] \\
    & = & b(s) \sum _{a} \pi_{\theta}(a|s) \nabla_{\theta} \log \pi_{\theta}(a|s) \\
    & = & b(s) \sum _{a} \nabla_{\theta} \pi_{\theta}(a|s) \\
    & = & b(s) \nabla_{\theta} \sum _{a} \pi_{\theta}(a|s) \\
    & = & b(s) \nabla_{\theta} 1 \\
    & = & 0
	\end{array}
$$

However, it can significantly reduce the variance of the gradient estimator.

Let $\psi$ be the score,

$$
\psi = \nabla _{\theta} \log \pi _{\theta} (a|s)
$$

and $g$ the gradient with the baseline subtracted,

$$
g = \psi (R - b)
$$

So

$$
	\begin{array}{rcl}
    b^*(s)
    & = & \arg \min \limits _{b(s)} \mathrm{Var}(g) \\
    & = & \arg \min \limits _{b(s)} \mathbb{E}[g^2] - (\mathbb{E}[g])^2 \\
    & = & \arg \min \limits _{b(s)} \mathbb{E}[g^2]
	\end{array}
$$

$$
	\begin{array}{rcl}
    \frac{d\mathbb{E}[g^2]}{db}
    & = & \frac{d}{db} \mathbb{E} \left[ \psi ^2 (R - b)^2 \right] \\
    & = & \mathbb{E} \left[ 2 \psi ^2 (b - R) \right]
	\end{array}
$$

Setting the derivative to zero gives the optimal baseline,

$$
b^*(s) = \frac{\mathbb{E} \left[ \psi ^2 R \right]}{\mathbb{E} \left[ \psi ^2 \right]}
$$

This is actually a weighted average of $R$, where sensitive actions have larger impacts on the optimal baseline.
In practice, $b(s)$ is often approximated by the state value function $V^{\pi}(s)$,
leading to the advantage function formulation:

$$
A^{\pi}(s,a) = R(\tau) - V^{\pi}(s)
$$

$R(\tau)$ includes rewards from the entire trajectory,
but a decision should only consider its impacts on future outcomes,
which is known as rewards-to-go or Q-function.

$$
Q^{\pi}(s,a) = \sum \limits _{t'=t} ^T r(s_{t'},a_{t'})
$$

So

$$
A^{\pi}(s,a) = Q^{\pi}(s,a) - V^{\pi}(s)
$$

Then the policy gradient becomes:

$$
\nabla _{\theta} J(\pi _{\theta})
\approx
\frac{1}{|\mathcal{D}|} \sum _{\tau \in \mathcal{D}} \sum _{t=0} ^T
\nabla _{\theta} \log \pi _{\theta} (a_t|s_t)
A^{\pi}(s_t, a_t)
$$

This is the foundation of Advantage Actor-Critic (A2C) and related algorithms.

# Score Centering

The identity behind the baseline,

$$
\mathbb{E} _{a \sim \pi _{\theta}} \left[ \psi \right] = 0
$$

held because the actions were drawn from the same $\pi _{\theta}$ the score is taken through.
In LLM training that is usually false.
Rollouts come from an inference engine and gradients from a training engine,
and the two disagree through numerical precision, kernel choice, or stale weights.

Here the state is a prefix $h$ and the actions are the vocabulary $V$.
Fix $h$ and write $p _v = \pi _{\theta} (v|h)$ for the trainer, $q _v = \mu (v|h)$ for the sampler
that actually produced the tokens, $y$ for the sampled token, and

$$
\psi _v = \nabla _{\theta} \log p _v
\qquad
\bar{\psi} = \mathbb{E} _q \left[ \psi \right] = \sum _{v \in V} q _v \psi _v
$$

On policy $q = p$ and $\bar{\psi} = 0$.
Off policy it is not, and the covariance identity splits the update at $h$ in two:

$$
\mathbb{E} _q \left[ R \psi \right]
=
\underbrace{\mathbb{E} _q \left[ R \right] \bar{\psi}} _{\text{drift}}
+
\underbrace{\mathrm{Cov} _q \left( R, \psi \right)} _{\text{signal}}
$$

Only the covariance knows which token earned which reward;
the drift sees $R$ through its mean alone. Its direction is

$$
\bar{\psi}
=
\sum _{v \in V} q _v \nabla _{\theta} \log p _v
=
\nabla _{\theta} \sum _{v \in V} q _v \log p _v
=
- \nabla _{\theta} H (q, p)
$$

a supervised fit of $p$ to $q$, scaled by the mean reward.
Fitting a fixed teacher would simply converge to that teacher.
But $q$ is a biased copy of $p$ that gets refreshed from it,
so the bias is learned, copied back, and learned again.

A baseline does not remove the drift.
Centering the reward over a group of $G$ rollouts constrains the sample,
not the conditional expectation the drift depends on:

$$
A _i = R _i - \bar{R}
\quad \Rightarrow \quad
\frac{1}{G} \sum _{i=1} ^{G} A _i = 0
\quad \not\Rightarrow \quad
\mathbb{E} _q \left[ A \mid h \right] = 0
$$

And that conditional is nonzero exactly where the learning signal is:
a prefix that already leads to the right answer has positive expected advantage,
and one that already contains a mistake has negative.
Centering the reward shrinks the drift without cancelling it.

Since the problem is that $\bar{\psi} \neq 0$, subtract it:

$$
\tilde{\psi} _v = \psi _v - \bar{\psi}
$$

The subtracted term is an expectation under the same $q$, so $\mathbb{E} _q [ \tilde{\psi} ] = 0$ and

$$
	\begin{array}{rcl}
    \mathbb{E} _q \left[ R \tilde{\psi} \right]
    & = & \mathbb{E} _q \left[ R \right] \underbrace{\mathbb{E} _q \left[ \tilde{\psi} \right]} _{0} + \mathrm{Cov} _q \left( R, \tilde{\psi} \right) \\
    & = & \mathrm{Cov} _q \left( R, \psi \right)
	\end{array}
$$

where the last step uses that shifting $\psi$ by a constant leaves the covariance alone.
The drift is cancelled exactly.

This mirrors the baseline: with an exact value function the baseline at this prefix is
$\mathbb{E} _q [R]$, and centering the reward does the same job,

$$
\mathbb{E} _q \left[ \left( R - \mathbb{E} _q \left[ R \right] \right) \psi \right]
=
\mathrm{Cov} _q \left( R, \psi \right)
$$

but that needs a critic.
Centering the score needs only $q$, one sum over next-token probabilities.
On policy a baseline changes the variance and not the mean.
Off policy, centering the score changes the mean.

It is not a full off-policy correction, because $\mathrm{Cov} _q \neq \mathrm{Cov} _p$.
On policy the same identity gives $\mathbb{E} _p [ R \psi ] = \mathrm{Cov} _p ( R, \psi )$,
so centering recovers the on-policy update up to that subscript.
Importance sampling attacks the subscript instead,
reweighting each token by $p _v / q _v$ to put the covariance back under $p$,
at the cost of a ratio that can be arbitrarily large on rare tokens.
Clipping bounds the variance but breaks the correction and lets the drift back in,
so the two are usually composed:
the ratio pulls the sampling distribution toward $p$,
and centering removes the drift the clipping leaves behind.
Numerical mismatch keeps $q$ close enough that centering alone is enough,
while badly stale rollouts need both.

## Implementation

Computing $\bar{\psi}$ exactly needs the sampler's full next-token distribution
at every generated token, which is far too much to store.
Let $H$ be its top $k$ tokens: log only those,
and model the tail with the trainer's own distribution rescaled to the sampler's tail mass:

$$
\hat{q} _v =
\begin{cases}
q _v & v \in H \\
\rho \, p _v & v \notin H
\end{cases}
\qquad
\rho = \frac{1 - \sum _{v \in H} q _v}{1 - \sum _{v \in H} p _v}
$$

Substituting $\hat{q}$ leaves a head term over $k$ tokens and a tail term over everything else.
Only the tail still ranges over the full vocabulary,
but the same identity holds for the trainer at $h$,
$\sum _v p _v \psi _v = 0$, so its head and tail sums are negatives of each other,

$$
	\begin{array}{rcl}
    \mathbb{E} _{\hat{q}} \left[ \psi \right]
    & = & \sum \limits _{v \in H} q _v \psi _v + \rho \sum \limits _{v \notin H} p _v \psi _v \\
    & = & \sum \limits _{v \in H} q _v \psi _v - \rho \sum \limits _{v \in H} p _v \psi _v \\
    & = & \sum \limits _{v \in H} \left( q _v - \rho \, p _v \right) \psi _v
	\end{array}
$$

so the tail is never touched and the correction is a sum over $k$ terms.
Since $\psi _v = \nabla _{\theta} \log p _v$,
the correction is the gradient of a scalar and needs no explicit gradient vectors,

$$
L = - R \left( \log p _y - \sum _{v \in H} \mathrm{sg} \left[ q _v - \rho \, p _v \right] \log p _v \right)
$$

where $\mathrm{sg}$ is a stop-gradient, applied to the coefficients only,
so that the gradient is the centered score with $\hat{q}$ in place of $q$,

$$
\nabla _{\theta} L
=
- R \left( \psi _y - \sum _{v \in H} \left( q _v - \rho \, p _v \right) \psi _v \right)
=
- R \, \tilde{\psi} _y
$$

```python
import jax.numpy as jnp
from jax.lax import stop_gradient

def score_centering_loss(logp, logq_head, head_ids, y, R):
    logp_head = logp[head_ids]
    rho = (1 - jnp.exp(logq_head).sum()) / (1 - jnp.exp(logp_head).sum())
    coeff = jnp.exp(logq_head) - rho * jnp.exp(logp_head)
    correction = (stop_gradient(coeff) * logp_head).sum()
    return -R * (logp[y] - correction)
```

So the rollout engine only has to return the top-$k$ token ids and their logprobs
alongside the sampled token, and $k = 32$ already matches the exact correction.

# Reference

- [Simple statistical gradient-following algorithms for connectionist reinforcement learning](https://doi.org/10.1007/BF00992696) (REINFORCE)
- [Spinning Up: Intro to Policy Optimization](https://spinningup.openai.com/en/latest/spinningup/rl_intro3.html)
- [Score Centering Stabilizes Off-policy Reinforcement Learning](https://arxiv.org/abs/2609.20807)
