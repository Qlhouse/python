> A for-loop enables you to execute a specific block of code however many times you want.    

# Introduction

`range()` allows you to generate a series of numbers within a given range. There are three ways you can call `range()`:    

1. `range(stop)` 

   ​	You will get a series of numbers that starts at `0` and includes every whole number up to, but not including, the number you have provided as `stop`.    

2. `range(start, stop)`

3. `range(start, stop, step)`

# Decrementing With `range()`

Python has a built-in `reversed()` function.    

```python
for i in reversed(range(-5, 10, 2)):
    print(i)
```

```python
for i in range(10, -5, -2):
    print(i)
```

# Usages for `range()`

`range()` is mainly used for two purposes:

1. Executing the body of a for-loop a specific number of times
2. Creating more efficient iterables of integers than can be done using lists or tuples

> You can access items in a `range()` by index.    

`range()` can take only integers as arguments.    

