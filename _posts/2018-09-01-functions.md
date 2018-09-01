---
layout: post
title:  "Functions"
date:   2018-09-01
author: "Huang Qiang"
tags: [discrete math, set theory]
comments: true
---

Definition: A _function_ $$f\ A \rightarrow B$$ from a _domain_ $$A$$ to a _codomain_ $$B$$ is a relation from $$A$$ to $$B$$ with the special properties (using the relation notation $$a\ f\ b$$:

1. $$\forall a \in A\ \exists b \in B\ a\ f\ b$$ ($$f$$ is totally defined)
2. $$\forall a \in A\ \forall b, b' \in B (a\ f\ b \wedge a\ f\ b' \rightarrow b = b')$$ ($$f$$ is well defined)

The first property means there's always a $$b$$ in $$B$$ that $$a$$ can map to. The second property means there's only one $$b$$ that $$a$$ can map to.

How many functions are possible from a set of $$x$$ elements to set of $$y$$ elements?

$$n(f) = y \times y \times y... \times y=y^x$$

![](../images/types_of_functions.png)

### Injective

If $$x_1 \not= x_2$$, then $$f(x_1) \not=f(x_2)$$,i.e., not two distinct values are mapped to the same function value (there are no "collisions").

How many injective functions are possible from a set of $$x$$ elements to set of $$y$$ elements?

$$n(f) = y \times (y-1) \times (y-2)...(y-(x-1)) = y_{p_x}$$

If $$x=y$$, then $$n(f) = n!$$

### Surjective (onto)

Let $$f: X \rightarrow Y$$

$$f$$ is surjective iff $$\forall y \in Y, \exists x \in X$$ such that $$f(x) = y$$ (every value in the codomain is taken on for some argument).

### Bijective (one-to-one)

If it is both injective and surjective.