---
layout: post
title:  "Ruby Block and Lambda"
date:   2016-02-26
author: "Huang Qiang"
tags: [ruby]
comments: true
---

## Block

- Blocks are anonymous lambdas that carry their environment around with them
- They allow "sending code to where an object is" rather than passing an object to the code
- Iterators are an important special use case

### find/detect

```ruby
>> (1..10).find {|i| i % 3 == 0}
=> 3
# code block could include expression
>> (1..10).find {|i| (1..10).include?(i * 3)}
=> 1
# if not find return nil
```

### find_all/select

```ruby
>> (1..10).find_all {|i| i % 3 == 0}
=> [3, 6, 9]
>> (1..10).find_all {|i| i % 30 == 0}
=> []
```

### any?

```ruby
>> (1..10).any? {|i| i % 3 == 0}
=> true
# when Hash.any? candidate[0] is the key and candidate[1] is its value.
{:locke => 4, :hugo => 8}.any? { |candidate| candidate[1] > 4 } # true
# 或者
{:locke => 4, :hugo => 8}.any? { |candidate, number| number < 4 } # false
```

### all?

```ruby
>> (1..10).all? {|i| i % 3 == 0}
=> false
```

### none?

```ruby
>> ['a', 'b', 'c'].none? {|i| i == "Esau"}
=> true
```

### reject/delete_if

```ruby
# can't work with range, but with array
>> [*1..10].delete_if {|i| i % 3 == 0}
=> [1, 2, 4, 5, 7, 8, 10]
```

### merge

work only with hash

```ruby
>> h1 = {"a"=>111, "b"=>222}
=> {"a"=>111, "b"=>222}
>> h2 = {"b"=>333, "c"=>444}
=> {"b"=>333, "c"=>444}
>> h1.merge(h2)
=> {"a"=>111, "b"=>333, "c"=>444}
>> h2.merge(h1)
=> {"b"=>222, "c"=>444, "a"=>111}
```

当key有冲突的时候，我们可以引入code block进行控制。例如：

```ruby
>> h1.merge(h2) {|key, old, new| new }
=> {"a"=>111, "b"=>333, "c"=>444}

>> h1.merge(h2) {|key, old, new| old }
=> {"a"=>111, "b"=>222, "c"=>444}

# 还可以做复杂的code block
>> h1.merge(h2) do |key, old, new|
?>   if old < new
>>     old
>>   else
?>     new
>>   end
>> end
=> {"a"=>111, "b"=>222, "c"=>444}

# 用ternary operator
>> h1.merge(h2) {|k, o, n| o < n ? o : n}
=> {"a"=>111, "b"=>222, "c"=>444}
```

### collect/map

works best with Arrays, Hashes and Ranges and always returns an Array

```ruby
>> (1..5).map {|num| num * 2}
=> [2, 4, 6, 8, 10]

>> hash = { "a"=>111, "b"=>222, "c"=>333}
=> {"a"=>111, "b"=>222, "c"=>333}
>> hash.map {|k, v| k}
=> ["a", "b", "c"]
>> hash.map {|k, v| v * 20}
=> [2220, 4440, 6660]
>> hash.map {|k, v| "#{k}: #{v * 20}"}
=> ["a: 2220", "b: 4440", "c: 6660"]

# 不要把puts放在block里面，否则map之后返回一个array with nils
>> ["apple", "banana", "pear"].map {|fruit| fruit.capitalize }
=> ["Apple", "Banana", "Pear"]
>> ["apple", "banana", "pear"].map {|fruit| puts fruit.capitalize }
Apple
Banana
Pear
=> [nil, nil, nil]
```

### Comparison Operator

```ruby
>> 1 <=> 2
=> -1 # first less than second
>> 2 <=> 1
=> 1 # first greater than second
>> 1 <=> 1 
=> 0 # equal
```

### Sort

```ruby
>> array = [3, 1 ,4 ,5 ,2]
=> [3, 1, 4, 5, 2]
>> array.sort {|v1, v2| v1 <=> v2}
=> [1, 2, 3, 4, 5]
>> array.sort {|v1, v2| v2 <=> v1}
=> [5, 4, 3, 2, 1]

>> fruits = ['banana', 'apple', 'orange', 'pear']
=> ["banana", "apple", "orange", "pear"]
>> fruits.sort
=> ["apple", "banana", "orange", "pear"]

>> fruits.sort {|f1, f2| f1.length <=> f2.length}
=> ["pear", "apple", "banana", "orange"]

# 如果只比较一个property
>> fruits.sort_by {|fruit| fruit.length}
=> ["pear", "apple", "banana", "orange"]

# 按照string的最后一个字母排序
>> fruits.sort_by {|fruit| fruit.reverse}
=> ["banana", "orange", "apple", "pear"]

# 可以对hash排序，但是hash会被先转换成array（to_a），然后给出排序规则，返回一个array。
>> hash = {"a"=>555, "b"=>333, "c"=>222, "d"=>111}
=> {"a"=>555, "b"=>333, "c"=>222, "d"=>111}
>> hash.sort {|item1, item2| item1[0] <=> item2[0]}
=> [["a", 555], ["b", 333], ["c", 222], ["d", 111]]
>> hash.sort {|item1, item2| item1[1] <=> item2[1]}
=> [["d", 111], ["c", 222], ["b", 333], ["a", 555]]
```

### Inject

就是scheme的accumulator

memo初始值，如果没给取序列的第一个，每进行一次操作，改变一次memo的值。例如：

```ruby
>> (1..10).inject {|memo, n| memo + n }
=> 55
>> (1..10).inject {|memo, n| memo }
=> 1
>> (1..10).inject {|memo, n| n }
=> 10
>> (1..10).inject(100) {|memo, n| memo }
=> 100
>> (1..10).inject(100) {|memo, n| memo + n }
=> 155
>> array
=> [3, 1, 4, 5, 2]
>> product = array.inject {|memo, n| memo * n}
=> 120

# 简便方法
>> (1..10).inject(:+)
=> 55
>> (1..10).inject(0, :+)
=> 55

# 返回最长的
>> fruits = ['apple', 'pear', 'banana', 'plum']
=> ["apple", "pear", "banana", "plum"]
>> longest_word = fruits.inject do |memo, fruit|
?>   if memo.length > fruit.length
>>     memo
>>   else
?>     fruit
>>   end
>> end
=> "banana"
# 先是apple和pear比
# memo = apple
# fruit = pear
# apple比pear长，所以memo还是apple
# memo再和下一个banana比
# banana比apple长，所以memo变为banana
# banana再和plum比，还是banana长，所以返回banana

>> %w{ cat sheep bear }
=> ["cat", "sheep", "bear"]

# find the longest word
longest = %w{ cat sheep bear }.inject do |memo, word|
   memo.length > word.length ? memo : word
end

longest  #=> "sheep"

# 初始是10，在加上array中所有string的长度
=> ["Home", "Histroy", "Products", "Services", "Contact Us"]
>> menu.inject(10) {|memo, item| memo + item.length }
=> 47

def add(*numbers)
  numbers.inject(0) { |sum, number| sum + number }
end

puts add(1)
puts add(1, 2)
puts add(1, 2, 3)
puts add(1, 2, 3, 4)
# 1
# 3
# 6
# 10

def add(*numbers)
  numbers.inject(0) { |sum, number| sum + number }
end

def add_with_message(message, *numbers)
  "#{message} : #{add(*numbers)}"
end

puts add_with_message("The Sum is", 1, 2, 3)
# The Sum is : 6
```

## lambda

A **lambda** is a piece of code that you can store in a variable, and is an object. The simplest explanation for a **block** is that it is a piece of code that **can't** be stored in a variable and **isn't** an object.

```ruby
empty_block = lambda { }
puts empty_block.object_id #
puts empty_block.class
puts empty_block.class.superclass

# 26826780
# Proc
# Object
```

method可以变为lambda

```ruby
class Calculator
  def add(a, b)
    return a + b
  end
end

addition_method = Calculator.new.method("add")
addition =  addition_method.to_proc

puts addition.call(5, 6) # 11
```

### ruby block 与 scheme lambda比较

```ruby
mylist = [1, 2, 3 ,4]

mylist.map {|x| x + 1} # [2, 3, 4, 5]
mylist.select {|x| x.even?} # [2, 4]
mylist.select {|x| x.even?}.map {|x| x + 1} # [3, 5]
```
```scheme
(define mylist '(1 2 3 4))
(map (lambda (x) (+ x 1)) mylist) ;'(2 3 4 5)
(filter (lambda (x) (even? x)) mylist) ;'(2 4)

(map (lambda (x) (+ x 1))
  (filter even? mylist)) ;'(3 5)
```

### &block或lambda作为method参数

```ruby
def meth_captures(arg, &block)
    block.call(arg, 0) + block.call(arg.reverse, 1)
end
=> :meth_captures

meth_captures 'pony' do |word, num|
  puts "in callback word = #{word.inspect}, num = #{num.inspect}"
  word + num.to_s
end
in callback word = "pony", num = 0
in callback word = "ynop", num = 1
=> "pony0ynop1"

l = lambda {|word, num| word + num.to_s}
=> #<Proc:0x007ffc9413af18@(irb):9 (lambda)>

meth_captures 'pony', &l
=> "pony0ynop1"
```

### lambda也可以写成区块形式

```ruby
l = lambda do |string|
  if string == "try"
    return "There's no such thing" 
  else
    return "Do or do not."
  end
end
puts l.call("try") # Feel free to experiment with this
# There's no such thing
```

### yield

```ruby
def demonstrate_block number
  yield number
end

puts demonstrate_block(1) { |number| number + 1 }
# 2
```

这里我们注意到：

- 没有`lambda`出现
- method中有一个关键词`yield`
- method call后紧跟着（无逗号）一个`lambda`的body。

It turns out that one of the most common uses for a lambda involves passing exactly one block to a method which in turn uses it to get some work done. You'll see this all over the place in Ruby - Array iteration is an excellent example.

这其实是ruby中lambda最普遍的用法之一。把一个lambda的body紧跟在一个method call后面，获取参数，完成任务。Ruby中Array的iteration就是一个很好的例子。对于我们自己建立的class，要iterate里面的sequence，可以定义each method。

```ruby
class RandomSequence
  def initialize(limit, num)
    @limit, @num = limit, num
  end
  def each
    @num.times { yield (rand * @limit).floor }
  end
end
 
n = RandomSequence.new(10, 4)

n.each { |x| puts x }
# 每次产生4个小于10的随机数字
```

Ruby optimizes for this use case by offering the yield keyword that can call a single lambda that has been implicitly passed to a method without using the parameter list.

Ruby优化了这种用法，使得我们可以借助`yield`关键词，调用紧跟在method之后的lambda。同时不占用参数list。

```ruby
def calculate a, b
  yield a, b
end

calculate(2, 3) { |a, b| a + b } returns 5
calculate(2, 3) { |a, b| a - b } returns -1 
calculate(2, 3) { |a, b| a * b } returns 6 
```

block_given?可以根据情况判断后面是否有block给出。

```ruby
class MyArray
  attr_reader :array

  def initialize(array)
    @array = array
  end

  def sum(initial_value = 0)
    return @array.inject(initial_value, :+) unless block_given?
    # return @array.inject(initial_value) {|initial_value, n| initial_value + n} unless block_given?
    sum = initial_value
    @array.inject(initial_value) {|memo, n| memo + yield(n)}
  end
end

my_array = MyArray.new([1, 2, 3])
puts my_array.sum                  # 6
puts my_array.sum(10)              # 16
puts my_array.sum(0) {|n| n ** 2 } # 14
```

传递proc并计算时间

```ruby
def exec_time(proc)
  begin_time = Time.now
  proc.call
  Time.now - begin_time
end
```