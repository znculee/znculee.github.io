---
layout: post
title: Revisiting Self-Training for Neural Sequence Generation
category: Paper_Reading
comments: false
---

- toc
{:toc}

## tl;dr

This paper points out self-training works on neural sequence generation mainly because dropout perturb the hidden states. Following this observation, it proposes the noisy self-training that perturb the inputs during pseudo-labelling the unlabelled data. The experiments show noisy self-training can significantly improve the generation performance by smoothing the prediction space.

## Self-training

### Classic Self-training

- Train a base model $f _\theta$ on $L=\\{x _i, y _i\\} ^l _{i=1}$
- **repeat**
  - Apply $f _\theta$ to the unlabeled instances $U$
  - Select a subset $S \subset \\{(x, f _\theta(x))\mid x\in U\\}$
  - Train a new model $f _\theta$ on $S \cup L$
- **until** convergence or maximum iterations are reached

The unsupervised loss is defined as

$$\mathcal{L} _U = -\mathbb{E} _{x \sim p(x)} \mathbb{E} _{y \sim p _{\theta *} (y\mid x \log p _\theta (y\mid x))}$$

where $p(x)$ is the empirical data distribution approximated with samples from $S$, $p(y\mid x)$ is the conditional distribution defined by the model. $\theta *$ is set as the parameter of the supervised baseline and fixed.

### Selection of The Subset $S$

- [ ] **S** is usually selected based on some confidence scores (e.g. log probability).
- [x] It is also possible for **S** to be the whole pseudo parallel data.

### Combination of Real and Pseudo Parallel data
- [ ] Introduce a hyper-parameter to weigh the importance of the parallel data relative to the pseudo data **S**.
- [x] **Separate Training Strategy** produce better or equal performance for neural sequence generation while being simpler. First train the model on pseudo parallel data $S$ (pseudo-training, PT), and then fine-tune it on real data $L$ (fine-tuning, FT).

## The Secret Behind Self-training

Removing dropout in the pseudo training step decrease the BLEU scores of self training.

Methods             | PT   | FT
--                  | --   | --
baseline            | --   | 15.6
ST w/o dropout      | 15.8 | 16.3
ST w/ dropout       | 16.5 | 17.5
Noisy ST w/o        | 15.8 | 17.9
Noisy ST w/ dropout | 16.6 | 19.3

## Noisy Self-training

See an interesting toy experiment to analyze the role of noise in section 4.1 of the paper to understand how noise affect generation by improving the smoothness in prediction space.

Inspired by dropout benefit self-training by perturbing hidden states, authors propose the noisy self-training to perturb the inputs.

The loss function in classic self-training would be modified to:

$$\mathcal{L} _U = -\mathbb{E} _{x' \sim g(x), x \sim p(x)} \mathbb{E} _{y \sim p _{\theta *} (y\mid x \log p _\theta (y\mid x'))}$$

where $g(x)$ is a perturbation function. In terms of machine translation, the authors try two different perturbation functions:

- [x] Synthetic noise where the input tokens are randomly dropped, masked and shuffled.
- [ ] Paraphrasing where the source sentences are translated to other language then translated back.

## Thoughts

The noisy self-training would be an efficient trick for sequence generation tasks with small-size training data. Adding appropriate noise to the inputs may smooth the prediction space like "erode" in the computer vision where both errors and good predictions may be eroded by surroundings. The smoothing effect cannot guarantee a performance gain, but fine-tuning benefits from it may because the errors are gathered and eliminated more efficiently.

## Further Readings

- [Full Paper](https://openreview.net/forum?id=SJgdnAVKDH)
