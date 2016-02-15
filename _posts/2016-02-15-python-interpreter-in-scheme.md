---
layout: post
title:  "用scheme实现一个python解释器"
date:   2016-02-15
author: "Huang Qiang"
tags: [61as, interpreter, scheme, python]
---

前面我们用scheme实现了一个scheme自身的解释器。让我们了解到了解释器的基本原理。利用同样方法，我们可以实现一个python的解释器[^1]。因为python不能像处理数据那样处理代码[^2]，所以parse起来要比scheme麻烦许多[^3]。

[^1]: python解释器是用C实现的
[^2]: [https://www.zhihu.com/question/19643954](https://www.zhihu.com/question/19643954)
[^3]: [谈语法](http://www.yinwang.org/blog-cn/2013/03/08/on-syntax)