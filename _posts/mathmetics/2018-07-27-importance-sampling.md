---
layout: blog
title: Importance Sampling
category: Mathmetics
---

- toc
{:toc}

# Softmax Approximation

$$
P \left(y_i \right) = \mathrm{softmax} \left( x^T, w_i \right) = \frac{e^{x^T w_i}}{Z}
$$

where $Z$ is a normalizer, denoted as $\sum _{j=1}^{\mid V \mid} e^T w_j$, which sometime would be hard to compute when the number of category is very large. To update the parameters $\theta$ of $x$, we may calculate the gradient of softmax.

$$
\begin{equation*}
	\begin{array}{rcl}
		\nabla _{\theta} \log P \left( y_i \right) & = & \nabla _{\theta} \log \frac{e^{x^T w_i}}{\sum \limits _{j=1}^{\mid V \mid}e^T w_j} \\
		& = & \nabla _{\theta} \log e^{x^T w_i} - \nabla _{\theta} \log \sum \limits _{j=1}^{\mid V \mid} e^{x^T w_j} \\
		& = & \frac{\nabla _{\theta} \left(e^{x^T w_i}\right)}{e^{x^T w_i}} - \frac{\nabla _{\theta} \left(\sum \limits _{j=1}^{\mid V \mid} e^{x^T w_j} \right)}{\sum \limits _{j=1}^{\mid V \mid} e^{x^T w_j}} \\
		& = & \nabla _{\theta} \left(x^T w_i \right) - \sum \limits _{j=1}^{\mid V \mid} \frac{e^{x^T w_j}}{\sum \limits _{k=1}^{\mid V \mid} e^{x^T w_k}} \nabla _{\theta} \left(x^T w_i \right) \\
		& = & \nabla _{\theta} \left(x^T w_i \right) - \sum \limits _{y' \in V} P \left(y' \right) \nabla _{\theta}  \left(x^T w_i \right) \\
		& = & \nabla _{\theta} \left(x^T w_i \right) - \mathbb{E} _{P} \left[\nabla _{\theta}  \left(x^T w_i \right) \right] \\
	\end{array}
\end{equation*}
$$

When $Z$ is hard to compute, we would like to estimate the expecation in previous gradient by Monte Carlo method, as following,

$$
\mathbb{E} _P \left[f\left(x\right)\right] = \frac{1}{m} \sum \limits _{k=1}^{m}f\left(x_k\right), \quad x_k \sim P
$$

However, sometimes sample from target distribution P is intractable or computational unefficient.

# Importance Sampling

Importance sampling is a method to estimate the expaction by sampling from proposal distribution instead of target distribution.

$$
\begin{equation*}
	\begin{array}{rcl}
		\mathbb{E} _P \left[f\left(x\right)\right] & = & \int _x f \left(x\right) p \left(x\right) dx \\
		& = & \int _x f \left(x\right) \frac{p \left(x\right)}{q \left(x\right)} q \left(x\right) dx \\
		& = & \int _x f \left(x\right) w \left(x\right) q \left(x\right) dx \\
		& = & \int _x g \left(x\right) q \left(x\right) dx
	\end{array}
\end{equation*}

$$

Here, we have been able to estimate the expection by sampling from the proposal distribution $Q$, instead of $P$. However, we usually only have the score function $\tilde{p}\left(x\right)$ of $p\left(x\right)$, namely the nomalizer $Z_p$ is hard to compute.

$$
\begin{equation*}
	\begin{array}{rclcrcl}
	p\left(x\right) & = & \frac{\tilde{p}\left(x\right)}{Z_p} & \quad & q\left(x\right) & = & \frac{\tilde{q}\left(x\right)}{Z_q} \\
	Z_p & = & \int _x \tilde{p}\left(x\right)dx & \quad & Z_q & = & \int _x \tilde{q}\left(x\right)dx
	\end{array}
\end{equation*}
$$

$$
\begin{equation*}
	\begin{array}{rcl}
		\int _x f\left(x\right)  p \left(x\right) dx & = &  \int _x f \left(x\right) \frac{p \left(x\right)}{q \left(x\right)} q \left(x\right) dx \\
		& = & \int _x f \left(x\right) \frac{\tilde{p} \left(x\right) / Z_p}{\tilde{q} \left(x\right) / Z_q} q \left(x\right) dx \\
		& = & \frac{Z_q}{Z_p} \int _x f \left(x\right) \frac{\tilde{p} \left(x\right)}{\tilde{q} \left(x\right)} q \left(x\right) dx \\
		& = & \frac{Z_q}{Z_p}  \int _x f \left(x\right) \tilde{w} \left(x\right) q \left(x\right) dx \\
		& = & \frac{Z_q}{Z_p} \int _x \tilde{g} \left(x\right) q \left(x\right) dx \\
	\end{array}
\end{equation*}
$$

$$
\begin{equation*}
	\begin{array}{rcl}
		\frac{Z_p}{Z_q} & = & \frac{\int _x \tilde{p}\left(x\right)dx}{Z_q} \\
		& = & \frac{\int _x \tilde{p}\left(x\right)dx}{\tilde{q}\left(x\right) / q\left(x\right)} \\
		& = & \int _x \frac{\tilde{p}\left(x\right)}{\tilde{q}\left(x\right)} q\left(x\right ) dx \\
		& = & \int _x \tilde{w}\left(x\right) q\left(x\right) dx \\
		& = & \frac{1}{m} \sum \limits _{k=1}^{m}\tilde{w}\left(x_k\right), \quad x_k \sim Q
	\end{array}
\end{equation*}
$$

$$
\begin{equation*}
		\mathbb{E} _P \left[f\left(x\right)\right] = \frac{1}{m} \sum \limits _{k=1}^{m}\hat{w}\left(x_k\right), \quad x_k \sim Q
\end{equation*}
$$

where

$$
\hat{w}\left(x_k\right) = \frac{\tilde{w}\left(x_k\right)}{\frac{1}{m} \sum \limits _{j=1}^{m}\tilde{w}\left(x_j\right), \quad x_j \sim Q}
$$

# Complementary Sum Sampling

[Complementary Sum Sampling for Likelihood Approximation in Large Scale Classification](http://proceedings.mlr.press/v54/botev17a/botev17a.pdf)

To reduce the variance of important sampling method, *Complementary Sum Sampling* explicitly calculate the positive part and only sample from the negative part.
