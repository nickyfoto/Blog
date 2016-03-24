---
layout: post
title:  "Ruby Object Types and Control Structures"
date:   2016-02-26
author: "Huang Qiang"
tags: [ruby]
comments: true
---

## Ruby Object Types

### Variables

* Everything is ruby is an object execpt variables.
* ruby variable convention: all lowercase underscore 
* 大写开头或全大写会被认为是 constant

#### Variable Scope and Indicators

Scope    | Indicators
-------- | ----
Global   | $variable
Class    | @@variable
Instance | @variable
Local    | variable
Block    | variable

<br>

### String

* ruby 单引号相当于 python 的 repr（verbatim 一字不差）
* 变量替换 #{var_name} 括号内可求值
* `%Q{string}` treat this string the same as if it had been in double quotes
* `%q{string}` treat this string the same as if it had been in single quotes

### Arrays

* array[index] 如果这个位置没有obj，返回nil而不是报错
* array[index] = obj，如果index没有值，中间用nil填补
* array << value，append obj到array，性能最佳
* array.push(obj) 同上
* array.pop 删除并返回最后一个element
* array.shift 删除并返回第一个element
* array.unshift(obj) 把obj添加到最头上
* array.delete_at(index)，返回被删除的obj，如果index无，返回nil
* array.delete(obj)，删除array中的某个obj，如无，返回nil
* inspect 返回string
* array可以加减，但是不改变array本身。如果减自身没有的，则返回自身

#### Arrays operation

##### Union operator

```ruby
union_example = ["a", "b", "a"] | ["c", "c"]
p union_example
=> ["a", "b", "c"]
```

##### Intersection operator

```ruby
>> intersection = ['a', 'b', 'c'] & ['b', 'c']
=> ["b", "c"]
```

##### Difference operator

```ruby
>> array_difference = [1,2,3, 1,2,3] - [1]
=> [2, 3, 2, 3]
```

### hash

#### 创建新hash

```ruby
# 常用方法
options = { :font_size => 10, :font_family => "Arial" }
options = { font_size: 10, font_family: "Arial" }

# 特别方法一
>> chuck_norris = Hash[:punch, 99, :kick, 98, :stops_bullets_with_hands, true]
=> {:punch=>99, :kick=>98, :stops_bullets_with_hands=>true}
>> chuck_norris
=> {:punch=>99, :kick=>98, :stops_bullets_with_hands=>true}
# 特别方法二
>> a = [:a, 1]
=> [:a, 1]
>> b = [:b, 2]
=> [:b, 2]
>> c = [a, b]
=> [[:a, 1], [:b, 2]]
>> d = Hash[c]
=> {:a=>1, :b=>2}
>> d
=> {:a=>1, :b=>2}

# 用class.new method可以设置初始值
>> h = Hash.new('default')
=> {}
>> h[:anything]
=> "default"
```

hash.key(value)，返回这个value的key；如无，返回nil

### symbol

#### feature

- A label used to identify a piece of data
- A symbol is only stored in memory one time, string created many times.
- like an immutable string whose value is itself
- symbols are not variables

```ruby
>> nil.respond_to?(:to_i)
=> true
>> :to_i
=> :to_i
>> nil.to_i
=> 0
```

```ruby
>> "test".object_id
=> 70346480937300
>> "test".object_id
=> 70346480906760
>> :test.object_id
=> 355868
>> :test.object_id
=> 355868
```

#### usage

-  use symbol as keys in hashes to avoid the same key in different hashes
- use symbol to pass messages around between different parts of program

### range

* range在ruby里是Range Class的instance，不是array
* Exclusive range 1...10 (like python range)
* Inclusive range 1..10
* **他们的end或者last都返回10**
* [*range] 可以把range展开成array
* `'a'..'m'` 也是range

### Constants

- Constants are similar to variables
  - Not true objects
  - point to objects
- Constants are different than variables
  - A contant is constant

- 大写开头都会被认为是constant
- 改变Constant的pointer会被警告

### Boolean

* true.class :=> TrueClass
* false.class :=> FalseClass
* **ALL values in Ruby are evaluated to true, EXCEPT for FALSE and NIL.**

```ruby
>> if 0
>>   puts "Hey, 0 is considered to be a truth in Ruby"
>> end
Hey, 0 is considered to be a truth in Ruby
=> nil
```

## Control Structures

### unless

与if相反, unless 是ruby中对false的检测。如果unless后面跟false，则执行它的从句，通常用于检测`x`有没有。

```ruby
>> puts "Yes!" if true
Yes!
=> nil
>> puts "No!" unless false
No!
=> nil
>> puts "No!" unless true
=> nil
```
换行时要注意

```ruby
raise("Boom!") unless
  ship_stable # 可以正确parsing
  
raise("Boom!") 
  unless (ship_stable) # 报错，因为上一个句子本身是一个statement
```

### case

```ruby
case test_value
when value
  ...
when value
  ...
else
  ...
end
```

### ternary operator

```ruby
boolean ? code1 : code2
```

### or

```ruby
x = y || z
# means
if y
  x = y
else
  x = z
end
```

### or-equals

```ruby
x ||= y
# means
x = y unless x
```

### LOOPS

#### Loop control keywords

- break = Terminiate the whole loop
- next = Jump to the next loop
- redo = Redo this loop
- retry = Start the whole loop over

Examples:

```ruby
>> x = 0
=> 0
>> puts x += 2 while x < 10
2
4
6
8
10
=> nil

>> y = 3245
=> 3245
>> puts y /= 2 until y <= 1
1622
811
405
202
101
50
25
12
6
3
1
=> nil
```

### iterators

- Integer/floats: times, upto, downto, step
- Range: each, step
- String: each, each\_line, each_byte
- Array: each, each\_index, each\_with_index
- Hash: each, each\_key, each\_value, each_pair

Examples:

```ruby
5.times do
  puts "Hello"
end

1.upto(5) {puts "Hello"}
5.downto(1) {puts "Hello"}
(1..5).each {puts "Hello"}

>> 1.upto(5) do |i|
?>   puts "hello" + i.to_s
>> end
hello1
hello2
hello3
hello4
hello5
=> 1

>> fruits = ['banana', 'apple', 'pear']
=> ["banana", "apple", "pear"]
>> fruits.each do |fruit|
?>   puts fruit.capitalize
>> end
Banana
Apple
Pear
=> ["banana", "apple", "pear"]

>> for fruit in fruits
>>   puts fruit.capitalize
>> end
Banana
Apple
Pear
=> ["banana", "apple", "pear"]
```