---
title: Star Sets
date: 2021-09-21
categories: [Tutorials]
tags: [Verification, Star Sets]     # TAG names should always be lowercase
math: true
---

The star set, introduced in {% cite tran2019star %}, is a construction that is central to my work right now, so I wrote up this tutorial to help explain it in a simple, motivated way to my collaborators. I'm sharing it here now for my students, and so that I have something to which I can direct the curious.

(Note: this post is a work in progress, expect it to change)

### Motivation

Star sets are a verification method created for calculating reachability[^1], i.e., answering the question of "if the set of all possible inputs is $X$, what are all possible outputs $Y$?". The typical use of this question and answer in control is to decide whether the set of possible outputs overlaps at all with an unsafe set. Any non-empty intersection indicates an intervention to prevent the potentially unsafe action.


### Definitions


We'll start by understanding what Star Sets are trying to accomplish, to represent neural network transformations on entire sets rather than individual inputs. The simplest case works best with DNNs defined only with fully connected (FC) layers and ReLU activations, so that's what we'll use in this tutorial. To establish some definitions, neural networks can be expressed as $y = f(x)$ where

$$
x\in\mathbb{R}^m, y\in\mathbb{R}^n, f: \mathbb{R}^m\to\mathbb{R}^n
$$

We're interested in efficiently calculating $Y = f(X)$ where $f$ is the same, but we want to work with sets, 
$$
X\subseteq\mathbb{R}^m, Y=\{y|y=f(x), x\in X\}
$$

The question is how to represent the sets so that $Y$ can be calculated efficiently?

### Post Script

#### Bibliography

{% bibliography --cited %}

#### Footnotes

[^1]: I'm using them for something different, but that's for another post.