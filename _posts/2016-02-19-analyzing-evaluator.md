---
layout: post
title:  "当我们define一个函数的时候到底define了什么"
date:   2016-02-19
author: "Huang Qiang"
tags: [61as, interpreter, sicp, scheme]
---

前面我们讲了eval和apply相互配合，形成一个简单的Metacircular Evaluator。但是，把语法分析和它们的execution交织在一起，让这个解释器的效率很低，如果一个program要executed很多次，他的语法分析也要做很多次。

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
当我们定义一个求阶乘的函数时

```scheme
(define (fact num) 
  (if (= num 0)
      1
      (* num (fact (- num 1)))))
      
definition? ==> #t
eval-definition exp env
  define-variable!
    var:= definition-variable exp  ==> 'fact
    val:= (mc-eval (definition-value exp) env):= 
    		    (mc-eval '(lambda (num) (if (= num 0) 1 (* num (fact (- num 1))))) the-global-environment)
    		      lambda? == #t
    		        (make-procedure '(num) '((if (= num 0) 1 (* num (fact (- num 1))))) the-global-environment)
    		          => (list 'procedure '(num) '((if (= num 0) 1 (* num (fact (- num 1))))) the-global-environment)
    env:= the-global-environment
```
通过这三个参数，`define-varialble!`把这个binding添加到env当中。然后，当我们计算(fact 3)时，先要判断`(fact 3)`属于`eval`中的哪种情况。它是`application?`，然后递归地`eval`他的`operator`，`fact`，它是`variable`，然后`lookup-variable-value`，得出它是一个`<procedure fact>`；然后`list-of-values (3)`。

得到这些之后`apply <procedure fact> (3)`，它是`compound-procedure?`，进入`eval-sequence`；通过`procedure-body`的到`(if (= num 0)...)`，`eval-sequence`需要`eval`它的`first-exp`，也就是`if`。然后用`eval-if`来取它的值，这需要`eval` if的pred`(= num 0)`，再eval它的operator，`＝`，`＝`是`variable？`于是找出它的value。apply它的到#t or #f。如果是#f，需要eval它的alternative，这时出现`(fact (- num 1))`，这一切的一切再重新上演一遍。

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
        procedure-body (list 'procedure '(num) '...) ==> (if (= num 0)...)
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

那我们看一下新的办法是如何做的：

```scheme
(define (fact num) 
  (if (= num 0)
      1
      (* num (fact (- num 1)))))
```
这里我们把这个exp简称为d：

```
(mc-eval d env)
  ((analyze d) env)
  definition? ==> #t
    ((analyze-definition d) env)
```
`analyze-definition`会首先analyze `definition-value`，然后创建一个execution procedure, 当它被executed时候，会把变量名define到`definition-value`上。

这非常重要，我们不仅仅是把lambda assigning到 fact上，我们assigning 一个analyzed lambda到fact上。这在后面会带来performance boon。

要确定fact的value，我们用`analyze-lambda`来analyze the lambda。

analyze-lambda带来的boom，就是只analyzing the body 一次，把它造成一个a procedure Abstract Data Type with an analyzed body (a scheme procedure)。而不是像原来eval做的，只是造a simple list of instructions。

这样做的好处是，invocation我们的lambda的时候，我们不用再parsing了。Parsing只在创建这个lambda的时候做。

```scheme 
    ((analyze-definition d) env)
    ==>(lambda (env) (define-varialble! var (vproc env) env)
    vproc:= (analyze (definition-value exp)))
```
definition-value的定义：

```scheme
(define (definition-value exp)
  (if (symbol? (cadr exp))
      (caddr exp)
      (make-lambda (cdadr exp)
                   (cddr exp))))
```
```scheme
(definition-value d)==>'(lambda (num) (if (= num 0) 1 (* num (fact (- num 1)))))

(analyze (definition-value d)):=
  (analyze-lambda '(lambda (num) (if (= num 0) 1 (* num (fact (- num 1)))))
```
`analyze-lambda`返回的是`(lambda (env) (make-procedure vars bproc env)))`。env进来之后，make-procedure开始工作

```scheme
(make-procedure vars bproc env)
(bproc (analyze-sequence (lambda-body d)))

(lambda-body d)==>'(if (= num 0) 1 (* num (fact (- num 1)))
```
`analyze-sequence`的定义：

```scheme
(define (analyze-sequence exps)
  (define (sequentially proc1 proc2)
    (lambda (env) (proc1 env) (proc2 env)))
  (define (loop first-proc rest-procs)
    (if (null? rest-procs)
        first-proc
        (loop (sequentially first-proc (car rest-procs))
              (cdr rest-procs))))
  (let ((procs (map analyze exps)))
    (if (null? procs)
        (error "Empty sequence -- ANALYZE"))
    (loop (car procs) (cdr procs))))
```
`analyze-sequence`会调用`(map analyze exps)`在`'(if (= num 0) 1 (* num (fact (- num 1)))`

```
(analyze-if '(if (= num 0) 1 (* num (fact (- num 1)))))   
```
`analyze-if`的定义：

```scheme
(define (analyze-if exp)
  (let ((pproc (analyze (if-predicate exp)))
        (cproc (analyze (if-consequent exp)))
        (aproc (analyze (if-alternative exp))))
    (lambda (env)
      (if (true? (pproc env))
          (cproc env)
          (aproc env)))))
```
`analyze-if`会analyze if表达式的pred，consequence和alternative。返回一个接受env的lambda procedure。最终define-varialble!把这个procedure与'fact binding到env中。

```scheme
 if-pred := (analyze '(= num 1)')
  ; this is the execution procedure: (lambda (env)
  ;                                    (execute-application (analyzed/= env)

  if-true := (analyze '1')
  ; this is the execution procedure: (lambda (env) 1)

  if-false := (analyze '(* (fact (- num 1)) num)')
  ; this is too long to write out, but it's
  ; kind of like if-pred

  ;;this is the execution procedure we return:
  ;;let's call this execution procedure 'analyzed-fact-if'
  (lambda (env)
    (if (true? (if-pred env))
        (if-true env)
        (if-false env)))
```
由此我们可以看出，original版本的evaluator做binding时，只是记住它的text和它所创建的env。调用的时候再去`eval-sequence`，`eval-sequence`需要调用`mc-eval`对每一个exp求值。

而analyze的版本，在define的时候，就把procedure的body analyze了（用`analyze-sequence`）。在储存的时候不是存储这个procedure的text，evaluator把它转换成一个分析过的procedure in underlying Scheme，把它，以及它的formal parameter，the defining environment一起与名称做绑定。

做个测试：

original：

```
;;; M-Eval input:
(define (fact num)
  (if (= num 0)
      1
      (* num (fact (- num 1)))))

;;; M-Eval value:
ok

;;; M-Eval input:
fact

;;; M-Eval value:
(compound-procedure (num) ((if (= num 0) 1 (* num (fact (- num 1))))) <procedure-env>)
```
analyze：

```;;; A-Eval input:
(define (fact num)
  (if (= num 0)
      1
      (* num (fact (- num 1)))))

;;; A-Eval value:
ok

;;; A-Eval input:
fact

;;; A-Eval value:
(compound-procedure (num) #[closure arglist=(env) 183d9d4] <procedure-env>)
```
表达式的语法分析是编译器的一个很大的组成部分。在某种程度上，analyzing evaluator就是编译器。与这个结构类似的编译器叫做`recursive descent compiler` 。如今，大部分的编译器用的是stack machine，因为它可以automate the writing of a parser。如果人工写parser，那么recursive descent更容易。

新的调用流程：

在analyze evaluator下，如果跟之前一样，调用(fact 3)，流程是：

```
(eval '(fact 3) env)
((analyze '(fact 3)) env)
application? ==> #t
((analyze-application '(fact 3)) env);返回一个lambda procedure
(lambda (env)
  (execute-application (fproc env)
                       (map (lambda (aproc) (aproc env))
                            aprocs)))
环境带入后
  (execute-application (fproc env)
                       (map (lambda (aproc) (aproc env))
                            aprocs))
	  (fproc env):= (analyze (operator exp)):= (analyze 'fact):= 
	     analyze-variable:= (lambda (env) (lookup-variable-value exp env))
	  ==>(lambda (env)
			  (if (true? (pproc env))
				   (cproc env)
				   (aproc env)))  
	  aprocs:= (map analyze (operands exp)):=(map analyze '(3))
	  ==> (lambda (env) '(3))
	  (map (lambda (aproc) (aproc env)) aprocs)
	  ==> '(3)
  (execute-application 
    (lambda (env)
        	  (if (true? (pproc env))
	   	          (cproc env)
		          (aproc env))) 
	 '(3)
	 
	 compound-procedure? ==> #t
	 (procedure-body proc)
          (extend-environment (procedure-parameters proc)
                              args
                              (procedure-environment proc))
    把环境填入，pproc ==> #f
    走aproc '(* (fact (- n 1)) n)即
    (analyze '(* (fact (- n 1)) n))
    application? ==> #t
    (analyze-application '(* (fact (- n 1)) n))
    返回一个(lambda (env)
            (execute-application (fproc env)
                       (map (lambda (aproc) (aproc env))
                            aprocs)))
                            

```
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





