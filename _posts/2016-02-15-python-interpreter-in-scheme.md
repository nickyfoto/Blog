---
layout: post
title:  "用scheme实现一个python解释器"
date:   2016-02-15
author: "Huang Qiang"
tags: [61as, interpreter, scheme, python]
---

前面我们用scheme实现了一个scheme自身的解释器。让我们了解到了解释器的基本原理。利用同样方法，我们可以实现一个python的解释器[^1]。因为python不能像处理数据那样处理代码[^2]，所以parse起来要比scheme麻烦许多[^3]。

## THE TOKENIZER

`parser.scm`文件中有个`PY-READ`procedure就是做这件事情。它调用`READ-CHAR`，返回a list of the corresponding tokens，最前面是indentation的数字，例如"if a == 3:" 返回"(0 if a == 3 :)"。同时，因为`PY-READ`返回scheme list，scheme中的特殊字符，例如括号，逗号，要用pipe符号区别出来。所以"(a + b) * 3"，最终得到"(0 |(| a + b |)| * 3)"。

### THE GET-TOKENS PROCEDURE

`PY-READ`中的主要工作由`GET-TOKENS`来完成，它读入characters，把它们group成tokens。它用`PEEK-CHAR` procedure来看输入Schython prompt的下一个字符，并且**不processing它**，因此，这个character还可以再被使用。这是它与`READ-CHAR`不同的地方。

```
STk> (read-char)123
#\1
STk> 23
STk> (peek-char)123
#\1
STk> 123
```

想想为什么要这样做。当你在检查输入的时候，你要向前多看一个character来决定current token是不是结束，新的是不是开始。在编译器里面这叫"LALR(1)-parser"，从左向右多看一个字符。`PEEK-CHAR`和`READ-CHAR`返回的都是characters。`GET-TOKENS`通过`CHAR->SYMBOL`来把character转换成symbol，`WORD` procedure用来把scheme的symbols转换成token，`CONS`把它们组成list。

注意，我们这里的**characters**，指的是`READ-CHAR`和`PEEK-CHAR`从prompt中读到的；scheme的**symbol**，是scheme能够理解的numbers和words。我们要把characters转换成symbols，这样scheme才能理解，因为最终要构建list，由scheme来eval。Characters在scheme中用#\<char>来表示，例如"a"在Scheme中表示为"#\a"，没有引号。

`GET-TOKENS`会一直读取characters直到下列两个条件之一得到满足：

1. 我们读到一行的尽头，所有的括号，braces，brackets都被正确的关闭。如果至少有一个没有关闭，那么换行符被忽略，procedure继续读characters。换行符由character #\newline表示。
2. 如果读的是文件，我们读到了文件的尽头。每一个文件由EOF character (Ctrl+D)来结束。它对Scheme的predicate EOF-OBJECT?返回#t。

上述两个条件中的任意一个得到满足，GET-TOKENS返回一个包涵tokens的scheme list。GET-TOKENS还有一个argument叫做`BRACES`，它来keep track of open parentheses, braces, or brackets，所以procedure可以知道括号是否被正确的关闭。

### AUGMENTING THE TOKENIZER*

1. 识别python单行，comment #

  ```
  STk> (py-read)x = 3 # I am a comment IGNORE ME
     (0 x = 3)

  STk> (py-read)x = 3 # Om # nom # nom
     (0 x = 3)
  ```

  (py-read)->(get-indent-and-tokens)->(cons 0 (get-tokens '()))->(peek-char)
  
  * 如果(peek-char)是newline，并且braces不为空->(begin (read-char) (get-tokens braces))

      如果brace是空->(begin (read-char) '())

  * 如果(peek-char)是eof-object，并且braces不为空->报错，end of file inside expressions

      如果brace是空->'()
      
  * 如果(peek-char)是#\space，(read-char) (get-tokens braces)
  * 如果(peek-char)是#\#，(ignore-comment) '()

2. 识别缩进

  ```
  STk> (py-read)x = 3
     (0 x = 3)

  STk> (py-read) x = 3
     (1 x = 3)

  STk> (py-read)   x = 3
     (3 x = 3)
  ```
3. 识别string

  ```
  STk> (py-read) print "Hello 'world'."
     (1 print "Hello 'world'.")

  STk> (py-read) print 'Hello 'world'.'
     (1 print "Hello " world ".")

  ("Hello ", world, and "." are three different tokens.)

  STk> (py-read) print 'Hello "world".'
     (1 print "Hello \"world\".")
  ```

4. 识别real numbers

  ```
  STk> (py-read) print 3.14.foo
     (1 print 3.14 .foo)

  STk> (py-read) print 3.14
     (1 print 3.14)

  STk> (py-read) print 3.14 + 5.15
     (1 print 3.14 + 5.15)
  ```

### THE LINE-OBJ CLASS

到这里，我们已经把所有的tokens转换成了Scheme的symbols或者strings。现在需要来eval这些token。在`driver-loop` procedure中有一行


`(let ((line-obj (make-line-obj (py-read))))`

它调用`make-line-obj`以及`PY-READ`返回的list of tokens，创建一个`LINE-OBJ`的object。`LINE-OBJ`被定义在"parser.scm"的顶部。一个`LINE-OBJ` object由一个list of tokens（还有这个list的indentation）实例化，得出本质上与之相对的"interactive"的list of tokens，让我们来process。每一个`LINE-OBJ` object接受下列messages：

```
    EMPTY?         Is the line of tokens empty?
    EXIT?          Is the line a command to exit?  ("exit()" or
                   "quit()")
    PEEK           What is the next token in the line of tokens?
    PUSH <token>   Push the token to the front of the line of tokens.
    NEXT           Return the next token in the line of tokens.
```
`PEEK`和`NEXT`与`PEEK-CHAR`，`READ-CHAR`类似，前者返回the token at the front of the line of tokens，后者返回`并且移除`这个token。

我们需要`LINE-OBJ`这个class本质上是因为python的infix operators。这在scheme里没有问题。但是在python里，针对"2 + 3"，我们只有remove了第一个token (2)，才发现我们要把它与第二个token (3)相加。理论上我们可以CDR这个list of tokens，但是这样当遇到"square(3 + 4) * 2"的输入的时候，我们需要先eval"3 + 4"才能call `square`这个function。`LINE-OBJ`为我们对list of tokens的remove from，peek at和add操作提供了一个clean interface，不用去考虑细节。你会在Schython的interpreter中看到相当多的`LINE-OBJ` instances。

## THE EVALUATOR

继续看REPL，我们会看到

`(py-print (eval-line line-obj the-global-environment))`

`eval-line`像你想的一样，evaluates the line of tokens（它被包裹成一个 `LINE-OBJ` object，后文称之为"line object"）。对于再高一层的抽象，我们知道这些就够了。

```scheme
(define (eval-line line-obj env)
  (if (ask line-obj 'empty?)
      *NONE*
      (if (zero? (ask line-obj 'indentation))
	  (let ((val (py-eval line-obj env)))
	    (if (not (ask line-obj 'empty?))
		(py-error "SyntaxError: multiple statements on one line")
		val))
	  (py-error "IndentationError: unexpected indent"))))
```

既然我们要修改`eval-line`，就先来看一下它的定义。它做基本的error check。只有在line of tokens不空的，并且0缩进时，并且只有一个Schython statement的时候。它调用PY-EVAL来eval the line。本质上来说，`eval-line` procedure确保`py-eval`收到的input没有syntactic errors.

```scheme
;; Starts the infix/item evaluator loop
(define (py-eval line-obj env)
  (handle-infix (eval-item line-obj env) line-obj env))
```

`py-eval`调用了另外两个procedure，`eval-item`和`handle-infix`。`eval-item`跟`mc-eval`很像。看第一个token，检查关键词；基于这个关键词，决定返回什么。因为python的infix特性，EVAL-ITEM processes as many tokens that constitute the "first operand" of an infix operator as it needs to. 结束之后，`eval-item`返回它的output给`handle-infix`。此时，如果line object不为空，line object的第一个token**可能**是个infix的operator：如果是，`handle-infix`再次用`py-eval`（或者`eval-item`）来eval line object infix operator后剩下的部分（或者是next item），最后把infix operator与左右的operands组合在一起，返回最后的结果。

总结一下，`eval-line`procedure用基本没有语法错误的line object做参数，调用`py-eval`。`py-eval`用`eval-item`来evaluate第一个（左边）operand of an infix operator，如果需要，把它结果传递给`handle-infix`。如果line object有一个infix operator，`handle-infix`用`py-eval`或者`eval-item`来evaluate第二个（右边）的operand，然后组合它们的结果。如名字所示，`eval-item`evalutates line object的下一个"item"，而`py-eval`evaluates整个line object。

注意，line object一直在这几个procedure间传来传去。而且line object有state，所以，要记得在evaluator任意一点line object是（可以是）什么。

### THE PY-OBJ CLASS

`eval-item`最终返回什么呢？返回的是`PY-OBJ`或者它的subclasses的实例。它定义在"py-primitves.scm"中。每一个Schython支持的数据"type"有一个PY-OBJ的subclass与之相对应。

* primitive procedures (PY-PRIMITIVE)
* user-defined procedures (PY-PROC)
* strings (PY-STRING)
* integers (PY-INT)
* real numbers (or "floats") (PY-FLOAT)
* Booleans (PY-BOOL)
* lists (PY-LIST)

如果我们在`eval-item`发现了一个number，我们创造一个`PY-NUM` object来"wrap"这个number。procedure也是一样。

为什么要这样做呢？只是为了模拟Python的**everything is an object**，包括numbers和strings。例如python下的

```
     >>> a = 3.5 + 4

it internally converts it to the equivalent statement

     >>> a = 3.5.__add__(4)
```
这里number"3.5"被转换成了一个object，它可以接受method`__add__`，在OOP-Scheme中对应的是`(ask 3.5 __add__ 4)`。尽管这在scheme里行不通，因为3.5是number，不是object。所以我们需要把number 3.5 "wrap"成一个PY-INT object，这样它就能调用`__add__`method了。（这就是为什么在B3中'3.14159.foo'不是一个错误，因为他是调用一个叫做foo的procedure。

这也使得infix operator可以用于多种类型。

```
     >>> a = "foo" + "bar"
     foobar

Since Python internally converts the statement to the equivalent

     >>> a = "foo".__add__("bar")
```
同样，"*"也能对应string，因为string也有一个`__mul__`method：

```
     >>> print 3 * 4
     12

     >>> print "foo" * 4
     foofoofoofoo
```

这个概念就是python中一切都是对象的概念。这让我们的代码得到了简化，尽管infix operator带来了些困难。"py-primitives"提供了一些helper constructor，例如MAKE-PY-STRING, MAKE-PY-BOOL, and MAKE-PY-INT来创造这些object。我们还提供了两个全局的true和false PY-BOOL objects，称作`PY-TRUE`和`PY-FALSE`，project中的任何一个file都可以access它们。

注意，一般来说，Schython中，PY-OBJ和它的subclass的methods返回的是PY-OBJ或者它sublcasses的instance。例外的情况是：

- "Predicate" methods, 以问号结尾的methods，返回的是scheme的booleans #t and #f
- 'type' method, 返回一个scheme word，这个PY-OBJ instance的type
- 'val' method, 返回被PY-OBJ instance "wrapped" Scheme value

这些例外可以让python object与scheme 的objects和primitives做互动。

最后，我们说一下Python的'None' object。在scheme中没有对应。如果一个function没有返回值，则它返回None object。这在一个procedure有时候返回值，有时候不返回任何东西的时候有用。但是为了保持一致性，所以让这个function返回None。所以在"py-primitives"里，我们定义了a NONE class来代表the 'None' object; the NONE class is a subclass of the PY-OBJ class. 我们还定义了一个 global object
called *NONE*, which is an instance of the NONE class. 练习：

## BLOCKS

python blocks在schython内部用第一个word是\*BLOCK*的lists表示。目前schython可以理解procedure definition。让我们来看下schython是怎么做的，这样可以看出 a pattern in evaluating a statement that uses blocks。

1. 首先要辨认procedure definition。'def' token代表我们要定义一个procedure。`eval-item`里加一个`DEF?`的条件句。
2. 然后我们从line object和prompt中读入procedure definition和body。然后我们用`MAKE-DEF-BLOCK`构建一个新的abstract data type，"DEF-block"。内部，"DEF-block"是一个有4个elements的list：the word `*BLOCK*`， the word `*DEF-BLOCK*`，一个由name和parameters组成的参数，以及body。The DEF-BLOCK-NAME, DEF-BLOCK-PARAMS, and DEF-BLOCK-BODY selectors allow us to extract different sections of a "DEF-block".
3. 我们的到"DEF-block"之后，我们把它粘在line object的前面，返回`py-eval`后的line object。第二次经过`eval-item`时，新的"DEF-block"满足`BLOCK?`检测，交由`eval-block`来处理，它去掉`*BLOCK*`tag，把它交给`eval-def-block`来处理。（这让你回忆起data-directed programming）。
4. `eval-def-block`用`make-py-proc`最终创造一个PY-PROC object，用`define-variable!`来把它保存到定义的Python procedure的env中。

我们用这些步骤作为一个总的指导来implementing statements with blocks in Schython。基本上，`eval-item`运行两次，第一次，收集用户输入，把用户输入package成"DEF-block"，然后把"DEF-block"贴到line object之前。第二次，我们侦测到新的"DEF-block"，eval它。

### while loop

user-input -> `eval-item` -> (while? token) -> make-while-block -> (ask line-obj 'push (make-while-block)) -> (py-eval line-obj env) -> (block? token) -> (eval-block token env) -> (while-block?) -> (eval-while-block block env)



[^1]: python解释器是用C实现的
[^2]: [https://www.zhihu.com/question/19643954](https://www.zhihu.com/question/19643954)
[^3]: [谈语法](http://www.yinwang.org/blog-cn/2013/03/08/on-syntax)