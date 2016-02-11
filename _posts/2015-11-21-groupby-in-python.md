---
layout: post
title:  "Python groupby 用法"
date:   2015-11-21
author: "Huang Qiang"
tags: [python]
---

例子

```python
from itertools import groupby

lst = [5, 4, 4, 4, 5, 5, 2, 5]

for n, l in groupby(lst):
	print n, list(l)
```
得到

```
5 [5]
4 [4, 4, 4]
5 [5, 5]
2 [2]
5 [5]
```

---

如果

```python
for n, l in groupby(sorted(lst)):
	print n, list(l)
```
得到

```python
2 [2]
4 [4, 4, 4]
5 [5, 5, 5, 5]
```