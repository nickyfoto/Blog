---
layout: post
title:  "Property of Relations"
date:   2018-08-29
author: "Huang Qiang"
tags: [discrete math]
comments: true
---

$$A = \{1, 2, 3\}$$

R | reflexive | irreflexive | symmetric | antisymmetric | asymmetric | transitive
------------ | ------------- | --- | --- |--- | --- | ---
$$\phi$$ | ❎ | ✅ | ✅ | ✅ | ✅ | ✅
$$A \times A$$ | ✅ | ❎ | ✅ | ❎ | ❎ | ✅
{(1,1),(2,2),(3,3)} | ✅ | ❎ | ✅ | ✅ | ❎ | ✅
{(1,2),(2,1),(1,1)} | ❎ | ❎ | ✅ | ❎ | ❎ | ✅
{(1,2),(2,1),(1,1),(2,2)}| ❎ | ❎ | ✅ | ❎ | ❎ | ✅ 
{(1,2),(2,3),(1,3)}| ❎ | ✅ | ❎ | ✅ | ✅ | ✅
{(2,3),(1,3),(1,1)}| ❎ | ❎ | ❎ | ✅ | ❎ | ✅
{(3,1),(1,3),(2,3)} |❎ | ✅ | ❎ | ❎ | ❎ | ❎
{(2,1),(2,3),(1,1)}| ❎ | ❎ | ❎ | ✅ | ❎ | ✅
{(2,3),(3,2),(2,2),(3,3)}| ❎ | ❎ | ✅ | ❎ | ❎ | ✅
{(1,1),(2,2),(2,3),(1,3)} | ❎ | ❎ | ❎ | ✅ | ❎ |✅
{(1,2),(2,1),(2,3)} | ❎ | ✅ | ❎ | ❎ | ❎ | ❎
{(1,1),(2,1),(1,2),(2,3)} | ❎ | ❎ | ❎ | ❎ | ❎ | ✅
{(1,2),(1,3)} | ❎ | ✅ | ❎ | ✅ | ✅ | ✅
{(2, 3)} | ❎ | ✅ | ❎ | ✅ | ✅ | ✅
{(1,2),(2,1)} | ❎ | ✅ | ✅ | ❎ | ❎ | ❎

reflexive: all diagonal element must be present

irreflexive: non diagonal element can present

symmetric: if $$(a, b) \in R$$, $$(b, a)$$ must $$\in R$$, $$a $$ can $$=b $$

antisymmetric: if $$(a, b) \in R$$ and $$(b, a) \in R$$, then $$a$$ must $$=b$$.

asymmetric: if $$(a, b) \in R$$, then $$(b, a) \not\in R$$, even if $$a = b$$. 

transitive: if $$(a, b) \in R$$ and $$(b, c) \in R$$, then $$(a, c)$$ must $$\in R$$