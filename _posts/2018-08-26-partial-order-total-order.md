---
layout: post
title:  "Partial order | Total order"
date:   2018-08-26
author: "Huang Qiang"
tags: [discrete math]
comments: true
---

A partial order is defined as a relation which is

- Antisymmetric
- Reflexive
- Transitive

e.g. $$A = \{1, 2, 3, 4\}$$, where $$xRy$$ if $$x$$ divides $$y$$

$$R = \{(1,1),(1,2),(1,3),(1,4),(2,2),(2,4),(3,3),(4,4)\}$$

Diagram can be draw as:

![](../images/partial_order.png)

if $$A = \{1, 2, 4, 8\}$$, using the same relation

$$R = \{(1,1),(1,2),(1,4),(1,8),(2,2),(2,4),(2,8),(4,4),(4,8),(8,8)\}$$

If any two elements of a poset $$(A; \preceq)$$ are comparable, then A is called _totally ordered_ (or linearly ordered) by $$\preceq$$.
 
![](../images/total_order_pre_hasse.png)

![](../images/total_order_hasse.png)

https://www.youtube.com/watch?v=R36F8CWAi2k