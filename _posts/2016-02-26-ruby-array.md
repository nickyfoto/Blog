---
layout: post
title:  "Ruby Array"
date:   2016-02-26
author: "Huang Qiang"
tags: [ruby]
comments: true
---

## splat (*) operator

```ruby
*initial, last = [42, 43, 44]

initial: [42, 43]
last: 44

>> first, *middle, last = [42, 43, 44, 45, 46, 47]
=> [42, 43, 44, 45, 46, 47]
>> middle
=> [43, 44, 45, 46]

def zen(*args)
	[args.first, args.last]
end

p zen(42, 43, 44, 45, 46) # [42, 46]
```

You can however only splat the last parameter of a method.

它还可以把`range`或者`str`转换成array

```ruby
>> zen = *(1..10)
=> [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>> zen
=> [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
>> str = *"zen"
=> ["zen"]
>> str
=> ["zen"]
```

传递参数

```ruby
def zen(a, b)
	a + b
end

puts zen(*[41, 1]) # 42
```

用在block里

```ruby
[[1, 2, 3, 4], [42, 43]].each { |a, *b| puts "#{a} #{b}" }
# 1 [2, 3, 4]
# 42 [43]
```

Array转换成Hash

```ruby
puts Hash[4, 8]
puts Hash[ [[4, 8], [15, 16]] ]
{4=>8}
{4=>8, 15=>16}

ary = [[4, 8], [15, 16], [23, 42]]
puts Hash[*ary.flatten]
{4=>8, 15=>16, 23=>42}
```

## Array method

### count

```ruby
# 三者都一样
puts [4, 8, 15, 16, 23, 42].count
puts [4, 8, 15, 16, 23, 42].size
puts [4, 8, 15, 16, 23, 42].length


puts [42, 8, 15, 16, 23, 42].count(42)
puts ["Jacob", "Alexandra", "Mikhail", "Karl", "Dogen", "Jacob"].count("Jacob")

# 2
# 2

# 后跟block
[4, 8, 15, 16, 23, 42].count { |e| e.even? } # 4
```

### index

```ruby
puts [4, 8, 15, 16, 23, 42].index(15) # 2
puts [4, 8, 15, 16, 23, 42].index { |e| e % 2 == 0 } # 0
```

### flatten

```ruby
p [4, 8, 15, 16, 23, 42].flatten
p [4, [8], [15], [16, [23, 42]]].flatten
# [4, 8, 15, 16, 23, 42]
# [4, 8, 15, 16, 23, 42]

[4, [8], [15], [16, [23, 42]]].flatten(1)
# [4, 8, 15, 16, [23, 42]]

p [nil, 4, nil, 8, 15, 16, nil, 23, 42, nil].compact
# [4, 8, 15, 16, 23, 42]
```

### zip

```ruby
p [4, 8, 15, 16, 23, 42].zip([42, 23, 16, 15, 8])
# [[4, 42], [8, 23], [15, 16], [16, 15], [23, 8], [42, nil]]
```

### slice

```ruby
>> [4, 8, 15, 16, 23, 42].slice(2)
=> 15
>> [4, 8, 15, 16, 23, 42][2]
=> 15
>> [4, 8, 15, 16, 23, 42].slice(2..5)
=> [15, 16, 23, 42]
>> [4, 8, 15, 16, 23, 42][2..5]
=> [15, 16, 23, 42]
```

### product

```ruby
a = ['a', 'b', 'c']
r = a.product(a)
p r
[["a", "a"], ["a", "b"], ["a", "c"], ["b", "a"], ["b", "b"], ["b", "c"], ["c", "a"], ["c", "b"], ["c", "c"]]

# 可以对这个结果做处理
# 去掉首尾相等的
r.reject { |c| c.first == c.last }
[["a", "b"], ["a", "c"], ["b", "a"], ["b", "c"], ["c", "a"], ["c", "b"]]
# 还可以再继而去掉前后重复的
n = r.reject { |c| c.first == c.last }
n.inject([]) {|memo, c| memo  << c.sort}.uniq
[["a", "b"], ["a", "c"], ["b", "c"]]
```

### transpose

```ruby
a = [[1,2], [3,4], [5,6]]
a.transpose   #=> [[1, 3, 5], [2, 4, 6]]
```