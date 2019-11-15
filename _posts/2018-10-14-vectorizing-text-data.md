---
layout: post
title:  "Vectorizing Text Data"
date:   2018-10-14
author: "Huang Qiang"
tags: [machine learning, sklearn, nlp]
comments: true
---



```python
# import necessary modules
from IPython.core.interactiveshell import InteractiveShell
InteractiveShell.ast_node_interactivity = "all"

%matplotlib inline
%config InlineBackend.figure_format = 'retina'

import numpy as np
import matplotlib.pyplot as plt
import pandas as pd

from evaluation import test
from utils import load_data

from sklearn.naive_bayes import MultinomialNB
```


```python
message0 = 'hello world hello hello world play'
message1 = 'test test test test one hello'

#Convert a collection of text documents to a matrix of token counts
from sklearn.feature_extraction.text import CountVectorizer
vectorizer = CountVectorizer()
bow = vectorizer.fit_transform([message0, message1])
print(bow)
print(type(bow))
```

      (0, 0)	3
      (0, 4)	2
      (0, 2)	1
      (1, 0)	1
      (1, 3)	4
      (1, 1)	1
    <class 'scipy.sparse.csr.csr_matrix'>


As you can see, `CountVectorizer` returns a sparse matrix encoding our texts with the number of times a particular word occurs.


```python
vocabulary = {v: k for k, v in vectorizer.vocabulary_.items()}
[vocabulary[i] for i in sorted([v for k,v in vectorizer.vocabulary_.items()])]
bow.toarray()
```




    ['hello', 'one', 'play', 'test', 'world']






    array([[3, 0, 1, 0, 2],
           [1, 1, 0, 4, 0]])



Note that you can see how the encoding information is saved in a sparse matrix. For `message0`, on indices [0,4,2] you have values [3,2,1].

If we set `binary=True` when encoding messages, our encoder only records the whether the word is present or not, ignoring the numbber of occurance. For our first implementation of `NaiveBayes`, we will simply encode the presence of each word.


```python
vectorizer_b = CountVectorizer(binary=True)
bow_b = vectorizer_b.fit_transform([message0, message1])
print(bow_b)
bow_b.toarray()
```

      (0, 0)	1
      (0, 4)	1
      (0, 2)	1
      (1, 0)	1
      (1, 3)	1
      (1, 1)	1





    array([[1, 0, 1, 0, 1],
           [1, 1, 0, 1, 0]])



$$\phi_{j|y=1} = \frac{\sum_{i=1}^m 1\{x_j^{(i)} = 1 \wedge y^{(i)} = 1\}}{\sum_{i=1}^m1\{y^{(i)} = 1\}}$$

$$\phi_{j|y=0} = \frac{\sum_{i=1}^m 1\{x_j^{(i)} = 1 \wedge y^{(i)} = 0\}}{\sum_{i=1}^m1\{y^{(i)} = 0\}}$$

$$\phi_y = \frac{\sum_{i=1}^m 1\{y^{(i) = 1}\}}{m}$$

