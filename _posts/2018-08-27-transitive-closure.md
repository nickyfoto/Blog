---
layout: post
title:  "Transitive Closure"
date:   2018-08-27
author: "Huang Qiang"
tags: [discrete math]
comments: true
---

Imagine four people on twitter, Alice, Bob, Chuck, Dan, their following relation can be represented as:

```
{(Alice, Bob), # Alice follows Bob
(Bob, Chuck), 
(Dan, Alice)}
```

Since Alice follows Bob, and Bob follows Chuck, twitter may suggest that Alice follows Chuck, so Dan should follow Bob.

The above relation is not transitive, in order to make it transitive, we should add `(Alice, Chuck)` and `(Dan, Bob)`. Since Bob is already follow Chuck, so we also should add `(Dan, Chuck)`. The result is:

```
{(Alice, Bob),
(Bob, Chuck), 
(Alice, Chuck),
(Dan, Alice),
(Dan, Bob),
(Dan, Chuck)}
```

This also contains the original relation as a subset. This forms a transitive closure.

Formal definition:

Given a relation $$r$$, on a set $$A$$, the transitive closure of $$r$$ is the smallest transitive relation that contains $$r$$ as a subset. Often denoted by $$r*$$.

A Theorem how to compute transitive relation is that:

Let $$r$$ be a relation on $$A$$ and let $$r*$$ be the transitive closure, Then $$(a,b) \in r*$$ if and only if 

https://www.youtube.com/watch?v=OO8Jfs9uZnc