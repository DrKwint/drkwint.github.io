---
title: An Intro to Star Sets
date: 2021-09-21
categories: [Tutorials]
tags: [verification, star sets]     # TAG names should always be lowercase
math: true
---

The star set, introduced in {% cite tran2019star %}, is a construction that is central to my work right now, so I wrote up this tutorial to help explain it in a simple, motivated way to my collaborators. I'm sharing it here now for my students, and so that I have something to which I can direct the curious.

(Note: this post is a work in progress, expect it to change)

### Motivation

Star sets are a verification method created for calculating reachability[^1], i.e., answering the question of "if the set of all possible inputs is $X$, what are all possible outputs $Y$?".
The typical use of this question and answer in control is to decide whether the set of possible control commands overlaps at all with an unsafe set.
Any non-empty intersection indicates an intervention to prevent the potentially unsafe action.

### Definitions

This section is going to use a lot of notation for readers who benefit from such things. If you're only interested in the main idea, don't worry about the typeset math. Just read the text and you'll be able to understand everything that follows just as well.

We'll start by understanding what Star Sets are trying to accomplish: representing neural network transformations on entire sets rather than individual inputs.
The simplest case works best with DNNs defined only with fully connected (FC) layers and ReLU activations, so that's what we'll use in this tutorial.
To establish some definitions, neural networks can be written as $y = f(x)$ where

$$
x\in\mathbb{R}^m, y\in\mathbb{R}^n, f: \mathbb{R}^m\to\mathbb{R}^n
$$

We're interested in efficiently calculating $Y = f(X)$ where $f$ is the same, but instead of indiviual inputs, we want to work with sets,

$$
X\subseteq\mathbb{R}^m, Y=\{y|y=f(x), x\in X\}
$$

The question is how to represent the sets so that $Y$ can be calculated efficiently? This can be accomplished by taking advantage of the network's form. Because we're only considering neural networks with FC layers and ReLU activations, we'll rewrite our $n$-layer DNN definition as

$$
\begin{aligned}
f &= f_n \circ f_{n-1} \circ\cdots\circ f_2\circ f_1 \\&= \text{aff}_n\circ\text{ReLU}_{n-1}\circ\text{aff}_{n-1}\circ\cdots\circ\text{ReLU}_1\circ\text{aff}_1
\end{aligned}
$$

where $f_i$ is the $i$th layer of the network and the final layer of the network isn't activated.

If we can apply both affine and ReLU functions to a set, then we can calculate the DNN's output set.
Applying an affine transformation to a set is straightforward as

$$
\begin{aligned}
    \text{aff}_i(x) &= W_i x + b_i
\end{aligned}
$$

Using a set as input to each layer, using ReLU activations means that each layer's output set to being closed under affine and ReLU operations.

### Post Script

#### Bibliography

{% bibliography --cited %}

#### Footnotes

[^1]: I'm using them for something different, but that's for another post.
