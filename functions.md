# Closures

## Late Binding Closures

### Introduction

```python
>>> def create_multipliers():
>>>     return [lambda x: i * x for i in range(5)]

>>> for multiplier in ceate_multipliers():
>>>    print(multiplier(2))
    
# 8
# 8
# 8
# 8
# 8
```

Python's closures are late binding. This means that the values of **variables** used in closures are looked up at the time the inner function is called.    

Here, whenever any of the returned functions are called, the value of `i` is looked up in the surrounding scope at call time. By then, the loop has completed and `i` is left with its final value of 4. The program upside has the same exact behavior showed below:    

```python
def create_multipliers():
    multipliers = []
    
    for i in range(5):
        def multiplier(x):
            return i * x
        multipliers.append(multiplier)
        
    return multipliers
```

### What You Should Do Instead

+ Using mutable default argument

```python
def create_multipliers():
    return [lambda x, i=i : i * x for in in range(5)]
```

+ Using the `functools.partial` function:

```python
from functools import partial
from operator import mul

def create_multipliers():
    return [partial(mul, i) for i in range(5)]
```

