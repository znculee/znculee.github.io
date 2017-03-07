---
layout: blog
title: Sample Variance
category: Mathmetics
---

- toc
{:toc}

$$\text{Var}\left(X_n\right)=S_n^2=\frac{\sum _{i=1}^{n}\left(X_i-\bar{X}_n\right)^2}{n-1}$$

$$\text{E}\left(S_n^2\right)=\text{E}\left(\frac{\sum _{i=1}^{n}\left(X_i-\bar{X}_n\right)^2}{n-1}\right)$$

---
$$\begin{array}{rcl}\left(n-1\right)\text{E}\left(S_n^2\right)&=&\text{E}\left(\sum _{i=1}^{n}\left(X_1-\bar{X}_n\right)^2\right) \\ &=&\text{E}\left(\sum _{i=1}^{n}X_i^2\right)-\text{E}\left(\sum _{i=1}^{n}2X_i\bar{X}_n\right)+\text{E}\left(\sum _{i=1}^{n}\bar{X}_n^2\right) \\ &=&\text{E}\left(\sum _{i=1}^{n}X_i^2\right)-\text{E}\left(2\bar{X}_n\sum _{i=1}^{n}X_i\right)+\text{E}\left(\bar{X}_n^2\sum _{i=1}^{n}1\right) \\ &=& \text{E}\left(\sum _{i=1}^nX_i^2\right)-\text{E}\left(2n\bar{X}_n\right)^2+\text{E}\left(n\bar{X}_n^2\right) \\ &=& \text{E}\left(\sum _{i=1}^{n} X_i^2\right)-n\text{E}\left(\bar{X}_n^2\right) \\ &=& n\text{E}\left(X_i^2\right)-n\text{E}\left(\bar{X}_n^2\right) \\ &\Leftrightarrow& \\ \frac{n-1}{n}\text{E}\left(S_n\right)^2&=&\text{E}\left(X_i^2\right)-\text{E}\left(\bar{X}_n^2\right)\end{array}$$

---
$\begin{array}{rcl}\text{E}\left(X_i^2\right)&=&\text{E}\left(X_i\right)^2+\text{V}\left(X\right) \\ &=&\mu^2+\sigma^2\end{array}$

$\begin{array}{rcl}\text{E}\left(\bar{X}_n^2\right)&=&\text{E}\left(\bar{X}_n\right)^2+\text{V}\left(\bar{X}_n\right) \\ &=&\mu^2+\text{V}\left(\frac{1}{n}\sum _{i=1}{n}X_i\right) \\ &=&\mu^2 + \frac{1}{n^2}\text{V}\left(\sum _{i=1}^{n}X_i\right) \\ &=& \mu^2+\frac{\sigma^2}{n}\end{array}$

---
$$\begin{array}{rcl}\frac{n-1}{n}\text{E}\left(S_n\right)^2&=&\mu^2+\sigma^2-\mu^2-\frac{\sigma^2}{n} \\ &=& \frac{n-1}{n}\sigma^2\end{array}$$

$$\text{E}\left(S_n\right)^2=\sigma^2$$
