---
layout: post
title:  "Equivalence Relations"
date:   2018-08-23
author: "Huang Qiang"
tags: [discrete math, set theory]
comments: true
---

### Definition

A _equivalence relation_ is a relation on a set $$X$$ that is

- $$(x,x) \in R$$ for all $$x \in X$$ (Reflexive)
- $$(x,y) \in R$$ implies $$(y,x) \in R$$ (Symmetric)
- $$(x,y)\ \text{and} (y,z) \in R$$ imply $$(x,y) \in R$$ (Transitive)

S = {1,2,3,4}

R = {(1,1),(1,2),(2,1),(2,2),(3,3),(4,4),(3,4),(4,3)}

R is an equivalence relation because

- Reflexive: (1,1),(2,2),(3,3),(4,4)
- Symmetric: (1,1),(2,2),(3,3),(4,4),(1,2),(2,1),(3,4)(4,3)
- Transitive:
	- (1,2)(2,1) -> (1,1)
	- (1,2)(2,2) -> (1,2)
	- (2,1)(1,1) -> (2,1)
	- (2,1)(1,2) -> (2,2)
	- (2,2)(2,1) -> (2,1)
	- (3,3)(3,4) -> (3,4)
	- (4,4)(4,3) -> (4,3)
	- (3,4)(4,3) -> (3,3)
	- (4,3)(3,3) -> (4,3)
	- (4,3)(3,4) -> (4,4)

```
⟲   ⟲
① ⇄ ② 
-------
③ ⇄ ④
⟲   ⟲
```
A **partition** $$\mathcal{P}$$ of a set $$X$$ is a collection of nonemtpy set $$X_1,X_2,\ldots$$ such that $$X_i \cap X_j = \emptyset$$ for $$i \not= j$$ and $$\bigcup_k X_k = X$$. Let $$\sim$$ be an equivalence relation on a set $$X$$ and let $$x \in X$$. Then $$[x] = \{y \in X: y \sim x\}$$ is called the **equivalence class** of $$x$$.

Partition = $$\{\{1,2\}, \{3,4\}\}$$

If R = {(1,1),(2,2),(3,3),(4,4),(3,4),(4,3)}, then Hasse diagram would be:

```
⟲ | ⟲ | ⟲   ⟲
① | ② | ③ ⇄ ④
```

Partition = $$\{\{1\},\{2\}, \{3,4\}\}$$

Partition can also be transformed to equivalence relation e.g.

$$P = \{\{1,2\}, \{3\}, \{4\}\}$$ can be mapped to R = {(1,1),(1,2),(2,1),(2,2),(3,3),(4,4))}