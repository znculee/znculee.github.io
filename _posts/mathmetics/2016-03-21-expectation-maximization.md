---
layout: blog
title: Expectation Maximization
category: Mathmetics
---

- toc
{:toc}

Let $x$ be observable data and $z$ be latent data.

$$\frac{p\left(x,z \mid \theta \right)}{p\left(x \mid \theta \right)} = p\left(z \mid x,\theta \right)$$

Take log on both sides:

$$\begin{array}{rl}
& \log p\left(x,z \mid \theta \right)-\log p\left(x \mid \theta \right) = \log p\left(z \mid x,\theta \right) \\
\Rightarrow & \log p \left(x \mid \theta \right) = \log p\left(x,z \mid \theta \right) - \log p\left(z \mid x,\theta \right)
\end{array}$$

Take conditional expectation with respect to $z \mid \theta ^\prime, x$ on both sides:

$$\begin{array}{rl}
& \varepsilon \left[ \log p\left(x \mid \theta \right) \mid \theta ^\prime,x \right] = \varepsilon \left[ \log p\left(x,z \mid \theta \right) \mid \theta ^\prime,x\right] - \varepsilon \left[ \log p\left(z \mid x,\theta \right) \mid \theta ^\prime,x\right] \\
\Rightarrow & \log p \left(x \mid \theta \right) = \varepsilon \left[ \log p\left(x,z \mid \theta \right) \mid \theta ^\prime,x\right] - \varepsilon \left[ \log p\left(z \mid x,\theta \right) \mid \theta ^\prime,x\right]
\end{array}$$

Choose

$$\begin{array}{rl}
& \theta ^{\left(i+1\right)} = \arg \max \limits _{\theta} \varepsilon \left[ \log p\left(x,z \mid \theta \right) \mid \theta ^{\left(i\right)},x\right] \\
\Rightarrow & \theta ^{\left(i+1\right)} = \arg \max \limits _{\theta} \sum \limits _z p\left(z \mid \theta ^{\left(i\right)},x \right) \log p\left(x,z \mid \theta\right)
\end{array}$$

---

Prove that $p\left(x \mid \theta ^{\left(i\right)}\right)$ is increasing as $i$ increasing, i.e., $p\left(x \mid \theta ^{\left(i+1\right)} \right) \geq p\left(x \mid \theta ^{\left(i\right)}\right)$.

* Because of the choice of $\theta ^{\left(i+1\right)}$, we have

$$\varepsilon \left[ \log p\left(x,z \mid \theta ^{\left(i+1\right)} \right) \mid \theta ^{\left(i\right)},x\right] \geq \varepsilon \left[ \log p\left(x,z \mid \theta ^{\left(i\right)} \right) \mid \theta ^{\left(i\right)},x\right]$$

* We only need to show that

$$\varepsilon \left[ \log p\left(z \mid x,\theta  ^{\left(i+1\right)} \right) \mid \theta  ^{\left(i\right)},x\right] \leq \varepsilon \left[ \log p\left(z \mid x,\theta  ^{\left(i+1\right)} \right) \mid \theta  ^{\left(i\right)},x\right]$$

This is true because of following.

If $\varepsilon$ is taken with respect to $p\left(x\right)$, we have $\varepsilon \left[ \log p \left(x\right) \right] \geq \varepsilon \log p ^\prime \left(x\right)$, where $p ^\prime \left(x\right)$ is any pdf (not identical as $p\left(x\right)$).

p.f.

$$\begin{array}{rl}
& \varepsilon \left[ \log \frac{p ^\prime \left(x\right)}{p\left(x\right)} \right] \leq \log \varepsilon \left[ \frac{p ^\prime \left(x\right)}{p\left(x\right)} \right] \qquad \left(\text{by Jensen's inequality}\right) \\
\Rightarrow & \varepsilon \left[ \log p ^\prime \left(x\right) \right] - \varepsilon \left[ \log p\left(x\right) \right] \leq \log \int \frac{p ^\prime \left(x\right)}{p\left(x\right)} \cdot p\left(x\right) dx = 1 \\
\Rightarrow & \varepsilon \left[ \log p ^\prime \left(x\right) \right] - \varepsilon \left[ \log p\left(x\right) \right] \leq 0 \\
\Rightarrow & \varepsilon \left[ \log p ^\prime \left(x\right) \right] \leq \varepsilon \left[ \log p\left(x\right) \right]
\end{array}$$

p.s.
Jensen's inequality:

For a convex function $\phi$,

$$\varepsilon \left[\phi \left(x\right) \right] \geq \phi \left( \varepsilon \left[ x\right] \right)$$

and let $\phi = - \log$,

$$\varepsilon \left[ \log \left(x\right) \right] \leq \log \left( \varepsilon \left[x\right] \right)$$e
