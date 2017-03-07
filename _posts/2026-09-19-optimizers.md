---
layout: blog
title: Optimizers
---

- toc
{:toc}

Optimizers developed along three separate lines: SGD to momentum,
AdaGrad to RMSProp to Adam to AdamW,
and full-matrix preconditioning to Shampoo to orthogonalized updates.
Muon is often read as the next Adam, but it comes from the third line,
and the three only met after 2024.

# Steepest Descent

Given parameters $\theta \in \mathbb{R}^d$ and a loss $L(\theta)$,
the first-order expansion around the current point is

$$
L(\theta + \Delta) \approx L(\theta) + g ^\top \Delta, \quad g = \nabla L(\theta)
$$

$L(\theta)$ is constant, so minimizing the approximation means minimizing $g ^\top \Delta$.
This is linear in $\Delta$ and has no lower bound,
since scaling up any $\Delta$ with $g ^\top \Delta < 0$ drives it to $-\infty$.
The expansion is only valid near the current point, so the step has to be constrained,

$$
\min \limits _{\Delta} g ^\top \Delta \quad \mathrm{s.t.} \quad \|\Delta\| _2 \le \epsilon
$$

Squaring the constraint keeps it differentiable,
so the Lagrangian is $g ^\top \Delta + \lambda ( \\|\Delta\\| _2 ^2 - \epsilon ^2 )$.
Dropping the constant and writing $\lambda = 1 / 2\eta$ leaves a penalty on the step length,

$$
\Delta ^* = \arg \min \limits _{\Delta} \left[ g ^\top \Delta + \frac{1}{2\eta} \|\Delta\| _2 ^2 \right]
$$

Setting the derivative to zero gives $g + \Delta / \eta = 0$, so

$$
\Delta ^* = -\eta g
$$

and $\theta _{t+1} = \theta _t - \eta g _t$ is SGD.

The constraint binds whenever $g \neq 0$, since a linear objective has no interior minimum,
so $\\|\Delta ^*\\| _2 = \eta \\|g\\| _2 = \epsilon$.
Fixing $\epsilon$ therefore means a step of constant length,
while fixing $\eta$ means one proportional to $\\|g\\| _2$.
The optimizers below are all this construction with a different measure of step length.

# Anisotropy

Consider a quadratic loss with Hessian $H \succeq 0$,

$$
L(\theta) = \frac{1}{2} \theta ^\top H \theta, \quad g = H \theta
$$

Gradient descent is then a linear map,

$$
\theta _{t+1} = (I - \eta H) \theta _t
$$

Diagonalize $H = Q \Lambda Q ^\top$ and change coordinates to $z = Q ^\top \theta$.
Every eigendirection now evolves on its own,

$$
z _{i,t+1} = (1 - \eta \lambda _i) z _{i,t}
$$

Stability in the steepest direction requires $\eta < 2 / \lambda _{\max}$.
With $\eta \approx 1 / \lambda _{\max}$, the flattest direction contracts by

$$
1 - \eta \lambda _{\min} \approx 1 - \frac{1}{\kappa}, \quad \kappa = \frac{\lambda _{\max}}{\lambda _{\min}}
$$

So a single scalar $\eta$ serves every direction at once.
When $\kappa$ is large, the steep directions oscillate while the flat ones barely move.
Momentum, Adam, Shampoo and Muon all address this.

# Momentum

Polyak's heavy ball accumulates gradients before stepping,

$$
v _t = \beta v _{t-1} + g _t, \quad \theta _{t+1} = \theta _t - \eta v _t
$$

Unrolling,

$$
v _t = g _t + \beta g _{t-1} + \beta ^2 g _{t-2} + \cdots
$$

which is a low-pass filter over the gradient history.
A direction whose gradient keeps the same sign adds up,
and a direction that flips sign every step cancels itself out.
So the flat directions accumulate and the steep ones stop oscillating.

Rewriting in terms of positions, since $\theta _t - \theta _{t-1} = -\eta v _{t-1}$,

$$
\theta _{t+1} = \theta _t - \eta \nabla L(\theta _t) + \beta (\theta _t - \theta _{t-1})
$$

The last term is inertia, which is where the name comes from.
The gradient acts as gravity, $v$ is velocity, and $\beta$ controls how heavy the ball is.

# AdaGrad

Momentum still gives every coordinate the same learning rate.
If one coordinate sees gradients of size $10 ^{-3}$ and another of size $10 ^2$,
no single $\eta$ is right for both.

AdaGrad accumulates squared gradients per coordinate and divides by them, all element-wise,

$$
s _t = s _{t-1} + g _t \odot g _t, \quad \theta _{t+1} = \theta _t - \eta \frac{g _t}{\sqrt{s _t} + \epsilon}
$$

A coordinate that keeps producing large gradients gets an effective rate $\eta / \sqrt{s _i}$ that shrinks,
while a rare one keeps a large step.
This was a good fit for sparse features.

However, $s _t = \sum _{\tau=1} ^t g _\tau ^2$ only grows,
so the effective learning rate decays monotonically and the updates eventually nearly stop.

# RMSProp

Replace the cumulative sum with an exponential moving average,

$$
v _t = \beta _2 v _{t-1} + (1 - \beta _2) g _t ^2, \quad \theta _{t+1} = \theta _t - \eta \frac{g _t}{\sqrt{v _t} + \epsilon}
$$

Now $\sqrt{v _t}$ is the recent RMS magnitude of that coordinate's gradient rather than its whole history,
so the step is the gradient divided by its own recent scale.

SGD and RMSProp are both of the form

$$
\Delta \theta = -\eta P _t g _t
$$

with $P _t = I$ for SGD and $P _t = \mathrm{diag}(1 / (\sqrt{v _t} + \epsilon))$ for RMSProp.
$P _t$ is a preconditioner, and RMSProp restricts it to be diagonal.

# Adam

Adam keeps both moments,

$$
m _t = \beta _1 m _{t-1} + (1 - \beta _1) g _t
$$

$$
v _t = \beta _2 v _{t-1} + (1 - \beta _2) g _t ^2
$$

$$
\theta _{t+1} = \theta _t - \eta \frac{\hat m _t}{\sqrt{\hat v _t} + \epsilon}
$$

so it is momentum in the numerator and RMSProp in the denominator.

The hats are bias correction.
Both moments start at zero, which drags the early estimates toward zero.
Assuming $\mathbb{E}[g _t] = \mu$ is constant,

$$
	\begin{array}{rcl}
    \mathbb{E}[m _t]
    & = & (1 - \beta _1) \sum \limits _{k=0} ^{t-1} \beta _1 ^k \mu \\
    & = & (1 - \beta _1) \frac{1 - \beta _1 ^t}{1 - \beta _1} \mu \\
    & = & (1 - \beta _1 ^t) \mu
	\end{array}
$$

which falls short of $\mu$ by a factor of $1 - \beta _1 ^t$. Dividing it out gives

$$
\hat m _t = \frac{m _t}{1 - \beta _1 ^t}, \quad \hat v _t = \frac{v _t}{1 - \beta _2 ^t}
$$

# Adam as Sign Descent

Ignoring the averaging and setting $m = g$, $v = g ^2$, the Adam step becomes

$$
\frac{g}{\sqrt{g ^2}} = \mathrm{sign}(g)
$$

In this limit the step is insensitive to whether $|g _i|$ is $0.1$ or $1000$, and depends only on the sign.
SGD moves proportionally to $g _i$, while Adam moves by roughly $\mathrm{sign}(g _i)$.

The same direction comes out of the first-order problem
if the step is bounded in $\ell _\infty$ instead of $\ell _2$,

$$
\min \limits _{\|\Delta\| _{\infty} \le \eta} g ^\top \Delta
$$

The coordinates are independent, so each is pushed to its own boundary,

$$
\Delta _i = -\eta \, \mathrm{sign}(g _i)
$$

So SGD is steepest descent under $\ell _2$, and Adam is close to steepest descent under $\ell _\infty$.
Muon changes the norm again.

# AdamW

Add L2 regularization,

$$
L _{\mathrm{reg}}(\theta) = L(\theta) + \frac{\lambda}{2} \|\theta\| _2 ^2, \quad \nabla L _{\mathrm{reg}} = g + \lambda \theta
$$

Under SGD this is equivalent to weight decay,

$$
\theta _{t+1} = \theta _t - \eta (g + \lambda \theta _t) = (1 - \eta \lambda) \theta _t - \eta g
$$

Under Adam it is not. Feeding $g + \lambda \theta$ into the adaptive update gives

$$
\Delta \theta _i = -\eta \frac{g _i + \lambda \theta _i}{\sqrt{v _i}}
$$

so the decay term is $-\eta \lambda \theta _i / \sqrt{v _i}$,
and coordinates with a large gradient history decay less.
The shrinkage is no longer uniform.

AdamW fixes this by not routing the decay through the adaptive step at all,

$$
\theta _{t+1} = (1 - \eta \lambda) \theta _t - \eta \frac{\hat m _t}{\sqrt{\hat v _t} + \epsilon}
$$

Momentum, coordinate-wise RMS normalization and decoupled weight decay
together give the standard LLM optimizer.

# Matrix Structure

A linear layer is $y = Wx$ with $W \in \mathbb{R} ^{m \times n}$.
Adam flattens it into $mn$ scalars and gives each its own second moment,
as if the entries were unrelated.

But $W$ is a linear map, and a change in $W$ acts on the layer output as

$$
\Delta y = \Delta W x, \quad \|\Delta y\| _2 \le \|\Delta W\| _{\mathrm{op}} \|x\| _2
$$

where $\\|\Delta W\\| _{\mathrm{op}} = \sigma _{\max}(\Delta W)$.
So the size of a step in $W$ is naturally measured by the operator norm,
not by the size of individual entries.

The ideal preconditioner would be a full $P \in \mathbb{R} ^{d \times d}$,
with Newton's $\Delta \theta = -H ^{-1} g$ as the extreme case.
At $d = 10 ^9$ that matrix cannot be stored, let alone inverted.
Adam's diagonal is the cheap extreme, and Shampoo sits in between.

# Shampoo

Shampoo builds one preconditioner per tensor axis instead of a single full one.
For a matrix gradient $G \in \mathbb{R} ^{m \times n}$ it keeps a row and a column accumulator,

$$
L _t = \sum _{\tau \le t} G _\tau G _\tau ^\top, \quad R _t = \sum _{\tau \le t} G _\tau ^\top G _\tau
$$

and steps with

$$
\Delta W = -\eta L _t ^{-1/4} G _t R _t ^{-1/4}
$$

This keeps two matrices of size $m \times m$ and $n \times n$ instead of one of size $mn \times mn$,
and handles the row and column geometry separately.

# Shampoo Without Accumulation

Drop the accumulation and keep only the current gradient, so $L = G G ^\top$ and $R = G ^\top G$.
Write the SVD $G = U \Sigma V ^\top$ and assume full rank. Then

$$
L ^{-1/4} = U \Sigma ^{-1/2} U ^\top, \quad R ^{-1/4} = V \Sigma ^{-1/2} V ^\top
$$

and the step collapses,

$$
	\begin{array}{rcl}
    \Delta W
    & = & - L ^{-1/4} G R ^{-1/4} \\
    & = & - U \Sigma ^{-1/2} U ^\top \cdot U \Sigma V ^\top \cdot V \Sigma ^{-1/2} V ^\top \\
    & = & - U \Sigma ^{-1/2} \Sigma \Sigma ^{-1/2} V ^\top \\
    & = & - U V ^\top
	\end{array}
$$

Every singular value cancels, leaving $U V ^\top$,
which keeps the singular directions of $G$ and sets all of their magnitudes to one.
This is the update Muon uses.

# Muon

Muon stands for MomentUm Orthogonalized by Newton-Schulz.
Accumulate momentum on the matrix, take its SVD, and keep only the factors,

$$
M _t = \beta M _{t-1} + G _t, \quad M _t = U \Sigma V ^\top
$$

$$
O _t = U V ^\top, \quad W _{t+1} = W _t - \eta O _t
$$

This flattens the spectrum. If

$$
\Sigma = \mathrm{diag}(100, \; 20, \; 1, \; 0.01)
$$

then Muon replaces it with $\mathrm{diag}(1, 1, 1, 1)$.
The singular values go to one, not to zero,
so the directions are untouched and only the disparity between them is removed.
Under plain momentum the first direction would supply almost the entire step.

The name is literal. For square full-rank $M$,

$$
O ^\top O = V U ^\top U V ^\top = V V ^\top = I
$$

and for rectangular $M$ it is semi-orthogonal, with $O O ^\top = I _m$ when $m < n$.
$U V ^\top$ is also the orthogonal factor of the polar decomposition,
the closest semi-orthogonal matrix to $M$ in Frobenius norm.

Note that momentum comes before orthogonalization.
Raw gradients are noisy, and flattening the spectrum of a noisy matrix
amplifies the noise directions as much as the signal ones.
Momentum denoises along the time axis and orthogonalization equalizes along the singular directions,
so the two are complementary.

# Spectral Steepest Descent

$U V ^\top$ also follows from the norm-constrained problem directly, without going through Shampoo.
Bound the step in operator norm,

$$
\min \limits _{\|\Delta\| _{\mathrm{op}} \le \eta} \langle G, \Delta \rangle, \quad \langle A, B \rangle = \mathrm{tr}(A ^\top B)
$$

The dual of the operator norm is the nuclear norm $\\|G\\| _* = \sum _i \sigma _i (G)$,
so the optimal value is $-\eta \\|G\\| _*$, attained at

$$
\Delta ^* = -\eta U V ^\top
$$

which checks out directly,

$$
\langle G, -\eta U V ^\top \rangle = -\eta \, \mathrm{tr}(V \Sigma U ^\top U V ^\top) = -\eta \, \mathrm{tr}(\Sigma) = -\eta \|G\| _*
$$

So Muon is steepest descent under the spectral norm,
in the same sense that SGD is steepest descent under $\ell _2$ and Adam under $\ell _\infty$.

# Newton-Schulz

A full SVD of every weight matrix at every step is too expensive,
and it also maps poorly onto GPU kernels and low precision.
Muon never computes one, and runs a matrix polynomial instead,

$$
X _{k+1} = a X _k + b (X _k X _k ^\top) X _k + c (X _k X _k ^\top) ^2 X _k
$$

Substituting $X _k = U \Sigma _k V ^\top$,

$$
	\begin{array}{rcl}
    (X _k X _k ^\top) X _k & = & U \Sigma _k ^3 V ^\top \\
    (X _k X _k ^\top) ^2 X _k & = & U \Sigma _k ^5 V ^\top \\
    X _{k+1} & = & U \left( a \Sigma _k + b \Sigma _k ^3 + c \Sigma _k ^5 \right) V ^\top
	\end{array}
$$

$U$ and $V$ never move. The iteration applies a scalar map

$$
\sigma \mapsto a \sigma + b \sigma ^3 + c \sigma ^5
$$

to every singular value in parallel,
and the coefficients are chosen so that repeated application drives $\sigma$ toward one.
Muon uses $(a, b, c) = (3.4445, -4.7750, 2.0315)$ for about five steps.

Every term is built from $X X ^\top$ and products with $X$,
so the whole iteration is GEMM and runs in bfloat16.
Orthogonalizing gradients is an old idea,
and what made it practical is that modern GPUs are very fast at this shape of computation.

# Update Scale

Let $O = U V ^\top$ have rank $r = \min(m, n)$. Every nonzero singular value is one, so

$$
\|O\| _F ^2 = r, \quad \mathrm{RMS}(O) = \frac{\|O\| _F}{\sqrt{mn}} = \sqrt{\frac{\min(m, n)}{mn}} = \frac{1}{\sqrt{\max(m, n)}}
$$

The wider the layer, the smaller the raw update.
A single global learning rate would then mean something different in every layer,
and none of the AdamW hyperparameter intuition would carry over.

The fix is to rescale by shape,

$$
O \leftarrow 0.2 \sqrt{\max(m, n)} \; O
$$

which pins the per-entry RMS near $0.2$ regardless of layer width.

# In Practice

Muon does not apply to every parameter.
It applies to the two-dimensional hidden weights, the ones that really are linear operators:
$W _Q, W _K, W _V, W _O$ and the MLP matrices.
Embeddings, the output head, and all scalar and vector parameters are usually left to AdamW.
An embedding is stored as a matrix but is not used as one,
since a step only touches the rows that appeared in the batch,
so its geometry is not the operator geometry Muon assumes.

Scaling Muon to large models took more than the core update.
Moonshot's Moonlight added weight decay and the update RMS rescaling above,
and reported in its scaling-law experiments that Muon reached AdamW-level quality
at roughly 52% of the training FLOPs,
then used it to train a 16B-total, 3B-active MoE on 5.7T tokens.
That factor is a result on their setup, not a general guarantee.
Kimi K2 pushed the same line to a 1T-parameter MoE and hit a new failure mode:
at that scale Muon produced attention logit explosions more readily than AdamW,
which they handled by clipping the query and key weights, giving MuonClip.
Unlike AdamW, none of this is settled yet, and the design is still changing.

# Angular Step

On many Transformer matrices, weight decay is not really regularization.
A linear map placed after a normalization layer, acting on a hidden state $h$
through a trainable gain $\gamma$, computes

$$
f(h; W, \gamma) = W \left( \gamma \odot \mathrm{RMSNorm}(h) \right)
$$

so rescaling $(W, \gamma)$ to $(cW, \gamma / c)$ leaves the function unchanged,
and the gain absorbs any scale placed on $W$.
If the loss barely depends on $\\|W\\| _F$, penalizing it cannot be why decay helps.

What decay does instead is set a radius.
Write $u$ for the update the base optimizer produces,
so AdamW takes the step $W _{t+1} = (1 - \eta \lambda) W _t - \eta u _t$.
Under stationary isotropic gradients this has an equilibrium norm

$$
\|W\| _F = \Theta \left( \|u\| _F \sqrt{\eta / \lambda} \right)
$$

and that radius controls how fast the direction $W _t / \\|W _t\\| _F$ turns,
since a larger radius means a smaller rotation for the same $\\|u\\| _F$.
Write $\Delta \phi$ for the angle between consecutive directions.
For AdamW at equilibrium,

$$
\Delta \phi \approx \sqrt{\frac{2 \eta \lambda (1 - \beta _1)}{1 + (1 - \eta \lambda) \beta _1}}
\; \propto \; \sqrt{\eta \lambda}
$$

so the rotation rate depends on $\eta$ and $\lambda$ only through their product.
Two AdamW runs with $(\eta, \lambda) = (0.002, 0.2)$ and $(0.004, 0.1)$, matched on $\eta \lambda$,
reach nearly the same loss while their weight norms differ by a factor of two.
The loss tracks $\eta \lambda$ and the norm tracks $\eta$.

So weight decay controls the angular step, but only indirectly,
through whatever radius it happens to equilibrate at.

# Hyperball

Hyperball fixes the radius and the update norm instead of letting them equilibrate.
Set $R = \\|W _0\\| _F$ and normalize both,

$$
W _{t+1} = R \cdot \mathrm{Normalize} \left( W _t - \eta R \cdot \mathrm{Normalize}(u _t) \right),
\quad \mathrm{Normalize}(X) = \frac{X}{\|X\| _F}
$$

Each step moves a distance $\eta R$ along a unit direction
and is then projected back onto the sphere of radius $R$.
Projection is radial, so it leaves the direction alone.
For an update orthogonal to the weight,

$$
\tan (\Delta \phi) = \eta
$$

so the learning rate is the angular step,
with no dependence on layer width, depth, or where weight decay settles.

This is a wrapper rather than an optimizer,
and the base update can be Adam or Muon, giving AdamH and MuonH.
Muon's orthogonalization and Hyperball's normalization do not interfere.
Muon's update is $O = U V ^\top$ with $\\|O\\| _F = \sqrt r$, so

$$
\mathrm{Normalize}(O) = \frac{O}{\sqrt r},
\quad \sigma _i \left( \mathrm{Normalize}(O) \right) = \frac{1}{\sqrt r}
$$

which is a single global scalar.
Muon still sets the relative shape of the spectrum and Hyperball sets only the total norm.
Moonlight's shape-dependent constant is no longer needed,
because each step is already measured against the matrix's own radius.

The constraint is on the Frobenius norm, not the spectral norm the rest of Muon is built on.
Frobenius projection needs one pass over the entries,
while a spectral projection needs an SVD.
The two behave alike when the stable rank

$$
\mathcal{R}(W) = \frac{\|W\| _F ^2}{\|W\| _{\mathrm{op}} ^2} \in \left[ 1, \min(m, n) \right]
$$

sits near its upper bound, meaning no single singular direction dominates.

On Qwen3-style models up to 1.2B parameters,
AdamW needs 20% to 30% more data to match MuonH,
where Muon with decoupled weight decay, MuonWD, forces only about 10% more.
The best learning rate also varies by a factor of about 1.4 across depth and width sweeps,
against 2 to 4 for the weight decay versions.
The supporting theory assumes stationary isotropic Gaussian gradients,
setting aside the anisotropy that motivated Muon in the first place.

# Implicit Schedule

MuonH does not simply run faster.
It starts behind MuonWD and overtakes only in the middle or late stages.
A fixed norm and a steady step size do not by themselves explain a crossover.

Setting $\tan (\Delta \phi) = \eta$ assumed the update was orthogonal to the weight.
A scale-invariant loss does give $\langle W, \nabla L(W) \rangle = 0$ for the raw gradient,
but momentum, preconditioning and orthogonalization all break that.
Writing $c$ for the cosine between $W$ and $u$, the general rotation is

$$
\tan (\Delta \phi) = \frac{\eta \|u\| _F \sqrt{1 - c ^2}}{(1 - \eta \lambda) \|W\| _F - \eta c \|u\| _F}
$$

The numerator is $\eta$ times the tangential part of the update, $\\|u\\| _F \sqrt{1 - c ^2}$,
which is the only part that rotates $W$.
The radial part, $c \\|u\\| _F$, sits in the denominator, where it changes the norm instead.
At $c = 0$ the whole expression reduces to $\eta \\|u\\| _F / (1 - \eta \lambda) \\|W\\| _F$,
which is how effective step size is usually estimated.
Under Hyperball both norms are fixed and cancel,

$$
\tan (\Delta \phi) = \frac{\eta \sqrt{1 - c ^2}}{1 - \eta c}
$$

leaving $c$ as the only state the rotation still depends on.

Hyperball's projection discards the radial part outright,
which is the obvious explanation for the crossover.
But in practice $c$ swings early and then settles into a slowly varying negative regime,
meaning the step keeps a component along $W$ and tends to grow it.
Once settled, the $c$ terms move the rotation far less than the learning rate schedule does,
and far less than the growing weight norm does for MuonWD,
so the radial part is not what produces the crossover.

A sharper test holds the optimizer fixed and changes only the learning rate.
Take MuonH's rotation at every step as a target,
then before each MuonWD step solve the general formula for the $\eta$ that hits it,
and run the same procedure in reverse.
Both directions largely reproduce the other's validation curve,
which places the difference in the size of the effective step rather than its direction.

If the advantage is a schedule, it can be scheduled for directly.
A steeper decay buys MuonH an earlier crossover,
but it then finishes worse than both MuonWD and MuonH on gentler schedules,
and so far no schedule puts it ahead for a whole run,
at least across Muon, MuonWD and MuonH on dense pretraining loss.
What Hyperball changes for certain is what the learning rate means:
a rotation per step rather than a distance in Frobenius norm,
which is why one value transfers across width and depth.
Whether it also reaches a better optimum is not settled.

# Summary

| Optimizer | Sees a parameter as | Geometry | Direction |
| --- | --- | --- | --- |
| SGD | a vector | Euclidean | $g$ |
| Momentum | a vector plus history | Euclidean, smoothed in time | $v$ |
| Adam | a bag of scalars | coordinate-wise | $\hat m / \sqrt{\hat v}$ |
| Shampoo | a structured tensor | row and column | $L ^{-1/4} G R ^{-1/4}$ |
| Muon | a linear operator | spectral | $U V ^\top$ |

AdamW still handles embeddings, the output head, and every scalar and vector parameter.
Muon handles the two-dimensional hidden weights.
It needs the shape rescaling and weight decay to scale,
and MuonClip if attention logits start to grow.
Hyperball is worth trying when learning rate transfer across model sizes
matters more than a tuned schedule.

# Reference

- [A Stochastic Approximation Method](https://doi.org/10.1214/aoms/1177729586)
- [Some methods of speeding up the convergence of iteration methods](<https://doi.org/10.1016/0041-5553(64)90137-5>)
- [Adaptive Subgradient Methods for Online Learning and Stochastic Optimization](https://www.jmlr.org/papers/v12/duchi11a.html) (AdaGrad)
- [Neural Networks for Machine Learning, Lecture 6](https://www.cs.toronto.edu/~tijmen/csc321/slides/lecture_slides_lec6.pdf) (RMSProp)
- [Adam: A Method for Stochastic Optimization](https://arxiv.org/abs/1412.6980)
- [Decoupled Weight Decay Regularization](https://arxiv.org/abs/1711.05101) (AdamW)
- [Shampoo: Preconditioned Stochastic Tensor Optimization](https://arxiv.org/abs/1802.09568)
- [Old Optimizer, New Norm: An Anthology](https://arxiv.org/abs/2409.20325)
- [Muon: An optimizer for hidden layers in neural networks](https://kellerjordan.github.io/posts/muon/)
- [Muon is Scalable for LLM Training](https://arxiv.org/abs/2502.16982) (Moonlight)
- [Kimi K2: Open Agentic Intelligence](https://arxiv.org/abs/2507.20534)
- [Rotational Equilibrium: How Weight Decay Balances Learning Across Neural Networks](https://arxiv.org/abs/2305.17212)
- [Fantastic Pretraining Optimizers and Where to Find Them](https://arxiv.org/abs/2509.02046)
- [Fantastic Pretraining Optimizers and Where to Find Them II: Hyperball Optimization](https://arxiv.org/abs/2606.16899)
- [Hyperball May Not Be a Free Lunch](https://arxiv.org/abs/2607.22444)
