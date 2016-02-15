---
layout: post
title:  "61AS Unit 4 解释器的实现 apply"
date:   2016-02-14
author: "Huang Qiang"
tags: [61as, interpreter, sicp, scheme]
---

## Apply的实现

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

`apply`把过程分为两类：

* **基本过程**：调用`apply-primitive-procedure`去应用基本过程
* **复合过程**：需要对组成改procedure的expressions顺序的求值`eval-sequence`。求值过程中需要建立相应的环境，环境的构造方式是扩充该过程所携带的基本环境，并加入一个框架，其中将过程的各个形式参数（parameters of the procedure）约束于过程调用的实际参数（arguments）。

### 复合过程的处理

```scheme
(define (make-procedure parameters body env)
  (list 'procedure parameters body env))
  
;from eval
((lambda? exp)
    (make-procedure (lambda-parameters exp)
      (lambda-body exp)
      env))
      
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

还记得复合过程是怎么来的吗？当我们eval到lambda表达式的时候吧它的params、body和env一起做成一个`compound-procedure`，apply遇到`compound-procedure`调用`eval-sequence`把它求值。`eval-sequence`不断地把expressions `eval`成更小的exp，根据不同情况返回value。


### 基本过程的处理

我们的求值器最终要把表达式归约到基本过程的应用。所以每个基本过程名必须由一个binding，这样eval求值一个基本过程的运算符时，可以找到相应的对象，并将这个对象传给`apply`。所以必须建立一个初始环境，把基本过程的名字和唯一对象相关联。在处理expression的时候可能遇到这些名字。在这一全局环境里还要包涵对true和false的binding，这样它们可以作为变量用在被求值的expression里。

```scheme
(define primitive-procedures
  (list (list 'car car)
        (list 'cdr cdr)
        (list 'cons cons)
        (list 'null? null?)
        (list 'cadr cadr)
      	(list '+ +)
      	(list '- -)
      	(list '* *)
      	(list '/ /)
      	(list '= =)
      	(list 'list list)
      	(list 'append append)
      	(list 'equal? equal?)
      	(list 'integer? integer?)
      	(list 'number? number?)
      	(list 'list? list?)
      	(list 'pair? pair?)
      	(list 'not not)
      	(list 'list-ref list-ref)
        (list 'assoc assoc)
;;      more primitives
  ))
```

这个初始环境（setup-environment）将从一个list里取得基本过程的名字，以及它们的实现过程。这里的名字不一定要于lisp系统里的名字相同，这里之所以相同是因为我们用scheme解释scheme，如果用scheme解释python，就可以不同了。

```scheme
(define (primitive-procedure-names)
  (map car
       primitive-procedures))

(define (primitive-procedure-objects)
  (map (lambda (proc) (list 'primitive (cadr proc)))
       primitive-procedures))       
```

用`map car`取得它们的name list `primitive-procedure-names`。注意`primitive-procedures`是一个变量，而`primitive-procedure-names`是一个没有参数的过程。`primitive-procedure-objects`同样也是一个过程，它把`primitive-procedures`里面具体的proc取出来，前面加上`'primitive`，组成一个list。

这两个过程都将交给extend-environment去调用。

### extend-environment

环境就是一个框架的序列。每个框架都是一个约束的表格。约束就是把变量和它对应的值关联在一起。

An environment is a sequence of frames, where each frame is a table of bindings that associate variables with their corresponding values.

我们在eval部分看到的`lookup-variable-value`，`define-variable!`，`set-variable-value!`都是对环境的操作。

```scheme
(define the-empty-environment '())
(define (first-frame env) (car env))
(define (enclosing-environment env) (cdr env))
```
为了实现这些操作，我们讲环境定义为一个框架的list。外围`enclosing-environment`就是cdr，空就是'()。

环境里的每个frame都是a pair of lists: a list of the variables bound in that frame and a list of the associated values.

```scheme
(define (make-frame variables values)
  (cons variables values))
(define (frame-variables frame) (car frame))
(define (frame-values frame) (cdr frame))
(define (add-binding-to-frame! var val frame)
  (set-car! frame (cons var (car frame)))
  (set-cdr! frame (cons val (cdr frame))))
```
要用（关联了变量和值的）框架去扩充一个环境，我们让框架由一个variable list和 value list组成，并结合到环境里。如果变量和值的个数不匹配，就报错。

```scheme
(define (extend-environment vars vals base-env)
  (if (= (length vars) (length vals))
      (cons (make-frame vars vals) base-env)
      (if (< (length vars) (length vals))
          (error "Too many arguments supplied" vars vals)
          (error "Too few arguments supplied" vars vals))))
```

### setup-environment

在setup-environment调用extend-environment来扩充初始环境。

```scheme
(define (setup-environment)
  (let ((initial-env
         (extend-environment (primitive-procedure-names)
                             (primitive-procedure-objects)
                             the-empty-environment)))
    (define-variable! 'true true initial-env)
    (define-variable! 'false false initial-env)
    (define-variable! 'import
                      (list 'primitive
			    (lambda (name)
			      (define-variable! name
				                (list 'primitive (eval name))
				                the-global-environment)))
                      initial-env)
    initial-env))
(define the-global-environment (setup-environment))    
```
extend-environment带了三个参数，变量name list，它对应的proc list，以及需要扩充的环境。

```scheme
(define apply-in-underlying-scheme apply)

(define (primitive-procedure? proc)
  (tagged-list? proc 'primitive))

(define (primitive-implementation proc) (cadr proc))

(define (apply-primitive-procedure proc args)
  (apply-in-underlying-scheme
   (primitive-implementation proc) args))
```
注意，这里的`apply-in-underlying-scheme`如名称所示，是scheme自身的apply。而不是我们自定义的`mc-apply`。 
