---
layout: blog
title: Singular Value Decomposition
category: Mathmetics
---

- toc
{:toc}

# Introduction

*Theorem*: Any $\mathbf{A} \in \mathbb{C}^{m\times n}$ can be factored as

$$
\mathbf{A} = \mathbf{U} \mathbf{\Sigma} \mathbf{V}^H
$$
where $\mathbf{U} \in \mathbb{C}^{m\times m}$, $\mathbf{V} \in \mathbb{C}^{n\times n}$ are unitary.

$\mathbf{\Sigma} \in \mathbb{R}^{m \times n}$ has

$$
\left[\Sigma \right]_{ij} =
\begin{cases}
\sigma _i & i=j \\
0 & i \neq j
\end{cases}
$$

with $\sigma _1 \geq \sigma _2 \geq \cdots \geq \sigma _p$, $p=\min \left \\{ m,n \right \\}$

*Partitioned Form*:

$$
\mathbf{A} =
\begin{bmatrix}
 \mathbf{U}_1 & \mathbf{U} _2
\end{bmatrix}
\begin{bmatrix}
\tilde{\mathbf{\Sigma}} & \mathbf{0} \\ \mathbf{0} & \mathbf{0}
\end{bmatrix}
\begin{bmatrix}
\mathbf{V}_1^H \\ \mathbf{V} _2^H
\end{bmatrix}
$$

$\tilde{\mathbf{\Sigma}} = \mathrm{Diag}\left(\sigma _1, \cdots , \sigma _r \right)$, $\sigma _1 \geq \sigma _2 \geq \cdots \geq \sigma _r > 0$

Thin form:

$$
\mathbf{A} = \mathbf{U}_1 \tilde{\mathbf{\Sigma}} \mathbf{V} _1^H
$$

Outer-product form:

$$\mathbf{A} = \sum \limits _{i=1}^{r} \sigma _i \mathbf{u} _i \mathbf{v} _i^H$$

*Observations*:

Suppose $\mathbf{A} = \mathbf{U} \mathbf{\Sigma} \mathbf{V}^H$

$$
\mathbf{A}\mathbf{A}^H = \mathbf{U} \mathbf{\Sigma} \underbrace{\mathbf{V}^H \mathbf{V}} _{\mathbf{I}} \mathbf{\Sigma}^H \mathbf{U}^H = \mathbf{U}
\begin{bmatrix}
\sigma _1^2 \\
& \ddots \\
& & \sigma _p^2 \\
& & & 0 \\
& & & & \ddots \\
& & & & & 0
\end{bmatrix} \mathbf{U}^H
$$

$$
\mathbf{A}^H \mathbf{A} = \mathbf{V}
\begin{bmatrix}
\sigma _1^2 \\
& \ddots \\
& & \sigma _p^2 \\
& & & 0 \\
& & & & \ddots \\
& & & & & 0
\end{bmatrix} \mathbf{V}^H
$$

Property: $\|\mathbf{A}\|_2 = \sqrt{\lambda _{\mathrm{max}} \left(\mathbf{A}^H \mathbf{A} \right)} = \sigma _1 \left( \mathbf{A} \right )$

Suppose $\mathbf{A}$ is a square, and nonsingular

$$
\mathbf{A}^{-1} = \left( \mathbf{U} \mathbf{\Sigma} \mathbf{V}^H \right )^{-1} = \mathbf{V} \mathbf{\Sigma}^{-1} \mathbf{U}^H
$$

# Proof

## Simple Version

Apply ED on $\mathbf{A} \mathbf{A}^H$ :

$$
\mathbf{A} \mathbf{A}^H = \mathbf{U} \mathbf{\Lambda} \mathbf{U}^H
$$

$\mathbf{\Lambda}=\mathrm{Diag}\left(\lambda _i, \cdots ,\lambda _m \right)$, $\lambda _1 , \cdots , \lambda _m \geq 0$

Suppose $\lambda _i > 0,\ \forall i$

$$
\tilde{\mathbf{\Sigma}} = \mathrm{Diag}\left(\sqrt{\lambda _1}, \cdots , \sqrt{\lambda _m} \right ) = \tilde{\mathbf{\Lambda}}^{\frac{1}{2}}
$$

$$
\mathbf{V}_1 = \mathbf{A}^H \mathbf{U} \tilde{\mathbf{\Sigma}}^{-1} \in \mathbb{C}^{n \times m}
$$

$$
\mathbf{U} \tilde{\mathbf{\Sigma}} \mathbf{V}_1^H = \underbrace{\mathbf{U} \underbrace{\tilde{\mathbf{\Sigma}} \tilde{\mathbf{\Sigma}}^{-1}} _{\mathbf{I}} \mathbf{U}^H} _{\mathbf{I}} \mathbf{A} = \mathbf{A}
$$

$$
\mathbf{V}_1^H \mathbf{V} _1 = \tilde{\mathbf{\Sigma}}^{-1} \mathbf{U}^H \mathbf{A} \mathbf{A}^H \mathbf{U} \tilde{\mathbf{\Sigma}}^{-1} = \tilde{\mathbf{\Sigma}}^{-1} \left( \mathbf{\Lambda} \right ) \tilde{\mathbf{\Sigma}}^{-1} = \mathbf{I}
$$

Lemma: Let $\mathbf{V} _1 \in \mathbb{C}^{n\times k}$ be semi-unitary; i.e., $\mathbf{V} _1^H \mathbf{V} _1 = \mathbf{I}$ (but not $\mathbf{V} _1 \mathbf{V} _1^H = \mathbf{I}$). There exists $\mathbf{V} _2 \in \mathbb{C}^{n \times \left(n-k\right)}$, such that $\mathbf{V} = \begin{bmatrix} \mathbf{V} _1 & \mathbf{V} _2 \end{bmatrix}$ is unitary.

## Full proof

$$
\mathbf{A} \mathbf{A}^H =
\begin{bmatrix}
 \mathbf{U}_1 & \mathbf{U} _2
\end{bmatrix}
\begin{bmatrix}
\tilde{\mathbf{\Lambda}} & \mathbf{0} \\ \mathbf{0} & \mathbf{0}
\end{bmatrix}
\begin{bmatrix}
\mathbf{U}_1^H \\ \mathbf{U} _2^H
\end{bmatrix}
$$
$\tilde{\mathbf{\Lambda}} = \mathrm{Diag}\left(\lambda _1, \cdots , \lambda _r \right )$, $\lambda _1 \geq \lambda _2 \geq \cdots \geq \lambda _n > 0$, $r = \\# \text{ nonzero eigenvalues of } \mathbf{A} \mathbf{A}^H$.

We have $\mathbf{U} _2 \mathbf{A} = \mathbf{0}$.

Consider
$$
\begin{array}{rcl}
\mathbf{U} _2 \left( \mathbf{A} \mathbf{A}^H \right) \mathbf{U} _2 & = &
\begin{bmatrix}
\mathbf{U} _2^H \mathbf{U} _1 & \mathbf{U} _2^H \mathbf{U} _2
\end{bmatrix}
\begin{bmatrix}
\tilde{\mathbf{\Lambda}} & \mathbf{0} \\ \mathbf{0} & \mathbf{0}
\end{bmatrix}
\begin{bmatrix}
\mathbf{U} _1^H \mathbf{U} _2 \\ \mathbf{U} _2^H \mathbf{U} _1
\end{bmatrix} \\
& = &
\begin{bmatrix}
\mathbf{0} & \mathbf{I}
\end{bmatrix}
\begin{bmatrix}
\tilde{\mathbf{\Lambda}} & \mathbf{0} \\ \mathbf{0} & \mathbf{0}
\end{bmatrix}
\begin{bmatrix}
\mathbf{0} \\ \mathbf{I}
\end{bmatrix} \\ 
& = & \mathbf{0}
\end{array}
$$

So, we have $\mathbf{U} _2^H \mathbf{A} \left( \mathbf{A} ^H \mathbf{U} _2 \right) = \mathbf{0} \Leftrightarrow \mathbf{U} _2^H \mathbf{A} = \mathbf{0}$

Choose

$$
\tilde{\mathbf{\Sigma}} = \tilde{\mathbf{\Lambda}} ^{\frac{1}{2}} \in \mathbb{R}^{r\times r}
$$

$$
\mathbf{V} _1 = \mathbf{A} ^H \mathbf{U} _1 \tilde{\mathbf{\Sigma}}^{-1} \in \mathbb{C}^{n\times n}
$$

and obtain $\mathbf{V} _2$ from the lemma above.

$$
\begin{array}{rl}
& \mathbf{V} _1 = \mathbf{A} \mathbf{U} _1 \tilde{\mathbf{\Sigma}}^{-1} \\
\Rightarrow & \mathbf{V} _1 \tilde{\mathbf{\Sigma}} = \mathbf{A} \mathbf{U} _1 \\\tilde{\mathbf{\Sigma}} \mathbf{V} _1 = \mathbf{U} _1^H \mathbf{A}
\Rightarrow &
\end{array}
$$

$$
\begin{array}{rcl}
\mathbf{U} ^H \mathbf{A} \mathbf{V} =
\begin{bmatrix}
\mathbf{U}_1^H \\ \mathbf{U} _2^H
\end{bmatrix} \mathbf{A}
\begin{bmatrix}
\mathbf{V} _1 & \mathbf{V} _2
\end{bmatrix} & = &
\begin{bmatrix}
\mathbf{U} _1^H \mathbf{A} \mathbf{V} _1 & \mathbf{U} _1^H \mathbf{A} \mathbf{V} _2 \\
\mathbf{U} _2^H \mathbf{A} \mathbf{V} _1 & \mathbf{U} _2^H \mathbf{A} \mathbf{V} _2
\end{bmatrix} \\
& = &
\begin{bmatrix}
\tilde{\mathbf{\Sigma}} \mathbf{V} _1^H \mathbf{V} _1 & \tilde{\mathbf{\Sigma}} \mathbf{V} _1^H \mathbf{V} _2 \\
\mathbf{0} & \mathbf{0}
\end{bmatrix} \\
& = &
\begin{bmatrix}
\tilde{\mathbf{\Sigma}} & \mathbf{0} \\ 
\mathbf{0} & \mathbf{0}
\end{bmatrix}
\end{array}
$$

# Properties of SVD

- $R\left(\mathbf{A}\right) = R\left(\mathbf{U} _1 \right)$
- $R\left(\mathbf{A}\right) _{\perp} = R\left(\mathbf{U} _2 \right)$
- rank$\left( \mathbf{A} \right) = r$
- $R\left(\mathbf{A}^H\right) = R\left(\mathbf{V} _1 \right)$
- $N\left(\mathbf{A}\right) = R\left(\mathbf{V} _2 \right)$

where $R\left(\mathbf{A}\right) = \left \\{ \mathbf{z} \in \mathbb{C}^m \mid \mathbf{z}^H \mathbf{y} = 0\ \forall \mathbf{y} \in R\left(\mathbf{A}\right) \right \\}$

Any $\mathbf{y}\in \mathbb{C}^{m}$ can be written as

$$
\begin{array}{rcl}
\mathbf{y} & = & \mathbf{U} \mathbf{\alpha} \quad \text{for some } \mathbf{\alpha}\ in\ \mathbb{C}^{m} \\
& = & \mathbf{U} _1 \mathbf{\alpha} _1 + \mathbf{U} _2 \mathbf{\alpha} _2
\end{array}
$$

For any $\mathbf{z}\in R\left(\mathbf{A}\right)$ (which means $z=\mathbf{U} _1 \mathbf{\beta}$ for some $\mathbf{\beta}$)
