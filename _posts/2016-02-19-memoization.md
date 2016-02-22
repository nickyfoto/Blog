---
layout: post
title:  "Memoization"
date:   2016-02-19
author: "Huang Qiang"
tags: [61as, memoization, functional programming, python]
---

[Fib number][2]中fib(0)可以定义为0，也可以定义为1。为了避免歧义，我们这里统一定义为fib(0)=0。

之前我们在[600.2x week5][1]中提到过用memoization的办法提高程序运行效率。里面python代码是这样写的：

```py
def fib(n):
    if n == 0 or n == 1 : # fib(0) = 0; fib(1) = 1
      return n
    else:
      return fib(n-1) + fib(n-2) 
      
def fastFib(x, memo):
    assert type(x) == int and x >= 0 and type(memo) == dict
    if x == 0 or x == 1:
        return x
    if x in memo:
        return memo[x]
    result = fastFib(x-1, memo) + fastFib(x-2, memo)
    memo[x] = result
    return result
```

如果用函数式编程的写法，可以这样写：

```py
def make_fib_memos():
    fib_memos = {}
    def fib(x):
      if x in fib_memos:     #fibonacci(x) has already been computed, return this value
        return fib_memos[x]
      elif x <= 1:
        return x
      else:
        t = fib(x-1) + fib(x-2)
        fib_memos[x] = t
        return t
    return fib
```
我们通常这样调用python的function

```
>>> fib(10)
55
>>> fastFib(10, {})
55
```
但是在函数式编程风格下，我们需要这样写：

```
>>> make_fib_memos()(10)
55
```
直接调用`make_fib_memos()`返回的是一个内部定义的function `fib`，要调用这个返回值，就需要在它后面加上括号以及参数`(10)`。为了表达更清晰，我们把`make_fib_memos()`的call定义给一个新的变量，然后再调用它返回的`fib`。

```
>>> make_fib_memos()
<function fib at 0x1052e1f50>
>>> memo_fib = make_fib_memos()
>>> memo_fib(10)
55
```
这也与一般的python写法不同。

除了fib，我们还可以memoize factorial：

```py
def make_fact_memos():
    fact_memos = {}
    def fact(x):
        if x in fact_memos:    # we've already computed fact(x)
            return fact_memos[x]
        elif x <= 1:
            return 1            # fact(0) = fact(1) = 1
        else:
            t = x * fact(x-1)
            fact_memos[x] = t
            return t
    return fact
    
memo_fact = make_fact_memos()
print memo_fact(0) #prints 1
print memo_fact(2) #prints 2
print memo_fact(20) #prints 2432902008176640000
```

我们发现它和memo_fib的pattern很相似，于是可以做一层抽象：

```py
def __fib(x, memo):
    if x <= 1:
      return x
    else:
      return memo(x-1) + memo(x-2)

def __factorial(x, memo):
    if x <= 1:
      return 1
    else:
      return x * memo(x-1)

def memoize(func):
    d = {}
    def memo(num):
        if num in d:
            return d[num]
        else:
            v = func(num, memo)
            d[num] = v
        return v
    return memo


factorial = memoize(__factorial)
fib_memo = memoize(__fib)
print fib_memo(10) 	#55
print factorial(10)	#3628800
```

先把它们的算法分别定义，取值的时候访问memoize内部定义的memo，如果有，就直接取，如果没有，就计算后存入，并返回计算结果。

[1]: http://nickyfoto.github.io/Blog/entries/6-00-2x-notes-week5-part1
[2]: https://en.wikipedia.org/wiki/Fibonacci_number