---
layout: post
title: "BART: Denoising Sequence-to-Sequence Pre-training for Natural Language Generation, Translation, and Comprehension"
category: Paper_Reading
comments: false
---

- toc
{:toc}

## tl;dr

BART is denoising autoencoder built with a sequence-to-sequence model that is applicable to a very wide range of end tasks. Denoising autoencoder is a model trained to reconstruct text where a random subset of the words has been masked out. BART somehow combines the BERT and GPT2.

## Pretraining

```
                      A  B  C  D  E
                      ▲  ▲  ▲  ▲  ▲
┌───────────────┐   ┌─┴──┴──┴──┴──┴─┐
│ Bidirectional │   │Autoregressive │
│    Encoder    │══▶│    Decoder    │
│<------------->│   │-------------->│
└─▲──▲──▲──▲──▲─┘   └─▲──▲──▲──▲──▲─┘
  │  │  │  │  │       │  │  │  │  │
  A  _  B  _  E      <s> A  B  C  D
```

**Pretraining has two stages**:
1. Text is corrupted with an arbitrary noising function.
2. A sequence-to-sequence model is learned to reconstruct the original text.

**Key advantage**:
- Noising flexibility; where arbitrary transformations can be applied to the original text, including changing its length.

**Noising approaches**:
```
┌─────────────┐   ┌─────────────┐    ┌─────────────┐
│A _ C . _ E .│   │D E . A B C .│    │C . D E . A B│
│   Masking   │   │ Permutation │    │  Rotation   │
└─────────────┘   └─────────────┘    └─────────────┘
       │                 │                  │
       └─────────────────┼──────────────────┘
┌─────────────┐          ▼           ┌─────────────┐
│ A . C . E . │   ┌─────────────┐    │A _ . D _ E .│
│  Deletion   │──▶│A B C . D E .│◀───│  Infilling  │
└─────────────┘   └─────────────┘    └─────────────┘
```
- [ ] Token Masking: random tokens are sampled and replaced with `[Mask]` elements.
- [ ] Token Deletion: Random tokens are deleted from the input.
- [X] Text Infilling: A number of text spans are sampled, with span lengths drawn from a Poisson distribution ($\lambda=3$). Each span is replaced with a single `[Mask]` token.
- [X] Sentence Permutation: A document is divided into sentences bashed on full stops, and these sentences are shuffled in a random order.
- [ ] Document Rotation: A token is chosen uniformly at random, and the document is rotated so that it begins with that token.

## Fine-tuning

### Sequence Classification Tasks

```
                                   label
                                     ▲
┌───────────────┐   ┌────────────────┴─┐
│  Pre-trained  │   │    Pre-trained   │
│    Encoder    │══▶│      Decoder     │
│<------------->│   │  --------------> │
└─▲──▲──▲──▲──▲─┘   └─▲──▲──▲──▲──▲──▲─┘
  │  │  │  │  │       │  │  │  │  │  │
  A  B  C  D  E      <s> A  B  C  D  E
```
- Encoder input: the input sequence
- Decoder input: same as encoder input
- Classifier input: the final hidden state of the final decoder token

### Token Classification Tasks

- Encoder input: the input sequence
- Decoder input: same as encoder input
- Classifier input: top hidden state of the decoder as a representation for each token

### Sequence Generation Tasks

- Encoder input: the input sequence
- Decoder generates outputs autoregressively

### Machine Translation

```
                        A  B  C  D  E
                        ▲  ▲  ▲  ▲  ▲
  ┌───────────────┐   ┌─┴──┴──┴──┴──┴─┐
  │  Pre-trained  │   │  Pre-trained  │
  │    Encoder    │══▶│    Decoder    │
  │<------------->│   │-------------->│
  └─▲──▲──▲──▲──▲─┘   └─▲──▲──▲──▲──▲─┘
┌───┴──┴──┴──┴──┴───┐   │  │  │  │  │
│     Randomly      │  <s> A  B  C  D
│Initialized Encoder│
│  <------------->  │
└───▲──▲──▲──▲──▲───┘
    │  │  │  │  │
    α  β  γ  δ  ε
```
[Edunov et al. (2019)](https://arxiv.org/abs/1903.09722) has shown that models can be improved by incorporating pre-trained encoders, but gains from using pre-trained language models in decoders have been limited. Nevertheless, BART use the entire pre-trained encoder and decoder as a single pre-trained decoder for machine translation, by adding a new set of encoder parameters that are learned from bitext.

Train source encoder in two steps:
1. Freeze most of BART parameters and only update the randomly initialized source encoder, the BART positional embeddings, and the self-attention input projection matrix of BART's encoder first layer.
2. Train all model parameters for a small number of iterations.

## Thoughts

- There may be a significant potential for development of other new noising schemes for document corruption.

## Further Readings

- [Full Paper](https://arxiv.org/abs/1910.13461)
