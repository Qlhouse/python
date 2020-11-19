# Namespace



# Scope

## Understanding Scope

In programming, the **scope** of a *name* defines the area of a program in which you can unambiguously access that name, such as variables, functions, objects, and so on. A name will only be visible to and accessible by the code in its scope. Several programming languages take advantage of scope for avoiding name collisions and unpredictable behaviors.    

When you can access the value of a given name from someplace in your code, you'll say that the name is **in scope**. If you can't access the name, then you'll say that the name is **out of scope**.    

## Names and Scopes in Python

Variables in Python come into existence when you first assign them a value. On the other hand, functions and classes are available after you define them using `def` or `class`, respectively. Finally, `modules` exist after you import them. As a summary, you can create Python names through one of the following operations:    

| Operation                                        | Statement                                       |
| ------------------------------------------------ | ----------------------------------------------- |
| Assignments                                      | x = value                                       |
| Import operations                                | import module *or*<br />from module import name |
| Function definitions                             | def my_func(): ...                              |
| Argument definitions in the context of functions | def my_func(arg1, arg2, ..., argN): ...         |
| Class definitions                                | class MyClass: ...                              |

> There's an important difference between **assignment operations** and **reference or access operations**. When you reference a name, you're just retrieving its content or value. When you assign a name, you're either creating that name or modifying it.

Python uses the 



## LGEB rule

The scope of a name or variable depends on the **place** in your code where you create that variable. The Python scope concept is generally presented using a rule known as the **LGEB rule**.

+ **Global scope**: The names that you define in this scope are available to all your code.
+ **Local scope**: The names that you define in this scope are only available or visible to the code within the  scope.