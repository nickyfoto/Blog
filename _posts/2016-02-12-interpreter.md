---
layout: post
title:  "61AS Unit 4 解释器的实现"
date:   2016-02-12
author: "Huang Qiang"
tags: [61as, interpreter, sicp]
---

>学习程序语言的最好方式就是实现一个程序语言。这并不需要一个完整的编译器，而只需要写一些简单的解释器，实现最基本的功能。之后你就会发现，所有语言的新特性你都大概知道可以如何实现，而不只停留在使用者的水平。

关于如何学习编程语言，这里有[一篇][1]参考。

## eval（求值器）的实现

表达式在环境中的求值被归约到过程对实际参数的应用，而这种应用又被归约到新的表达式在新的环境中的求值，直到下降到符号（其值可以在环境中找到）或者基本过程（它们可以直接应用）。

A basic cycle in which expressions to be evaluated in environments are reduced to procedures to be applied to arguments, which in turn are reduced to new expressions to be evaluated in new environments, and so on, until we get down to symbols, whose values are looked up in the environment, and to primitive procedures, which are applied directly.

既然有了基本过程(the ability to apply primitives)，为什么还要求值器？

1. 求值器使我们可以处理嵌套（deal with nested expressions）
2. 求值器使我们可以使用变量
3. 求值器使我们可以定义复合过程（compound procedures）
4. 求值器要求提供一批特殊形式 （special forms）求值方式与普通过程不同。



![](http://mitpress.mit.edu/sicp/full-text/sicp/book/chapter-4/figs/eval-apply.gif)

```scheme
(define (eval-1 exp)
  (cond ((constant? exp) exp)
        ((symbol? exp) (eval exp))      ; use underlying Scheme's EVAL                        
        ((quote-exp? exp) (cadr exp))
        ((if-exp? exp)
         (if (eval-1 (cadr exp))
             (eval-1 (caddr exp))
             (eval-1 (cadddr exp))))
        ((lambda-exp? exp) exp)
        ((pair? exp) (apply-1 (eval-1 (car exp))      ; eval the operator                     
                              (map eval-1 (cdr exp))))
        (else (error "bad expr: " exp))))
```
这是一段之前eval-1的实现，其中第三行用了scheme本身的`eval`，现在我们来看这个eval是如何实现的。

```scheme
(define (mc-eval exp env)
  (cond ((self-evaluating? exp) exp)
  ((variable? exp) (lookup-variable-value exp env))
  ((quoted? exp) (text-of-quotation exp))
  ((assignment? exp) (eval-assignment exp env))
  ((definition? exp) (eval-definition exp env))
  ((if? exp) (eval-if exp env))
  ((lambda? exp)
    (make-procedure (lambda-parameters exp)
      (lambda-body exp)
      env))
  ((begin? exp) 
  (eval-sequence (begin-actions exp) env))
  ((cond? exp) (mc-eval (cond->if exp) env))
  ((application? exp)
    (mc-apply (mc-eval (operator exp) env)
      (list-of-values (operands exp) env)))
  (else
    (error "Unknown expression type -- EVAL" exp))))
```

### Self-Evaluating Expressions

```scheme
(define (self-evaluating? exp) 
  (cond ((number? exp) true)
        ((string? exp) true)
        (else false)))      
```

首先碰到的是`self-evaluating?`。只有number和string可以self-evaluate。

```scheme
(define (variable? exp) (symbol? exp)) 
```

如果是variable，则要在env中lookup它的值。

### Special Forms

#### Special Forms: Sentences and Words

```scheme
(define (tagged-list? exp tag)
  (if (pair? exp)
      (eq? (car exp) tag)
      false))

(define (text-of-quotation exp) (cadr exp)) 
;returns just the text as a list that will print to output
;(quote <text-of-quotation>)

(define (quoted? exp)
  (tagged-list? exp 'quote))
```

利用`tagged-list?`可以处理parse一系列情况。

#### Special Form: Lambda

```scheme
(define (lambda? exp) (tagged-list? exp 'lambda))
(define (lambda-parameters exp) (cadr exp))
(define (lambda-body exp) (cddr exp))
```

提取这个lambda的body和参数。

#### Special Form: Sequences

```scheme
(define (begin? exp) (tagged-list? exp 'begin))
(define (begin-actions exp) (cdr exp))
(define (last-exp? seq) (null? (cdr seq)))
(define (first-exp seq) (car seq))
(define (rest-exps seq) (cdr seq))

(define (eval-sequence exps env)
  (cond ((last-exp? exps) (mc-eval (first-exp exps) env))
        (else (mc-eval (first-exp exps) env)
              (eval-sequence (rest-exps exps) env))))
```

* `Begin` 把多个expression放到一个expression里面。它要求按出现顺序对它的子expression求值。
* `Eval-sequence` is used by apply to evaluate the sequence of expressions in a procedure body. It is also used by `eval` to evaluate the sequence of expressions in a `begin` expression. 它拿 a sequence of expressions 和env做参数。返回最后一个exp的value。

#### Special Form: Conditionals

```scheme
(define (true? x)
  (not (eq? x false)))
(define (false? x)
  (eq? x false))
  
(define (if? exp) (tagged-list? exp 'if))
(define (if-predicate exp) (cadr exp))
(define (if-consequent exp) (caddr exp))
(define (if-alternative exp)
  (if (not (null? (cdddr exp)))
      (cadddr exp)
      'false))
  
(define (eval-if exp env)
  (if (true? (mc-eval (if-predicate exp) env))
      (eval (if-consequent exp) env)
      (eval (if-alternative exp) env)))
```

`eval-if`对if expression的predicate部分进行求值。如果true，则对consequence求值，否则对alternative求值。`if-predicate` 用于被解释的语言，求出来的值用`true?`来判断。然后交由解释器本身的if来判断执行。这里体现了解释器所用的语言和被解释的语言之间的关系。

```scheme
(define (make-if predicate consequent alternative)
  (list 'if predicate consequent alternative))
  
(define (cond? exp) (tagged-list? exp 'cond))
(define (cond-clauses exp) (cdr exp))
(define (cond-else-clause? clause)
  (eq? (cond-predicate clause) 'else))
(define (cond-predicate clause) (car clause))
(define (cond-actions clause) (cdr clause))
(define (cond->if exp)
  (expand-clauses (cond-clauses exp)))

(define (expand-clauses clauses)
  (if (null? clauses)
      'false                          ; no else clause
      (let ((first (car clauses))
            (rest (cdr clauses)))
        (if (cond-else-clause? first)
            (if (null? rest)
                (sequence->exp (cond-actions first))
                (error "ELSE clause isn't last -- COND->IF"
                       clauses))
            (make-if (cond-predicate first)
                     (sequence->exp (cond-actions first))
                     (expand-clauses rest))))))
```

`cond`被转换为`if`，这样减少了需要特别描述的求值过程的spacial form的数目。当然，这里还用到了`make－if`。通过语法变换（syntactic transformations）的方式实现的exp（例如cond）被称作**derived expressions**。`let`也是这样的。

#### Special Form: Assignments and Definitions

```scheme
(define (eval-assignment exp env)
  (set-variable-value! (assignment-variable exp)
                       (mc-eval (assignment-value exp) env)
                       env)
  'ok)
  
(define (eval-definition exp env)
  (define-variable! (definition-variable exp)
                    (mc-eval (definition-value exp) env)
                    env)
  'ok)  
```

这个procedure处理变量赋值。它调用eval，找出需要赋的值，将变量和得到的值传给set-variable-value!，将相关的值安置到指定的环境里。对变量的定义也一样。

```scheme
(define (assignment? exp)
  (tagged-list? exp 'set!))
(define (assignment-variable exp) (cadr exp))
(define (assignment-value exp) (caddr exp))
```

现在我们来看下赋值的形式。抓`set!`。

`define` 有两种形式

* `(define <var> <value>)` 赋值
* `(define (<var> <parameter1> ... <parametern>)
  <body>)` 定义procedure

后一种形式只是下面这种形式的语法糖。

`(define <var>
  (lambda (<parameter1> ... <parametern>)
    <body>))`

因此定义的过程是

```scheme
(define (definition? exp)
  (tagged-list? exp 'define))
(define (definition-variable exp)
  (if (symbol? (cadr exp))
      (cadr exp)
      (caadr exp)))
(define (definition-value exp)
  (if (symbol? (cadr exp))
      (caddr exp)
      (make-lambda (cdadr exp)   ; formal parameters
                   (cddr exp)))) ; body
```

以上对exp有不同的判断，最后一个是`application?`，也就是复合表达式（compound expression）时，需要分清operator和operands，然后(apply procedure arguments)，这里procedure就对应operator，arguments对应operands。所以`(list-of-values (operands exp) env)`用来生成参数表。


```scheme
(define (application? exp) (pair? exp))
(define (operator exp) (car exp))
(define (operands exp) (cdr exp))
(define (no-operands? ops) (null? ops))
(define (first-operand ops) (car ops))
(define (rest-operands ops) (cdr ops))

(define (list-of-values exps env)
  (if (no-operands? exps)
      '()
      (cons (mc-eval (first-operand exps) env)
        (list-of-values (rest-operands exps) env))))
```


## Apply的实现

apply把过程分为两类，一类调用apply-primitive-procedure去应用基本过程；符合过程要顺序的求值组成该过程体的表达式。求值过程中需要建立相应的环境，环境的构造方式是扩充该过程所携带的基本环境，并加入一个框架，其中将过程的各个形式参数约束于过程调用的实际参数。

```scheme
(define (apply procedure arguments)
  (cond ((primitive-procedure? procedure)
         (apply-primitive-procedure procedure arguments))
        ((compound-procedure? procedure)
         (eval-sequence
           (procedure-body procedure)
           (extend-environment
             (procedure-parameters procedure)
             arguments
             (procedure-environment procedure))))
        (else
         (error
          "Unknown procedure type -- APPLY" procedure))))
```

### 复合过程的处理

```scheme
(define (compound-procedure? p)
  (tagged-list? p 'procedure))

(define (eval-sequence exps env)
  (cond ((last-exp? exps) (mc-eval (first-exp exps) env))
        (else (mc-eval (first-exp exps) env)
              (eval-sequence (rest-exps exps) env))))
              
(define (procedure-body p) (caddr p))
(define (procedure-parameters p) (cadr p))
(define (procedure-environment p) (cadddr p))               
```

### 基本过程的处理

```scheme
(define (primitive-procedure? proc)
  (tagged-list? proc 'primitive))

(define (primitive-implementation proc) (cadr proc))

(define primitive-procedures
  (list (list 'car car)
        (list 'cdr cdr)
        (list 'cons cons)
        (list 'null? null?)
        <more primitives>
        ))

(define (primitive-procedure-names)
  (map car
       primitive-procedures))

(define (primitive-procedure-objects)
  (map (lambda (proc) (list 'primitive (cadr proc)))
       primitive-procedures))
```
要应用一个基本过程，就是将实现过程应用于实际参数。

```scheme
(define (apply-primitive-procedure proc args)
  (apply-in-underlying-scheme
   (primitive-implementation proc) args))
```

### 对环境的操作

求值器需要对环境的操作。环境就是a sequence of frames。每个框架都是a table of bindings that associate variables with their corresponding values. 我们针对环境有下列操作：

{% gist nickyfoto/5edce9213004aea5cfc6 %}



[1]: http://yinwang0.lofter.com/post/183ec2_47bea8