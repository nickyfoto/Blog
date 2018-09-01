---
layout: post
title:  "Special Elements in Posets"
date:   2018-08-30
author: "Huang Qiang"
tags: [discrete math]
comments: true
---

### Least Greatest Minimal Maximal Element

Let $$(A;\preceq)$$ be a poset. let $$S \subseteq A$$ be some subset of $$A$$.

Hasse diagram is

```
   t
  / \
 /   \
s     r
 \   /
  \ /
   q
   |
   |
   p
```
$$p$$ is the least element and $$t$$ is the greatest element.


The below Hasse diagram, only have greatest element, no least element.

```
   w
   |
   |
   x
  / \
 /   \
y     z
```
Formal definition $$a \in S$$ is the least(greatest) element of $$S$$ if $$a \preceq b\ (a \succeq b)$$ for all $$b \in S$$. Greatest, least are unique if they exist.

In the following Hasse diagram

```
g   f   e
 \  |  /
  \ | /
    d
    |
    |
    c
   / \
  /   \
 a     b
```

Minimal elements {a, b}, maximal elements {e, f, g}

Formal definition: $$a \in S$$ is a minimal(maximal) element of $$S$$ if there exists no $$b \in S$$ with $$b \prec a\ (b \succ a)$$.

### Lower Bound, Upper Bound, LUB and GLB

```
    A                     S
    
36      24
  \    /
   \  /
    12                   12
    |                     |
    |                     |
    6                     6
   / \
  /   \
 2     3
```

LB: {2, 3, 6}, UB: {36, 24, 12}

GLB: 6, LUB: 12

Formal definition: $$a \in A$$ in the _lower (upper) bound_ of $$S$$ if $$a \preceq b\ (a \succeq b)$$ for all $$b \in S$$.

$$a \in A$$ is the _greatest lower bound (least upper bound)_ of $$S$$ if $$a$$ is the greatest(least) element of the set of all lower (upper) bounds of S. No guarantee that there's always GLB or LUB, if there is, it's unique.

LUB is also called infimum and GLB are also called supremum.

Another example:

```
    A              S
    
    24
   /  \
  /    \
 12     8       12    8  
  \    /         \   /
   \  /           \ /
    4              4
    |
    |
    2 
```

LB: {2, 4}
UP: {24}


```
    A                     S1           S2             S3      
    
36      24                                          36      24
  \    /                                              \    /
   \  /                                                \  /
    12                   12            6                12  
    |                     |           / \                |
    |                     |          /   \               |
    6                     6         2     3              6
   / \
  /   \
 2     3
  \   /
   \ /
    1
```

Poset | Subset | LB  | UP  | GLB | LUP
----- | ------ | --- | --- | --- | ---
A     | S1     | {1,2,3,6}  | {12, 24, 36} | 6 | 12
A     | S2     | {1} | {6,12,24,36} | 1 | 6
A     | S3     | {1,2,3,6} | $$\emptyset$$ | 6 | -

<br>

Example: 

```
  e
 / \
d   |
|   |
b   c
 \ /
  a
```

Poset | Subset | LB  | UP  | GLB | LUP
----- | ------ | --- | --- | --- | ---
ex    | {d,c}  | {a} | {e} | a   | e

