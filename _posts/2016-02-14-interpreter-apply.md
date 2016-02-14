---
layout: post
title:  "61AS Unit 4 解释器的实现 apply"
date:   2016-02-14
author: "Huang Qiang"
tags: [61as, interpreter, sicp, scheme]
---

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

要应用一个基本过程，就是将实现过程应用于实际参数。

```scheme
(define (primitive-procedure? proc)
  (tagged-list? proc 'primitive))

(define (primitive-implementation proc) (cadr proc))

(define (apply-primitive-procedure proc args)
  (apply-in-underlying-scheme
   (primitive-implementation proc) args))
```

### 对环境的操作

求值器需要对环境的操作。环境就是a sequence of frames。每个框架都是a table of bindings that associate variables with their corresponding values. 我们针对环境有下列操作：

