# Understanding Scope

In programming, the **scope** of a *name* defines the area of a program in which you can unambiguously access that name, such as variables, functions, objects, and so on. A name will only be visible to and accessible by the code in its scope. Several programming languages take advantage of scope for avoiding name collisions and unpredictable behaviors.    

When you can access the value of a given name from someplace in your code, you'll say that the name is **in scope**. If you can't access the name, then you'll say that the name is **out of scope**.    

# Names and Scopes in Python

Variables in Python come into existence when you first assign them a value. On the other hand, functions and classes are available after you define them using `def` or `class`, respectively. Finally, `modules` exist after you import them. As a summary, you can create Python names through one of the following operations:    

| Operation                                        | Statement                                       |
| ------------------------------------------------ | ----------------------------------------------- |
| Assignments                                      | x = value                                       |
| Import operations                                | import module *or*<br />from module import name |
| Function definitions                             | def my_func(): ...                              |
| Argument definitions in the context of functions | def my_func(arg1, arg2, ..., argN): ...         |
| Class definitions                                | class MyClass: ...                              |

> There's an important difference between **assignment operations** and **reference or access operations**. When you reference a name, you're just retrieving its content or value. When you assign a name, you're either creating that name or modifying it.

Python uses the location of the name assignment or definition to associate it with a particular scope. In other words, where you assign or define a name in your code determines the scope or visibility of that name.



# Python Scope vs. Namespace

In Python, the concept of scope is closely related to the concept of the *namespace*. Python scopes are implemented as *dictionaries* that map names to objects. These dictionaries are commonly called **namespaces**. There are the concrete mechanisms that Python uses to store names. They're stored in a special attribute called `.__dict__`.    

Names at the top level of a module are stored in the module's namespace. In other words, they're stored in the module's `.__dict__` attribute.    

# LEGB rule

The scope of a name or variable depends on the **place** in your code where you create that variable. The Python scope concept is generally presented using a rule known as the **LEGB rule**.

+ **Local (or function) scope**: The code block or body of any Python function or lambda expression. This Python scope contains the names that you define inside the function. <u>These names will only be visible from the code of the function. It's created at the function call, *not* at function definition</u>, so you'll have as many different local scopes as function calls. This is true even if you call the same function multiple times, or recursively. Each call will result in a new local scope being created.    
+ **Enclosing (or nonlocal) scope**: The enclosing scope only exists for inner functions. If the local scope is an *inner function*, then the enclosing scope is the scope of the outer (or enclosing) function. The names in the enclosing scope are visible from the code of the inner functions.    

> Notice that the local and enclosing Python scopes are searched only if you use a name inside function or a inner function.

+ **Global (or module) scope**: The top-most scope in a Python program, script, or module. This Python scope contains all of the names that you define at the top level of a program or a module. Name in global scope are visible from everywhere in you code.    
+ **Built-in scope**: The built-in Python scope is created or loaded whenever you run a script or open an interactive session. This scope contains names such as *keywords*, *functions*, *exceptions*, and other attributes that are built into Python. Names in this Python built-in scope are also available from everywhere in your code. It's automatically loaded by Python when you run a program or script.    

## Functions: The Local Scope

The local scope is Python scope created at function calls. Every time you call a function, you're also creating a new local scope.    

By default, parameters and names that you assign inside a function exist only within the function or local scope associated with the function call. When the function returns, the local scope is destroyed and the names are forgotten.      

You can inspect the names and parameters of a function using `.__code__`, which is an attribute that holds information on the function's internal code.    

## Inner Functions: The Enclosing Scope

Names that you define in the enclosing Python scope are commonly known as **nonlocal names**.    

The inner function is only visible to the enclosing function. It's a temporary function that comes to life only during the execution of its enclosing function.    

All the names that you create in the enclosing scope are visible for inner function. However, you can't modify names in the enclosing scope from inside a inner function unless you declare them as **nonlocal** in the inner function.    

## Modules: The Global Scope

Whenever you run a Python program or an interactive session, the interpreter executes the code in the module or script that serves as an entry point to your program. This module or script is loaded with the special name, `__main__`. From this point on, you can say that your main **global scope** is the scope of `__main__`.       

> In Python, the notions of global scope and global names are tightly associated with module files.    

To inspect the names within your main global scope, you can use the `dir()`. If you call `dir()` without arguments, then you'll git the list of names that live in your current global scope.     

There's only one global Python scope per program execution. This scope remains in existence until the program terminates and all its names are forgotten.     

You can access the value of any global name from any place in your code. However, you can't assign names inside functions unless you explicitly declare them as global names using a **global** statement.      

If you try to assign a value to a global name inside a function, then you'll be creating that name in the function's local scope, shadowing or overriding the global name. This means that you won't be able to change most variables that have been defined outside the function within the function.     

Good programming practice recommends using local names rather than global names. Here are some tips:

+ Write self-contained functions that rely on local names rather than global ones.
+ Try to use unique objects names, no matter what scope you're in
+ Avoid global name modifications throughout your programs
+ Avoid cross-module name modifications
+ Use global names as constants that don't change during your program's execution.

## built-ins: The Built-In Scope

The **built-in scope** is a special Python scope that's implemented as a standard library module named **builtins** in Python 3.x. All of Python's built-in objects live in this module. They're automatically loaded to the built-in scope when you run the Python interpreter. You can get all the names it defines without importing any module.    

Notices that the names in `builtins` are always loaded into your global Python scope with the special name `__builtins__`.    

You can override or redefine any built-in name in your global scope. If you do so, then keep in mind that this will affect all your code.     

> If you're experimenting with some code and you accidentally re-assign a built-in name at the interactive prompt, then you can either restart your session or run `del name` to remove the redefinition from your global Python scope. This way, you're restoring the original name in the built-in scope.    

# Modifying the Behavior of a Python Scope



## The `global` Statement



When you try to assign a value to a global name inside a function, you create a new local name in the function scope. To modify this behavior, you can use a `global` statement. With this statement, you can define a list of names that are going to be treated as global names.    

The statement consists of the `global` keyword followed by one or more names separated by commas. You can also use multiple `global` statements with a name (or a list of names). All the names that you list in a `global` statement will be mapped to the global or module scope in which you define them.    

## The `nonlocal` Statement

Nonlocal names can be accessed from inner functions, but not assigned or updated. If you want to modify them, then you need to use a `nonlocal` statement. With a `nonlocal` statement, you can define a list of names that are going to be treated as nonlocal.    

The `nonlocal` statement consists of the  `nonlocal` keyword followed by one or more names separated by commas. These names will refer to the same names in the enclosing Python scope.     

You can't use `nonlocal` in either the global scope or in a local scope. Besides, you can't create nonlocal names by declaring them in a `nonlocal` statement in a inner function.    



# Using Enclosing Scopes as Closures

Closures are a special use case of the enclosing Python scope. *When you handle a nested function as data, the statements that make up that function are packaged together with the environment in which execute*. The resulting object is known as a **closure**. In other words, a closure is an inner function that carries information about its enclosing scope, even though this has completed its execution.    

> You can also call this kind of function as a **factory**, a **factory function**, or a **closure factory** to specify that the function builds and returns closures, rather than classes or instances.    

*Closures provide a way to retain state information between function calls*. This can be useful when you want to write code based on the concept of **lazy of delayed evaluation**.    

```python
def mean():
    sample = []
    def _mean(number):
        sample.append(number)
        return sum(sample) / len(sample)
    return _mean
```



# Bringing Names to Scope with `import`

When you write a Python program, you typically organize the code into several modules. For your program to work, you'll need to bring the names in those separate modules to your `__main__` module. To do that, you need to `import` the modules or the names explicitly. This is the only way you can those names in you main global Python scope.    

# Discovering Unusual Python Scopes

You'll find some Python structures where name resolution seems not to fit into the LEGB rule for Python scopes:    

+ Comprehensions
+ Exception blocks
+ Classes and instances

## Comprehension Variables Scope

A **comprehension** is a compact way to process all or  part of the elements in a collection or sequence. You can use comprehensions to create **lists**, **dictionaries** and **sets**.    

Comprehensions consist of a pair of brackets ([]) or curly braces ({}) containing an expression, followed one or more `for` clauses and then zero or one `if` clause per `for` clause. The `for` clause in a comprehension works similarly to a traditional *for loop*. *The loop variable in a comprehension is local to the structure*.    

Once you run the comprehension, the loop variable is forgotten and you can't access its value anymore.    

> Note that this only applies to comprehensions. When it comes to regular `for` loops, the loop variable holds the last value processed by the loop.    

## Exception Variables Scope

The **exception variable** is a variable that holds a reference to the exception raised by a `try` statement. In Python 3.x, such variables are local to the `except` block and are forgotten when the block ends.    

## Class and Instance Attributes Scope

When you define a class, you're creating a new local Python scope. The names assigned at the top level of the class live in this local scope. The names that you assigned inside a `class` statement don't clash with names elsewhere.    

Unlike functions, the class local scope isn't created at call time, but at *execution time*. Each class object has its own `.__dict__` attribute that holds the *class scope* where all the class attributes live.        

> Python's default arguments are evaluated once when the function is defined.     
>
> The local scope is Python scope created at function calls.    

The names in the `.__dict__` are visible to all instances of the class and to the class itself. To get access to a class attribute from outside the class, you need to use the **dot notation**.    

If you try to access an attribute that isn't defined inside a class, then you'll get an **AttributeError**.    

*Whenever you call a class, you're creating a new instance of that class*. Instances have their own `.__dict__` attribute that holds the names in the instance local scope. These names are commonly called **instance attributes** and are local and specific to each instance. This means that if you modify an instance attribute, then the changes will be visible only to that specific instance.    

