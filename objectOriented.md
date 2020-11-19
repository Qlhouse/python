# Methods

## Instance Methods

```python
class MyClass:
    def method(self):
        return "instance method called", self
```

The first parameter in instance method is `self`, which points to an instance of `MyClass` when the method is called.    

Through the `self` parameter, instance method can freely access attributes and other methods on the same object. This gives them a lot of power when it comes to modifying an object's state.    

Not only can they modify object state, instance methods can also access the class itself through the `self__class__` attribute. This means instance methods can also modify class state.    

## Class Methods

```python
class Pizza:
    def __init__(self, ingredients):
        self.ingredients = ingredients

    def __repr__(self):
        return f'Pizza({self.ingredients!r})'

    @classmethod
    def margherita(cls):
        return cls(['mozzarella', 'tomatoes'])

    @classmethod
    def prosciutto(cls):
        return cls(['mozzarella', 'tomatoes', 'ham'])
```

Class method is marked with a `@classmethod` decorator. Instead of accepting a `self` parameter, class method take a `cls` parameter as the first parameter. Parameter `cls` points to the class when the method is called.    

Because the class method only has access to this `cls` argument, it can't modify object instance state, which would require access to `self`. However, class methods can still modify **class state** that applies across all instances of the class.    

## Static Methods

```python
import math

class Pizza:
    def __init__(self, radius, ingredients):
        self.radius = radius
        self.ingredients = ingredients

    def __repr__(self):
        return (f'Pizza({self.radius!r}, '
                f'{self.ingredients!r})')

    def area(self):
        return self.circle_area(self.radius)

    @staticmethod
    def circle_area(r):
        return r ** 2 * math.pi
```

Static method is marked with `@staticmethod` decorator. Static method takes neither a `self` nor a`cls` parameter (but of course it's free to accept an arbitrary number of other parameters).    

Static methods can neither access the  object state nor the class state. They work like regular functions   but belong to the class's namespace.    

> *Everything in Python is an object*.    

## Usages

### Static methods

**Static methods** can't access class or instance state because they don't take a `cls` or `self` argument. This is a bit limitation --- but it's also a great signal to show that a particular method is independent from everything else around it.    

Static methods also have benefits when it comes to writing test code.

### Class methods

Python only allows one `__init__` method per class. Using **class methods** is possible to add as many alternative constructors as necessary. This can make the interface for your classes self-documenting and simplify their usage.   

## Conclusion 

Techniques like that allow you to communicate clearly about parts of your class architecture so that new development work is naturally guided to happen within these set boundaries.     

Put differently, using static methods and class methods are ways to communicate developer intent while enforcing that intent enough to avoid most slip of the mind mistakes and bugs that would break the design.    

Applied sparingly and when it make sense, writing some of your methods that way can provide **maintenance benefits** and make it less likely that other developers use your classes incorrectly.    

