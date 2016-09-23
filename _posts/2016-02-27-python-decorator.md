---
layout: post
title:  "Python Decorator"
date:   2016-02-25
author: "Huang Qiang"
tags: [python]
comments: true
---

首先我们来看一下python class的staticmethod和classmethod。他们是不需要instance的类中的函数，如果没有静态方法，用户只能在全局名字空间中创建函数，并在其中使用类对象来操作类或者类属性。例如

```py
class TestStaticMethod:
    def foo():
        print 'calling static method foo()'

print TestStaticMethod.foo
<unbound method TestStaticMethod.foo>

TestStaticMethod.foo()
# TypeError: unbound method foo() must be called with TestStaticMethod
# instance as first argument (got nothing instead)

TestStaticMethod().foo()
# TypeError: foo() takes no arguments (1 given)
```
当我们想在全局空间调用这个类中定义的方法时，都会报错。因为默认只有instance可以调用类中的方法。当我们试图用instance调用时，又发现这个方法不带任何参数。

只要我们在这个方法上方加上@staticmethod，就可以调用了。

```py
class TestStaticMethod:
    @staticmethod
    def foo():
        print 'calling static method foo()'

print TestStaticMethod.foo()
print TestStaticMethod().foo()
# calling static method foo()
# calling static method foo()
```

classmethod也是一样，当我们直接调用时，因为不是类的instance而报错。用实例调用时，实例又没有class的`__name__`属性，所以还是报错。

```py
class TestClassMethod:
    def foo(cls):
        print 'calling class method foo()'
        print 'foo() is part of class:', cls.__name__

TestClassMethod.foo()
# TypeError: unbound method foo() must be called with TestClassMethod
# instance as first argument (got nothing instead)
TestClassMethod().foo()
# AttributeError: TestClassMethod instance has no attribute '__name__'
```
加上@classmethod之后，调用成功

```py
class TestClassMethod:
    @classmethod
    def foo(cls):
        print 'calling class method foo()'
        print 'foo() is part of class:', cls.__name__

TestClassMethod().foo()
>> calling class method foo()
>> foo() is part of class: TestClassMethod
TestClassMethod.foo()
>> calling class method foo()
>> foo() is part of class: TestClassMethod
```

（未完待续）