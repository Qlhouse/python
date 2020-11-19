# What is a Function



# Usages of Function

> In Python, functions are **first-class citizens**. This means that they are at the same level of any other object (integers, strings, lists, modules, and so on). You can dynamically create or destroy them, pass them to other functions, return them as values, and so forth.



# Inner Function



## Encapsulation

Using inner functions to protect them from everything happening outside of the function, meaning that they are hidden from the global scope.    

```python
def factorial(number):

    # Error handling
    if not isinstance(number, int):
        raise TypeError("Sorry. 'number' must be an integer.")
    if not number >= 0:
        raise ValueError("Sorry. 'number' must be zero or positive.")

    def inner_factorial(number):
        if number <= 1:
            return 1
        return number*inner_factorial(number-1)
    return inner_factorial(number)

# Call the outer function.
print(factorial(4))
```

## Keep it DRY





# Closures

A closure is a function object that remembers values in enclosing scopes even if they are not present in memory.     

A closure cause the inner function to remember the state of its environment when called. The closure "closes" the local variable on the stack, and this stays around after the stack creation has finished executing.

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

