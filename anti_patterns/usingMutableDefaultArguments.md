# Introduction

Python's default arguments are evaluated *once* when the function is *defined*, not each time the function is called. This means that if you use a mutable default argument and mutate it, you will and have mutated that object for all future calls to the functions as well.    

```python
def append_to(element, to=[]):
    to.append(element)
    return to
```

> ```
> >>> my_list = append_to(12)
> >>> print(my_list)
> [12]
> 
> >>> my_other_list = append_to(42)
> >>> print(my_other_list)
> [12, 42]
> ```

# What You Should Do Instead

Create a new object each time the function is called, by using a default arg to signal that no argument was provided (**None** is often a good choice).    

```python
def append_to(element, to=None):
    if to is None:
        to = []
    to.append(element)
    return to
```

