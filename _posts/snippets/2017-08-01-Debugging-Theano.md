---
layout: blog
title: Debugging Theano
category: Snippets
---

- toc
{:toc}

# Using Test Values

```python
import numpy as np
import theano
import theano.tensor as T

theano.config.compute_test_value = 'warn'

x = T.dmatrix('x')
x.tag.test_value = np.array([[0, 1], [-1, -2]])
s = 1 / (1 + T.exp(-x))
logistic = theano.function([x], s)
# print logistic([[0, 1], [-1, -2]])
print s.tag.test_value
```

