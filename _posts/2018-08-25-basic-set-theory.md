---
layout: post
title:  "Basic Set Theory"
date:   2018-08-25
author: "Huang Qiang"
tags: [discrete math, set theory]
comments: true
---

M = {a,b,c}, N = {a}

$$M \subseteq M$$

$$a \not\subseteq N$$

$$a \in N$$

$$P = \{\{a, b\}, c, d\}$$

$$\{a, b\} \not\subseteq P$$

$$\{\{a, b\}\} \subseteq P$$

$$\{a, b\} \in P$$

$$|\emptyset| = 0$$

$$|\{\emptyset\}| = 1$$

$$\emptyset \not= \{\emptyset\}$$

$$\emptyset \not\subseteq \{\emptyset\}$$

$$\emptyset \in \{\emptyset\}$$

$$|\mathcal{P}(A)| = 2^{|A|}$$

S = {1}

$$\mathcal{P}(S) = \{\emptyset, \{1\}\}$$

$$\mathcal{P}(S)\ \cap S = \emptyset$$

$$\mathcal{P}(\mathcal{P}(S)) = \{\emptyset, \{1\}, \{\emptyset\}, \{\emptyset, \{1\}\}\}$$

Set difference

$$B\ \backslash\ A = \{x \in B\ |\ x \not\in A\}$$

Compliment

$$\bar A  = \{x \in U\ |\ x \not\in A\}$$

Symmetric Difference

$$A \Delta B = \{x\ |\ (x \in A \vee x \in B)\ \wedge x \not\in (A \cap B)\}$$