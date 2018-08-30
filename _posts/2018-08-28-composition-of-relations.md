---
layout: post
title:  "Composition of Relations"
date:   2018-08-28
author: "Huang Qiang"
tags: [discrete math]
comments: true
---

Find $$S \circ R$$ if $$R = \{(1,1),(1,2),(2,5),(3,4)\}$$ and $$S = \{(1,6),(2,1),(4,0)\}$$

$$S \circ R = \{(1,6),(1,1),(3,0)\}$$

because (1,1)[1] == (1,6)[0], we get ((1,1)[0], (1,6)[1]) == (1,6)

because (1,2)[1] == (2,1)[0], we get ((1,2)[0], (2,1)[1]) == (1,1)

because (3,4)[1] == (4,0)[0], we get ((3,4)[0], (4,0)[1]) == (3,0)

In general, Let $$A, B, C$$ be sets. Let $$R$$ be a relation from $$A$$ to $$B$$, and let $$S$$ be a relation from $$B$$ to $$C$$, The composition of $$R$$ and $$S$$, denoted by $$S \circ R$$, is the relation from $$A$$ to $$C$$ given by

$$S \circ R =\{(a,c) \in A \times C: (\exists b \in B)[(a,b) \in R \land (b,c) \in S]\}$$