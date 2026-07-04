---
layout: blog_post
title: An Intro to Star Sets
date: 2021-09-21
---

The star set, introduced in Tran et al. (2019), is a construction that is central to my work right now, so I wrote up this tutorial to help explain it and build intuition for my collaborators. I'm sharing it here for my students, and so that I have something to point the curious toward.

### Motivation

Star sets are a verification method for calculating the *reachability* of deep neural networks: if the set of all possible inputs is $X$, what is the set $Y$ of all possible outputs?

The usual reason to ask this in control is to decide whether the set of commands a deep neural network (DNN) controller could output overlaps at all with a pre-computed set of unsafe commands. Any non-empty intersection between the output set and the unsafe set signals that an intervention is needed to guarantee the unsafe action is never taken. Most existing work uses star sets for exactly this; my own work aims to widen the range of problems they can handle.

Without set-based reachability, your instinct might reasonably be to sample random points from the input set and push them through the network to approximate the output set. Sampling is useful for exploring individual trajectories, but it can only ever give you a lower bound on what's reachable — you might miss the one input that produces an unsafe output. Star sets instead let us reason *symbolically* about entire sets of inputs at once, which is what makes them a clean and efficient abstraction for neural network verification and hybrid system analysis.

TL;DR: piecewise-linear DNNs are built entirely out of affine transformations, and sets defined by linear inequalities are closed under affine transformation. If you keep everything linear, computing exact output sets is mostly a matter of careful bookkeeping.

### Basic Definitions

This section leans on notation for readers who like it. If you only want the main idea, skip the typeset math — the text alone will carry you through everything that follows.

The function represented by a DNN can be written as $y = f(x)$, where

$$
x\in\mathbb{R}^m,\quad y\in\mathbb{R}^n,\quad f: \mathbb{R}^m\to\mathbb{R}^n .
$$

Instead of a single input point, we want to compute the output set $Y = f(X)$ corresponding to a whole input set $X$:

$$
X\subseteq\mathbb{R}^m,\qquad Y=\{\,y \mid y=f(x),\ x\in X\,\}.
$$

To make $Y$ computable, we lean on two assumptions: the DNN is a composition of piecewise-linear functions, and the input set is defined by linear constraints. Both can be relaxed, but only at a cost I'll set aside here. For the input set, we take a *polytope* — an intersection of linear halfspaces:

$$
X = \bigcap_i \{\, x \mid a_i^\top x \le b_i \,\}.
$$

This is stored compactly as a matrix–vector pair $(C, d)$: each row of $C$ holds the coefficients of one halfspace inequality, each column corresponds to a dimension of the space, and each entry of $d$ is the right-hand side. A point is in the set exactly when it satisfies every constraint at once, i.e. $Cx \le d$.

A **star set** pairs such a predicate with an affine map. Concretely, we carry along an affine function $f^{\text{trans}}(\alpha) = A\alpha + b$ together with a predicate $C\alpha \le d$ over the variables $\alpha$. The set it represents is the image of the predicate polytope under that map:

$$
\Theta = \{\, A\alpha + b \;\mid\; C\alpha \le d \,\}.
$$

Postpone, for a moment, the natural urge to ask how a single affine map can stand in for a nonlinear network — I'll get to that below. You'll sometimes see the same object written as a *center* $b$, a set of *basis vectors* (the columns of $A$), and a *predicate* $(C, d)$; that's the same thing, just named piece by piece. When $A$ is square and invertible, the output set has the closed form

$$
Y = \{\, A\alpha + b \mid C\alpha \le d \,\} = \{\, y \mid C A^{-1}(y - b) \le d \,\},
$$

but we won't rely on invertibility in general — keeping the map and predicate separate is what lets everything stay linear.

### Converting a DNN to star sets

With a star set in hand, here's how to use it to compute a DNN's output set. We walk through the network layer by layer, updating the star to represent the output of each layer's transformation. When a layer is piecewise linear, we split the star into pieces, one per linear region.

To make the layer structure explicit, rewrite an $n$-layer network as

$$
f = f_n \circ f_{n-1} \circ \cdots \circ f_2 \circ f_1 = \text{aff}_n \circ \text{ReLU}_{n-1} \circ \text{aff}_{n-1} \circ \cdots \circ \text{ReLU}_1 \circ \text{aff}_1,
$$

where the final layer is left unactivated. We now need to apply each affine map and each ReLU to a star set and get star sets back out.

**Affine layers are easy.** Suppose the current star is $\Theta = \{\, A\alpha + b \mid C\alpha \le d \,\}$ and the next layer is $\text{aff}_i(x) = W_i x + c_i$. Composing the two affine maps gives

$$
W_i(A\alpha + b) + c_i = (W_i A)\,\alpha + (W_i b + c_i),
$$

so the new star has map matrix $A' = W_i A$ and offset $b' = W_i b + c_i$, with the predicate $(C, d)$ untouched. The value stored in every coordinate is still an affine function of the predicate variables $\alpha$ — exactly the invariant we want to preserve.

**ReLU layers are where the branching happens.** ReLU applies $\max(0, \cdot)$ elementwise, so we handle it one coordinate at a time. In the current star, coordinate $i$ is the affine function $x_i = A_{i,:}\,\alpha + b_i$. Its sign is what ReLU cares about, so we split the star into two:

- **Active case ($x_i \ge 0$):** add the constraint $A_{i,:}\,\alpha \ge -b_i$ to the predicate and leave coordinate $i$ alone — on this piece the ReLU is the identity.
- **Inactive case ($x_i \le 0$):** add the constraint $A_{i,:}\,\alpha \le -b_i$ to the predicate, then zero out coordinate $i$ by setting row $i$ of $A$ and entry $i$ of $b$ to $0$ — on this piece the ReLU clamps the coordinate to zero.

Each case just *adds a linear inequality* to the predicate and *edits the affine map*, so each piece is still a star. That's the whole trick: the predicate stays a conjunction of linear inequalities, the coordinates stay affine in $\alpha$, and ReLU never breaks either property.

The catch is that every neuron can double the number of stars, so a layer with $k$ neurons produces up to $2^k$ of them and the count can explode with depth. In practice you tame this by checking each neuron for *stability* first: solve a small linear program to ask whether $x_i$ can ever be negative (or ever be positive) over the current predicate. If a neuron is provably always active or always inactive, there's no branch — you just apply the identity or the zero map. You only pay the $2\times$ cost on the genuinely *unstable* neurons whose sign the input set can't pin down. The result is an *exact* representation of the output set as a union of stars, one per activation pattern that's actually reachable.

### Checking safety

Once the layers are processed, the output set $Y$ is a union of stars $\{\Theta_1, \dots, \Theta_k\}$. To check safety we intersect each one with the unsafe set, itself a polytope $U = \{\, y \mid G y \le h \,\}$. A star $\Theta_j = \{\, A\alpha + b \mid C\alpha \le d\,\}$ intersects $U$ exactly when the system

$$
C\alpha \le d, \qquad G(A\alpha + b) \le h
$$

is feasible — a single linear-programming feasibility check over $\alpha$. If every output star has an empty intersection with $U$, then no input in $X$ can drive the network to an unsafe output, and we've *proven* the controller safe over that input set. That guarantee, rather than a pile of passing test cases, is the whole point.

### Wrapping up

Star sets get their power from a simple observation: piecewise-linear networks only ever apply affine maps and sign-based splits, and both preserve "an affine function subject to linear constraints." Affine layers compose into the map for free, ReLU layers add constraints and branch, and safety reduces to a feasibility LP. The main cost is the branching, and most practical work — including a good chunk of mine — is about controlling that blow-up so exact reachability stays tractable on real networks.

### References

- Hoang-Dung Tran, Diego Manzanas Lopez, Patrick Musau, Xiaodong Yang, Luan Viet Nguyen, Weiming Xiang, and Taylor T. Johnson. *Star-based reachability analysis of deep neural networks.* International Symposium on Formal Methods (FM), 2019.
- Hoang-Dung Tran et al. *NNV: The Neural Network Verification Tool for Deep Neural Networks and Learning-Enabled Cyber-Physical Systems.* International Conference on Computer Aided Verification (CAV), 2020.
