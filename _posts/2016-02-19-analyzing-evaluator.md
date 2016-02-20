---
layout: post
title:  "61AS Unit 4 解释器的实现 analyzing evaluator"
date:   2016-02-19
author: "Huang Qiang"
tags: [61as, interpreter, sicp, scheme]
---

通过构建前面的eval和apply，我们完成了一个简单的Metacircular Evaluator。但是，把语法分析和它们的execution交织在一起，让这个解释器的效率很低，如果一个program要executed很多次，他的语法分析也要做很多次。

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
例如，我们计算(fact 3)

```scheme
(define (fact num) 
  (if (= num 0)
      1
      (* num (fact (- num 1)))))
```

先要判断`(fact 3)`属于`eval`中的哪种情况。它是`application?`，然后递归地`eval`他的`operator`，`fact`，它是`variable`，然后`lookup-variable-value`，得出它是一个`<procedure fact>`；然后`list-of-values (3)`。

得到这些之后`apply <procedure fact> (3)`，它是`compound-procedure?`，进入`eval-sequence`；通过`procedure-body`的到`(if (= num 0)...)`，`eval-sequence`需要`eval`它的`first-exp`，也就是`if`。

```scheme
eval (fact 3) 
  self-evaluating? ==> #f 
  variable? ==> #f
  quoted? ==> #f 
  assignment? definition?
  if? ==> #f
  lambda? ==> #f
  begin? ==> #f
  cond? ==> #f 
  application? ==> #t 
  eval fact
    self-evaluating? ==> #f
    variable? ==> #t
    lookup-variable-value ==> <procedure fact>
    list-of-values (3)
      eval3 ==> 3
    apply <procedure fact> (3)
      compound-procedure? ==> #t
      eval-sequence
        procedure-body (define (fact num)...) ==> (if (= num 0)...)
          eval (if (= num 0) ...) 
          self-evaluating? ==> #f 
          variable? ==> #f 
          quoted? ==> #f 
          assignment? ==> #f 
          definition? ==> #f
          if? ==> #t
            eval-if (if (= num 0) ...) 
              if-predicate ==> (= num 0)
                eval (= num 0)
                self-evaluating? ==> #f
                ...
            if-alternative ==> (* num (fact (- num 1)))  
              eval (* num (fact (- num 1)))
                self-evaluating? ==> #f
                ...
                list-of-values (num (fact (- num 1)))
                  ...
                  eval (fact (- num 1))
                    ...
                    apply <procedure fact> (2)
                      eval (if (= num 0) ...)      
```
evaluator要examine the procedure body四次，check它是否是if expression，把它分成pred，consequent和alternative，evalutate它们（还要确定它们每个部分分别是何种expression）。

这就是为何解释型语言比编译型语言慢许多的原因。解释器一遍又一遍的做着语法分析，而编译器只分析一次，被编译的程序就可以只做给定实际参数的那部分计算。所以我们现在要学习analyzing evaluator，看它如何避免重复地分析一个程序的语法。

## The Separation

eval有两个参数，exp和env。递归的时候，exp每次都一样，只是env不同。例如，当我们计算(fact 3)的时候，我们在num是3的env中eval fact的body。这个body包含了(fact 2)的计算，在计算它的时候，我们eval的body是一样的，只是此时env中num绑定了2。

我们计划在eval的process中，找到那些只依赖exp本身，不依赖env的部分，只做它们一次，做这个工作的过程被称为**analyze**。

**analyze**的结果是什么？它需要与env结合并返回一个value。所以analyze返回的是一个procedure，只取一个env作为argument，做剩下的evaluation。

所以，之前是

```scheme
(eval exp env) ==> value
```
现在是

```scheme
1. (analyze exp) ==> exp-procedure 
2. (exp-procedure env) ==> value
```
当我们eval同样的exp时，只需要重复第二步。我们将要做的事情与memoization很相似，memoization通过记住计算结果来避免重复。而这里，我们记住的是一个problem soluction的一部分，而非整个的solution。

我们可以这样来达到跟original的eval相同的效果：

```scheme
(define (eval exp env)
  ((analyze exp) env))
```

## Analyze

`analyze`与之前的`eval`有相似的结构

```scheme
(define (analyze exp)
  (cond ((self-evaluating? exp) 
         (analyze-self-evaluating exp))
        ((quoted? exp) (analyze-quoted exp))
        ((variable? exp) (analyze-variable exp))
        ((assignment? exp) (analyze-assignment exp))
        ((definition? exp) (analyze-definition exp))
        ((if? exp) (analyze-if exp))
        ((lambda? exp) (analyze-lambda exp))
        ((begin? exp) (analyze-sequence (begin-actions exp)))
        ((cond? exp) (analyze (cond->if exp)))
        ((application? exp) (analyze-application exp))
        (else
         (error "Unknown expression type -- ANALYZE" exp))))
```












