---
layout: post
title:  "Ruby Method, Class and Modules"
date:   2016-02-26
author: "Huang Qiang"
tags: [ruby]
comments: true
---

## Method

* Everything (except fixnums) is pass-by-reference

- a.b means: call method b on object a
  - a is the **receiver** to which you **send** the method call assumming a will **respond to** that method

* **does not mean:** b is an instance variable of a
* **does not mean:** a is some kind of data structure that has b as a member

```ruby
# my_str.length => my_str.send(:length)
>> my_str = "abc"
=> "abc"
>> my_str.send(:length)
=> 3

>> y = 3 + 5
=> 8
>> y = [1, 2] + ['foo', :bar]
=> [1, 2, "foo", :bar]
>> y = 'hello ' + 'world'
=> "hello world"
```
加号分别对应的是 Numeric#+, Array#+, String#+

* send is a language primitive
* 可以看做语法糖
* object可以看做receiver，只要它对message有反应就可以

```ruby
def over5?(value)
    return 'Exactly 5' if value == 5 # return和if配合可以不放在method末尾
    value > 5 ? 'over 5' : 'not over 5'
end

puts over5?(6)
>> over 5
```

如果return多个值，则返回一个array，可以用多重赋值接住返回结果

```ruby
def add_and_sub(n1, n2)
    add = n1 + n2
    sub = n1 - n2
    return add, sub
end

a, b = add_and_sub(1, 2)
puts a # 3
puts b # -1
puts add_and_sub(3, 4).inspect #[7,-1]
```

### Objectifying methods

Methods aren't exempt from Ruby's "everything is an object" rule. All objects in Ruby expose the eponymous `method` method that can be used to get hold of any of its methods as an object.

```ruby
>> 1.next
=> 2
>> 1.method('next')
=> #<Method: Fixnum(Integer)#next>
>> 1.method('next').call
=> 2
```

### Naming parameters

Ruby makes this possible by allowing the last parameter in the parameter list to skip using curly braces if it's a hash, making for a much prettier method invocation. That's why we default the `options` to `{}` - because if it isn't passed, it should be an empty `Hash`.

```ruby
def add(a_number, another_number, options = {})
  sum = a_number + another_number
  sum = sum.abs if options[:absolute]
  sum = sum.round(options[:precision]) if options[:round]
  sum
end

puts add(1.0134, -5.568)
puts add(1.0134, -5.568, absolute: true)
puts add(1.0134, -5.568, absolute: true, round: true, precision: 2)

-4.5546
4.5546
4.55
```

```ruby
def add(*numbers)
  numbers.inject(0) { |sum, number| sum + number }  
end

def subtract(*numbers)
  current_result = numbers.shift
  numbers.inject(current_result) { |current_result, number| current_result - number }  
end

def calculate(*arguments)
  # if the last argument is a Hash, extract it 
  # otherwise create an empty Hash
  options = arguments[-1].is_a?(Hash) ? arguments.pop : {}
  options[:add] = true if options.empty?
  return add(*arguments) if options[:add]
  return subtract(*arguments) if options[:subtract]
end
```

### Syntactic Suger

可以改写一个class的method从而利用这些语法糖使代码更清晰好看

Suger          | Vinegar | message passing
-------------- | --------|---
8 - 2          | 8.-(2)  | 8.send(:-, 2)
8 * 2          | 8.\*(2) | 8.send(:*, 2)
8 / 2          | 8./(2)  | 8.send(:/, 2)
8 ** 2         | 8.**(2) | 8.send(:**, 2)
array << 4     |array.<<(4) |array.send(:<<, 4)
array[2]       |array.\[](2) | array.send(:[], 2)
array[2] = 'x' |array.[]=(2,'x')|array.send(:[]=, 2, 'x')
if(x == 3) | if x.==(3) | if (x.send(:==, 3))
my_func(z) | | self.send(:my_func, z)

When I call up a regular function, I'm sending to self which is the implicit receiver. We're sending self the name of a method, `my_func`, and one argument that's expected by that method. The hope is that self has an instance method called `my_func`, in this case, `my_func` is prepared to accept an argument.

## Class

name convention: UpperCamelCase

```ruby
class Animal
    def make_noice
        "Miao!"
    end
end

cat = Animal.new
puts cat.make_noice # Miao!

p cat.is_a?(Animal) #true
p cat.kind_of?(Animal) #true
```

利用语法糖写getter and setter

```ruby
class Animal
    attr_accessor :name # 与下面method等同
    def noise=(noise)
        @noise = noise
    end

    def noise
        @noise
    end
end

cat = Animal.new
cat.noise = "Miao!"
cat.name = "ABC"
puts cat.noise # Miao
puts cat.name # ABC

class Animal
    attr_accessor :noise, :legs, :arms
    def initialize(noise, legs, arms)
        @noise = noise
        @legs = legs
        @arms = arms
    end
end

cat = Animal.new("Miao", 4, 0)
puts cat.noise # Miao
puts cat.legs # 4
puts cat.arms # 0
```

### classmethod

Class methods do not have access to instance methods or instance variables. However instance methods can access both class methods and class variables.

ruby classmethod的`self`，指代class；instance method里`self`指代instance

```ruby
class Animal
    @@total_animal = 0
    @@species = []
    def self.total_animal
        @@total_animal
    end
    def self.species
        @@species
    end
    def initialize
        @@total_animal += 1
        @@species << self
    end
end

a1 = Animal.new
a2 = Animal.new
puts Animal.total_animal # 2
puts Animal.species.inspect 
# [#<Animal:0x007fe67b083b58>, #<Animal:0x007fe67b083b30>]
```

### 继承

可以用关键词super取父类method的结果再进行处理

```ruby
class Animal
    attr_accessor :noise, :legs, :arms
    def initialize(noise, legs=4, arms=0)
        @noise = noise
        @legs = legs
        @arms = arms
    end
    def color
        "The color is black"
    end
end

class Cow < Animal
    def color
        "The cow's color is yellow"
    end

    def noise
        "Cow's notice is " + super
    end
end

maisie = Cow.new("Moo!")
puts maisie.noise # Cow's notice is Moo!
puts maisie.color # The cow's color is yellow

puts 1.0.is_a?(Float) #true
puts 1.0.is_a?(Numeric) #true
puts 1.0.class # Float
puts 1.0.class.superclass # Numeric

# Class#superclass is an instance method on Class, not on object
puts Float.superclass    # Numeric
puts Numeric.superclass  # Object
puts Object.superclass   # BasicObject
puts BasicObject.superclass #nil
```

`BasicObject`是一个空的class，它没有superclass了。

### Redefining, overriding, and super

#### Redefining method

打开一个class，把它已有的method重新定义，叫做redifine，这会引起意想不到的效果，特别是language本身提供的class，所以永远不要这样做。

#### Overriding

写一个新的class，继承上一级的class之后，可以对method进行overriding，而且原来的method还可以用super来调用。

```ruby
class Foo
  attr_accessor :num
  def initialize(num)
    @num = num
  end

  def myadd(sth)
    self.num + sth
  end
end

class Bar < Foo
  def myadd(sth)
    super + sth
  end
end

bar = Bar.new(1)
p bar  #<Bar:0x007fd7e9a29f68 @num=1>
p bar.myadd(2) #5

# 如果当前类与super类的同名method参数不同，
# 可以指明传递给super的参数，否则程序会报错。

class Foobar < Foo
  def myadd(sth, other)
    super(sth) + other
  end
end

foobar = Foobar.new(1)
p foobar #<Foobar:0x007fd8db949330 @num=1>
p foobar.myadd(2, 3) # 6
```

### Class Variable

class variable在继承之后会被改写

```ruby
class ApplicationConfiguration
  @@configuration = {}

  def self.set(property, value)
    @@configuration[property] = value
  end

  def self.get(property)
    @@configuration[property]
  end
end

class ERPApplicationConfiguration < ApplicationConfiguration
end

class WebApplicationConfiguration < ApplicationConfiguration
end

ERPApplicationConfiguration.set("name", "ERP Application")
WebApplicationConfiguration.set("name", "Web Application")

p ERPApplicationConfiguration.get("name")
p WebApplicationConfiguration.get("name")

p ApplicationConfiguration.get("name")
```

解决办法是用class instance variable

```ruby
class Foo
  @foo_count = 0
  
  def self.increment_counter
    @foo_count += 1
  end
  
  def self.current_count
    @foo_count
  end  
end

class Bar < Foo
  @foo_count = 100
end

Foo.increment_counter
Bar.increment_counter
p Foo.current_count # 1
p Bar.current_count # 101
```

1. Instance variables are available only for instances of a class. They look like `@name`. Class variables are available to both class methods and instance methods. They look like `@@name`
2. It is almost always a bad idea to use a class variable to store state. There are only a very few valid use cases where class variables are the right choice.
3. Prefer class instance variables over class variables when you do really need store data at a class level. Class instance variables use the same notation as that of an instance variable. But unlike instance variables, you declare them inside the class definition directly.
4. instance method have no access to class instance variable

### Equality of Objects

```ruby
class Item
    attr_reader :item_name, :qty
    
    def initialize(item_name, qty)
        @item_name = item_name
        @qty = qty
    end
    def to_s
        "Item (#{@item_name}, #{@qty})"
    end
    def ==(other_item)
      # your code here
      @item_name == other_item.item_name && @qty == other_item.qty
    end
end

p Item.new("abcd",1)  == Item.new("abcd",1)
p Item.new("abcd",2)  == Item.new("abcd",1)
```

`==`也是method，可以重新定义。`uniq` method使用的是`eql?`做比较，所以仅仅定义`==`并不影响它。要改变它，需要重新定义`hash`和`eql?`两个instance method。[祥见](https://rubymonk.com/learning/books/4-ruby-primer-ascent/chapters/45-more-classes/lessons/105-equality_of_objects)

### puts and p, to_s and inspect

`puts` generally prints the result of applying `to_s` on an object while `p` prints the result of `inspect`ing the object.

```ruby
class Item
  def initialize(item_name, qty)
    @item_name = item_name
    @qty = qty
  end
end

item = Item.new("a",1)

puts item #<Item:0x007fb2bb952bb0>
p item #<Item:0x007fb2bb952bb0 @item_name="a", @qty=1>
```

## Modules

* Modules are wrappers around Ruby code
* Module can't be instanciated
* Modues are used in conjunction with classes

```ruby
module WarmUp
end

puts WarmUp.class      # Module
puts Class.superclass   # Module
puts Module.superclass  # Object
```

用module的两个原因

### 1. namespaces
   
好比同样文件名存放在不同文件夹下才可以被正确调用。例如约会网站有一个class叫Date，会跟Ruby自带的Date class混淆

```ruby
class Date # 约会的类
end
  
dinner = Date.new # 想作为约会的instance
dinner.date = Date.new # 晚餐日期
puts dinner.class # Date

# 正确做法
module Romantic
    class Date
    end
end
  
dinner = Romantic::Date.new
dinner.date = Date.new # 晚餐日期
puts dinner.class # Romantic::Date
```

更常见的情况是我们引入了不同的library，会出现相同的class名以及method名，例如

```ruby
# class Push
#   def up
#     40
#   end
# end
require "gym" # up returns 40
gym_push = Push.new
p gym_push.up # 40

# class Push
#   def up
#     30
#   end
# end
require "dojo" # up returns 30
dojo_push = Push.new
p dojo_push.up # 30
p gym_push.up # 30
```

所以正确的做法应该是把类都包含在module里：

```ruby
# module Gym
#   class Push
#     def up
#       40
#     end
#   end
# end
require "gym"

# module Dojo
#   class Push
#     def up
#       30
#     end
#   end
# end
require "dojo"

dojo_push = Dojo::Push.new
p dojo_push.up # 40

gym_push = Gym::Push.new
p gym_push.up # 30
```

`::`前面没有内容，会取到最顶端的变量值

```ruby
module Kata
  A = 5
  module Dojo
    B = 9
    A = 7
    
    class ScopeIn
      def push
        ::A
      end
    end
  end
end

A = 10

puts Kata::Dojo::ScopeIn.new.push # 10
```

### 2. Mix-ins

利用module可以实现多重继承

```ruby
class A; include MyModule; end
```

- `A.foo` will search A, then `MyModule`, then `method_missing` in A, then A's ancestor

```ruby
module ContactInfo
    attr_accessor :first_name, :last_name
    def full_name
        return @first_name + " " +@last_name
    end
end

class School
    def address
        "New York"
    end
end

class Teacher < School
    include ContactInfo
end
p = Teacher.new
p.first_name = "John"
p.last_name = "Smith"
puts p.full_name # John Smith
puts p.address   # New York
```

#### included callback

如果module里面有`included` method，当这个module被引入到其它class时，它会被executed。可以用来查看哪个class引入了这个module。例如：

```ruby
module Foo
  def self.included(klass)
    puts "Foo has been included in class #{klass}"
  end
end

class Bar
  include Foo
end

# Foo has been included in class Bar
```

但是使用`include`引入module，只能用于class的instance的method，不能作为class本身的method。如果使用`self.extend`，则class本身可以调用这个method。如果instance需要调用，则需要instance先extend这个module，这样class和instance可以同时使用这个method。

However, include has a limitation: it can only add instance level methods - not class level methods. Lets look at an example:

include只能把引入的method用于instance level，无法用于class level。

```ruby
module Foo
  def self.module_level_method
    puts "Module level method"
  end
end

class Bar
  include Foo
end

Bar.module_level_method # 报错
```

这时可以使用`extend`

```ruby
module Foo
  def module_method
    puts "Module Method invoked"
  end
end

class Bar
  self.extend Foo
end

# instance level
bar=Bar.new
bar.extend Foo
bar.module_method # Module Method invoked

# class level
Bar.module_method # Module Method invoked
```

当然，你也可以在initialize的时候extend这个module，这样每个instance都可以调用这个method。虽然这可以模拟include，但是include能解决问题的时候就用include，这是ruby默认的方式。

```ruby
class Bar
  # include Foo
  self.extend Foo

  def initialize
    self.extend Foo
  end
end
```

当然，还可以都用

```ruby
class Bar
  include Foo
  self.extend Foo
end
```

与included一样，extended也有一个callback来看谁extend了这个module

更多例子

```ruby
module Perimeter
  def perimeter
    sides.inject(0) { |sum, side| sum + side }
  end
end

class Rectangle
  # Your code here
  include Perimeter
  
  def initialize(length, breadth)
    @length = length
    @breadth = breadth
  end

  def sides
    [@length, @breadth, @length, @breadth]
  end
end

class Square
  # Your code here
  include Perimeter
  
  def initialize(side)
    @side = side
  end

  def sides
    [@side, @side, @side, @side]
  end
end

Rectangle should include Perimeter ✔
Square should include Perimeter ✔
Perimeter should have a method named `perimeter` ✔
Rectangle should have `perimeter` included into it and not define its own version ✔
Square should have `perimeter` included into it and not define its own version ✔
Rectangle.new(2, 3).perimeter returns 10 ✔
Rectangle.new(5, 10).perimeter returns 30 ✔
Square.new(5).perimeter returns 20 ✔
Square.new(15).perimeter returns 60 ✔
Rectangle#perimeter should in turn call Rectangle#sides ✔
Square#perimeter should in turn call Square#sides ✔
```

### 使用习惯

* Modules are usually kept in separate files
* Modules files can serve as code libraries

### load modules into Ruby

* require

  * require之前要给明查找文件的路径，例如
  
  ```ruby
	APP_ROOT = File.dirname(__FILE__)
	$:.unshift(File.join(APP_ROOT, 'lib'))
	```
	
	`$:` 是个特殊变量，它是一个ruby require搜索变量path的array
  
  * 只load一次，这样避免重复load
* include
  * 只用在把module作为mix-ins的时候使用
* load
  * module在另外的文件要先load
  * 每次出现都要load，因此优先使用require

### Extend modules into Ruby Class

- [Extend](http://stackoverflow.com/questions/156362/what-is-the-difference-between-include-and-extend-in-ruby)

### Enumerable as a mix-in

有了它，我们可以方便地enumerate instance。例如：

```ruby
class ToDoList
    attr_accessor :items
    def initialize
        @items = []
    end
end

list = ToDoList.new
list.items = ['laundry', 'dished', 'vacuum']
puts list.items.select {|i| i.length > 6} # laundry

puts list.select {|i| i.length > 6}
# in `<main>': private method `select' called for
# #<ToDoList:0x007f878a050118> (NoMethodError)
```

我们可以选择这个list的item做select，但是如果我们对这个object本身做select，则会报错，因为我们没有定义一个叫做select的method。只要我们在class中`include Enumerable`，定义你想要iter的`each`，就可以实现`list.select`的功能。

```ruby
class ToDoList
    include Enumerable
    attr_accessor :items

    def initialize
        @items = []
    end

    def each
        @items.each {|item| yield item}
    end
end

list = ToDoList.new
list.items = ['laundry', 'dished', 'vacuum']

puts list.select {|i| i.length > 6} # laundry
```