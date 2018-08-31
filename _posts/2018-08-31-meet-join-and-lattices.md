---
layout: post
title:  "Meet, Join and Lattices"
date:   2018-08-31
author: "Huang Qiang"
tags: [discrete math]
comments: true
---

Example 1:

S8 = {1, 2, 3, 4}, factors of 8. Hasse diagram is

```
8
|
4
|
2
|
1
```

Poset | Subset | LB  | UP  | meet(GLB) | join(LUP) | lattice
----- | ------ | --- | --- | --- | --- | ---
S8 | {1,2}    | {1}  | {2, 4, 8} | 1 | 2 | ✅
S8 | {2,4}    | {1,2} | {4,8}    | 2 | 4 | ✅
S8 | {2,8}    | {1,2} | {8}      | 2 | 8 | ✅

Formal definition: Let $$(A;\preceq)$$ be a poset. If $$a$$ and $$b$$ (i.e. the set $$\{a,b\} \in A$$) have a GLB, then it is called the **meet** of $$a$$ and $$b$$, often denoted $$a \wedge b\ (a+b)$$. If $$a$$ and $$b$$ have a LUB, then it is called the **join** of $$a$$ and $$b$$, often denoted $$a \vee b\ (a \cdot b)$$.

A poset $$(A;\preceq)$$ in which every pair of elements has a meet and a join is called a **lattice**.

Example 2:

S6 = {1,2,3,6}, Hasse diagram is

```
  6
 / \
2   3
 \ /
  1
```
Poset | Subset | LB  | UP  | meet(GLB) | join(LUP) | lattice
----- | ------ | --- | --- | --- | --- | ---
S6 | {1,2}    | {1}  | {2, 6}    | 1 | 2 | ✅
S6 | {2,3}    | {1}  | {6}       | 1 | 6 | ✅

Example 3:

S24 = {1,2,3,4,6,8,12,24}, Hass diagram is

```
      24
     / \
    12  8
   / \ /
  6   4
 / \ /
3   2
 \ /
  1
```

S24 is a lattice.

Example 4:

$$S = \{a, b, c\}$$

```   
      {a,b,c}
     /   |   \
    /    |    \
 {a,b} {a,c} {b,c}
  \   \/   \/   /
   \  /\   /\  /
   {a}  {b}  {c}
     \   |   /
      \  |  /
         ∅
```
$$(\mathcal{P}(S), \subseteq)$$ is also a lattice.


Example 5:

``` 
36      24
  \    /
   \  /
    12                   
    |                     
    |                     
    6                     
   / \
  /   \
 2     3
```

Poset | Subset | LB  | UP  | meet(GLB) | join(LUP) | lattice
----- | ------ | --- | --- | --- | --- | ---
ex5   | {2,3}  | $$\emptyset$$  | {6,12,24,36}    | - | 6 | ❎
ex5   |{24,36} | {2,3,6,12} | $$\emptyset$$ | 12 | - | ❎

Example 6:

```
     e
    / \
   / c \
  / / \ \
 d       b
  \     /
   \   /
     a
```
Poset | Subset | LB  | UP  | meet(GLB) | join(LUP) | lattice
----- | ------ | --- | --- | --- | --- | ---
ex6   | {c,e}  | {d,b}  | $$\emptyset$$    | - | - | ❎



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