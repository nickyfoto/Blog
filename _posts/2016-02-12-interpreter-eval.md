---
layout: post
title:  "61AS Unit 4 解释器的实现 eval"
date:   2016-02-12
author: "Huang Qiang"
tags: [61as, interpreter, sicp, scheme]
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

首先碰到的是`self-evaluating?`。只有number和string可以self-evaluate。`number?`和`string?`解释器中都没有定义。

```scheme
(define (variable? exp) (symbol? exp)) 
```

如果是variable，则要在env中lookup它的值。`symbol?`解释器也没有定义。

```scheme
(define (lookup-variable-value var env)
  (define (env-loop env)
    (define (scan vars vals)
      (cond ((null? vars)
             (env-loop (enclosing-environment env)))
            ((eq? var (car vars))
             (car vals))
            (else (scan (cdr vars) (cdr vals)))))
    (if (eq? env the-empty-environment)
        (error "Unbound variable" var)
        (let ((frame (first-frame env)))
          (scan (frame-variables frame)
                (frame-values frame)))))
  (env-loop env)) 
```

要在一个环境中查找一个变量，就需要扫描第一个框架里的变量表。如果在这里找到了所需的变量，那么就返回与之对应的值表里的对应元素。如果当前框架找不到，就要到它外围环境里去找，并继续下去。如果遇到了空环境，则发出一个“未约束变量”的错误信号。

---

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

利用`tagged-list?`可以处理关键词`quote`，`set!`， `define`， `if`， `lambda`， `begin`， `cond`。

#### Special Form: Assignments and Definitions

现在我们来看下赋值的形式。抓`set!`。

```scheme
(define (assignment? exp)
  (tagged-list? exp 'set!))
(define (assignment-variable exp) (cadr exp))
(define (assignment-value exp) (caddr exp))
```

`define` 有两种形式

* `(define <var> <value>)` 赋值
* ```(define (<var> <parameter1> ... <parametern>) <body>)``` 定义procedure

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

赋值和定义变量都是把变量和得到的值传递给`set-variable-value!`和`define-variable!`，由这两个procedure把相关的值安置到环境里。

```scheme
(define (set-variable-value! var val env)
  (define (env-loop env)
    (define (scan vars vals)
      (cond ((null? vars)
             (env-loop (enclosing-environment env)))
            ((eq? var (car vars))
             (set-car! vals val))
            (else (scan (cdr vars) (cdr vals)))))
    (if (eq? env the-empty-environment)
        (error "Unbound variable -- SET!" var)
        (let ((frame (first-frame env)))
          (scan (frame-variables frame)
                (frame-values frame)))))
  (env-loop env))  
```

在需要为某个变量在给定的环境里设置一个新值时，我们需要扫描这个变量，跟过程lookup-variable-value一样，找到这一变量后修改它的值。

```scheme
(define (define-variable! var val env)
  (let ((frame (first-frame env)))
    (define (scan vars vals)
      (cond ((null? vars)
             (add-binding-to-frame! var val frame))
            ((eq? var (car vars))
             (set-car! vals val))
            (else (scan (cdr vars) (cdr vals)))))
    (scan (frame-variables frame)
          (frame-values frame))))
```

要定义一个变量，我们需要在第一个框架里查找该变量的约束（binding）。如果找到，就对其进行修改（跟`set-variable-value!`一样）。如果不存在，就在第一个框架中加入`add-binding-to-frame!`这个约束（binding）。



#### Special Form: Lambda

```scheme
(define (make-procedure parameters body env)
  (list 'procedure parameters body env))

(define (lambda? exp) (tagged-list? exp 'lambda))
(define (lambda-parameters exp) (cadr exp))
(define (lambda-body exp) (cddr exp))
```

提取这个lambda的body和参数。然后加上关键词`procedure`交由`compound-procedure?`处理。

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

* `begin` 把多个expression放到一个expression里面。它要求按出现顺序对它的子expression求值。`begin-actions` 取出begin后的exps。
* `Eval-sequence`递归地对包含在`begin`里的exps求值，如果递归到`last-exp?`则返回它的value。

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

`eval-if`把含有if的exp拆成三部分。首先对if expression的predicate部分进行求值。如果true，则对consequence求值，否则对alternative求值。`if-predicate` 用于被解释的语言，求出来的值用`true?`来判断。然后交由解释器本身的if来判断执行。这里体现了解释器所用的语言和被解释的语言之间的关系。

```scheme

(define (sequence->exp seq)
  (cond ((null? seq) seq)
        ((last-exp? seq) (first-exp seq))
        (else (make-begin seq))))
(define (make-begin seq) (cons 'begin seq))

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

至此special forms结束。

---

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

((application? exp)
    (apply (mc-eval (operator exp) env)
      (list-of-values (operands exp) env)))
```

如果exp既不是number，也不是string，也不是symbol，也不是其它的special form，那么它就是个pair?，这样的表达式被称为procedure application，或者叫做compound expression。对这样的表达式，我们先求出它的`operator`的value `(mc-eval (operator exp) env)`，再列出它的参数的值 `(list-of-values (operands exp) env))`，最后，通过apply来处理它。

---

[1]: http://yinwang0.lofter.com/post/183ec2_47bea8