---
layout:   post
title:    Center Kernel in GPU
category: Random_Thoughts
date:     2019-03-12
comments: false
---

- toc
{:toc}

Brain consists of neuron cells, which are working parallel for producing intelligence.
Compared with a neural network, although the tensor operations can be accelerate by GPUs, the lowest-level logic of the whole system still relies on the kernel centered in CPU.
Generally, our perceptions and actions can happen simultaneously instead sequentially because we can implicitly control and communicate with our hardware (body) in parallel.
In terms of a computer, these functions are implemented as a kernel.
Let's imagine, what if we have a new style computer which can control hardware in parallel with thousands of computational cores.

Currently, the most common parallel computing device is GPU, which is widely used in Deep Learning.
However, GPU lacks effective control unit and cache, leading to hard for it to replace CPU as the center of a computer.
But in another perspective, a new neuron based system has not necessary to support the common software in the desktop computers, and the goal of this idea is to put new way to design algorithms for AI tasks.
Behold, there is a GPU based kernel in future, and a numbers of AI applications even a new style operating system may be designed on it.

I've been thinking this for a long time.
I used to believe the best way to build a real parallel computer is to design a new hardware electronically, until recently I read the code of Linux kernel and I found there may be a more effective way that is designing a new kernel which is centered with GPU instead CPU.
This hunch is very meta, and please bless somebody can specify it in future!
