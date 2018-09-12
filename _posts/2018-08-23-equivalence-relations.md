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

### Equivalence class

Let $$\mathbb{Z}_n$$ be the set of equivalence classes of the integers mod $$n$$ and $$a,b,c \in \mathbb{Z}_n$$. We have:

1. Addition and multiplication are commutative:

	$$a+b \equiv b+a \pmod n$$

	$$ab \equiv ba \pmod n$$

2. Addition and multiplication are associative:

	$$(a+b)+c \equiv a+(b+c) \pmod n$$
	
	$$(ab)c \equiv a(bc) \pmod n$$
	
3. There are both additive and multiplicative identities:

	$$a+0 \equiv a \pmod n$$
	
	$$a \cdot 1 \equiv a \pmod n$$
	
4. Multiplication distributes over addition:

	$$a(b+c) \equiv ab+ac \pmod n$$
	
5. For every integer $$a$$, there is an additive inverse $$-a$$:

	$$a+(-a) \equiv \pmod n$$
	
6. Let $$a$$ be a nonzero integer. Then $$gcd(a,n)=1$$ if and only if there exists a multiplicative inverse $$b$$ for $$a \pmod n$$; that is a nonzero integer $$b$$ such that

	$$ab \equiv 1 \pmod n$$