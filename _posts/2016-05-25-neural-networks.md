---
layout: post
title:  "Neural Networks"
date:   2016-05-25
author: "Huang Qiang"
tags: [Machine Learning]
comments: true
---

## Neural Networks

strengh * weight 的和是不是达到threshold。如果达到，output是1，否则是0。这种neural叫做perceptron。

perceptron用来计算[half-plane][1]。

Half-plane它有什么用呢？可以用来组成逻辑门。

- Perceptron Training

  前面weights都是手动设置的。这对机器学习没什么用。我们希望有一个系统，给到training examples，能够自动寻找到合适的 **weights** 来 map inputs to outputs。有两种方法可以实现这：
 
 - perceptron rule (threshold)
 - gradient descent / delta rule (unthreshold)

### Perceptron Rule

```py
import numpy as np

class Perceptron:

    def activate(self,inputs):
        '''Takes in @param inputs, a list of numbers.
        @return the output of a threshold perceptron with
        given weights, threshold, and inputs.
        ''' 

        return np.dot(inputs, self.weights) > self.threshold
        
    def __init__(self,weights=None,threshold=None):
        if weights is not None:
            self.weights = weights
        if threshold is not None:
            self.threshold = threshold

a = Perceptron(np.array([1, 2]), 0)
print a.activate(np.array([1, -1])) # False
print a.activate(np.array([-1, 1])) # True
print a.activate(np.array([2, -1])) # False
```


How to set the weights of a single unit (perceptron) so that it matches some training set.

举了个例子，X是training set，都是vectors。output y 是0或1。我们想要做的是set the weights to capture the same dataset。

We modify the weights over time

因为之前是需要判断

$$\sum_i WiXi \geq \theta$$

两边减去 $$\theta$$，变成

$$\sum_i WiXi - \theta \geq 0$$

可以把 $$-\theta$$ 转换成另外一个weight。

例如：一个weights是 [2, 2] 的 perceptron，怎样移除 $$\theta = 3$$呢？

我们可以把 2x + 2y >= 3 变为 2x + 2y - 3 >= 0

也就是把 $$(x, y) \rightarrow (2, 2)$$ 变为 $$(x, y, 1) \rightarrow (a, b, -\theta)$$。上面式子两遍都除3，得：

$$\frac23x+\frac23y+(-1)\times1 \geq 0$$ 即：$$(x, y, 1) \rightarrow (\frac23, \frac23, -1)$$

怎么学呢？

y: target

$$\hat y$$: output

n: learning rate

x: input

$$Wi = wi + \triangle Wi$$

$$\triangle Wi = n(y - \hat y) Xi$$

$$\hat y = (\sum_iWiXi \geq 0)$$

$$y$$ | $$\hat y$$ | $$y - \hat y$$
----- | --- | ---
   0  |  0  | 0 
   0  |  1  | -1
   1  |  0  | 1
   1  |  1  | 0
 
如果算法和结果一致，则weight不需要改变，如果结果是0，算法是1，说明算法比实际值要大，所以要调小一个 learning rate 乘 Xi；如果结果是1，算法是0，说明算法小了，weights需要增大一个 learning rate 乘 Xi。

```py
import numpy as np

class Perceptron:
    
    def activate(self,values):
        strength = np.dot(values,self.weights)
        
        if strength>self.threshold:
            result = 1
        else:
            result = 0
            
        return result

    def update(self,values,train,eta=.1):
        
        target = train
        inputs = values
        for i in range(len(inputs)):
            output = self.activate(inputs[i])
            delta_w = eta * (target[i] - output) * inputs[i]
            self.weights = self.weights + delta_w
        return self.weights

        
        
    def __init__(self,weights=None,threshold=None):
        if weights is not None:
            self.weights = weights
        if threshold is not None:
            self.threshold = threshold

a = Perceptron(np.array([1, 1, 1]), 0)
b = Perceptron(np.array([1, 2, 3]), 0)
c = Perceptron(np.array([3, 0, 2]), 0)

print a.update(np.array([[2, 0, -3]]), np.array([1]))
print b.update(np.array([[3, 2, 1], [4, 0, -1]]), np.array([0, 0]))
print c.update(np.array([[2, -2, 4], [-1, -3, 2], [0, 2, 1]]), np.array([0, 1, 0]))

# [ 1.2  1.   0.7]
# [ 0.7  1.8  2.9]
# [ 2.7 -0.3  1.7]
```

还可以组成一个XOR 网络

```py
import numpy as np

class Perceptron:

    def evaluate(self,values):
        
        strength = np.dot(values,self.weights)
        if strength >= self.threshold:
            result = 1
        else:
            result = 0

        return result

    def __init__(self,weights=None,threshold=None):
        if weights is not None:
            self.weights = weights
        if threshold is not None:
            self.threshold = threshold
            

AND = Perceptron([0.5, 0.5], 0.75)
OR = Perceptron([2, 2], 0.5)
SUB = Perceptron([1, -1], 0.5)

Network = [[OR, AND], SUB]

def EvalNetwork(inputValues, Network):
    # print inputValues
    i = 0
    while i < len(Network) - 1:
        t1 = Network[i][0].evaluate(inputValues)
        t2 = Network[i][1].evaluate(inputValues)
        i += 1
        OutputValues = Network[i].evaluate([t1, t2])
    return OutputValues

print EvalNetwork(np.array([0, 0]), Network)
print EvalNetwork(np.array([0, 1]), Network)
print EvalNetwork(np.array([1, 0]), Network)
print EvalNetwork(np.array([1, 1]), Network)
```

[1]: https://en.wikipedia.org/wiki/Half-space_(geometry)