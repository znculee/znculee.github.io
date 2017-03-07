---
layout: blog
title: Reinforcement Learning
category: Mathmetics
---

- toc
{:toc}

# Policy Gradient

The objective in reinforcement learning is to optimize a policy $\pi$ by maximizing the expected return $J(\pi)$.

$$
\pi ^* = \arg \max \limits _{\pi} J (\pi)
$$

The reutrn of a policy is defined over all possible trajectories $\tau$, which is a sequence of states and actions $(s_0, a_0, \dots, s_T, a_T)$:

$$
J (\pi _{\theta}) = \int _{\tau} P(\tau | \pi) R(\tau) = \mathbb{E} _{\tau \sim \pi} \left[ R(\tau) \right]
$$

where

$$
P(\tau | \pi) = \rho _0 (s_0) \prod \limits _{t=0} ^{T-1} P (s _{t+1} | s_t, a_t) \pi (a_t | s_t)
$$

$$
R(\tau) = \sum \limits _{t=0} ^{\infty} \gamma ^t r_t
$$

where $\gamma \in [0, 1]$ is the discount factor and $r_t$ is the reward received at time $t$.

To maximize the return, we use stochastic gradient ascent to update the policy:

$$
\theta _{k+1} = \theta _k + \alpha \nabla _{\theta} J (\pi _{\theta}) | _{\theta _k}
$$

Here, $\nabla _{\theta} J (\pi _{\theta})$ is known as the policy gradient.

$$
\begin{equation*}
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
\end{equation*}
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
\begin{equation*}
	\begin{array}{rcl}
    \mathbb{E} _{a \sim \pi _{\theta}} \left[ \nabla_{\theta} \log \pi_{\theta}(a|s) b(s) \right]
    & = & b(s) \mathbb{E} _{a \sim \pi _{\theta}} \left[ \nabla_{\theta} \log \pi_{\theta}(a|s) \right] \\
    & = & b(s) \sum _{a} \pi_{\theta}(a|s) \nabla_{\theta} \log \pi_{\theta}(a|s) \\
    & = & b(s) \sum _{a} \nabla_{\theta} \pi_{\theta}(a|s) \\
    & = & b(s) \nabla_{\theta} \sum _{a} \pi_{\theta}(a|s) \\
    & = & b(s) \nabla_{\theta} 1 \\
    & = & 0
	\end{array}
\end{equation*}
$$

However, it can significantly reduce the variance of the gradient estimator.

Let

$$
x = \nabla _{\theta} \log \pi _{\theta} (a|s)
$$

$$
g = x (R - b)
$$

So

$$
\begin{equation*}
	\begin{array}{rcl}
    b^*(s)
    & = & \arg \min \limits _{b(s)} \mathrm{Var}(g) \\
    & = & \arg \min \limits _{b(s)} \mathbb{E}[g^2] - (\mathbb{E}[g])^2 \\
    & = & \arg \min \limits _{b(s)} \mathbb{E}[g^2] \\
	\end{array}
\end{equation*}
$$

$$
\begin{equation*}
	\begin{array}{rcl}
    \frac{d\mathbb{E}[g^2]}{db}
    & = & \frac{d}{db} \mathbb{E} [x^2 (R - b)^2] \\
    & = & \mathbb{E} [2 x^2 (b - R)] \\
	\end{array}
\end{equation*}
$$

Let the derivative be 0, the optimal baseline that minimizes variance is

$$
b^*(s) =
\frac{
\mathbb{E}\left[(\nabla_{\theta} \log \pi_{\theta}(a|s))^2 R\right]
}{
\mathbb{E}\left[(\nabla_{\theta} \log \pi_{\theta}(a|s))^2\right]
}
$$

This is actually a weighted average $R$, where sensitive actions have larger impacts to the optimal baseline.
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
