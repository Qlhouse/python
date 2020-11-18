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

# Data Classes

## What is Data Class

A data class is created using the decorator `@dataclass`, which is a new feature coming in Python 3.7.     

A data class is a regular Python class. The only thing that sets it apart is that it has basic **data model methods** like `.__init__()`, `__repr__()` and `__eq__()` implemented for you.

```python
from dataclasses import dataclass

@dataclass
class DataClassCard:
    rank: str
    suit: str
```

By default, data classes implement a `.__repr__()` method to provide a nice string representation and an `__eq__()` method that can do basic object comparison.

```python
class RegularCard
    def __init__(self, rank, suit):
        self.rank = rank
        self.suit = suit
        
    def __repr__(self):
        return (f'{self.__class__.__name__}'
                f'(rank={self.rank!r}, suit={self.suit!r})')
    
    def __eq__(self, other):
        if other.__class__ is not self.__class__:
            return NotImplemented
        return (self.rank, self.suit) == (other.rank, other,suit)
```

## Alternatives to Data Class

### **namedtuple**

```python
from collections import namedtuple

NamedTupleCard = namedtuple('NamedTupleCard', ['rank', 'suit'])
```

> ```python shell
> >>> queen_of_hearts = NamedTupleCard('Q', 'Hearts')
> >>> queen_of_hearts.rank
> 'Q'
> >>> queen_of_hearts
> NamedTupleCard(rank='Q', suit='Hearts')
> >>> queen_of_hearts == NamedTupleCard('Q', 'Hearts')
> True
> ```

A *namedtuple* is a regular tuple.     

The *namedtuple* comes with some restrictions. For instance, it's hard to add default values to some of the fields in a *namedtuple*. A *namedtuple* is also by nature immutable. That is, the value of a *namedtuple* can never change. In some applications, this is an awesome feature, but in other settings, it would be nice to have more flexibility.    

### `attrs` project

## Basic Data Classes

```python
# Create a Position class that will represent geographic positions with a name as well as the latitude and longitude
from dataclasses import dataclass

@dataclass
class Position:
    name: str
    lon: float 
    lat: float
```

### Default Values

```python
@dataclass
class Position:
    name: str
    lon: float = 0.0
    lat: float = 0.0
```

### Type Hints

Adding some kind of type hint is mandatory when defining the field in your data class. Without a type hint, the field will not be a part of the data class. However, if you do not want to add explicit types to your data class, use `typing.Any`.

```python
from dataclasses import dataclass
from typing import Any

@dataclass
class WithoutExplicitTypes:
    name: Any
    value: Any = 42
```

### Adding Methods

```python
from dataclasses import dataclass
from math import asin, cos, radians, sin, sqrt

@dataclass
class Position:
    name: str
    lon: float = 0.0
    lat: float = 0.0

    def distance_to(self, other):
        r = 6371  # Earth radius in kilometers
        lam_1, lam_2 = radians(self.lon), radians(other.lon)
        phi_1, phi_2 = radians(self.lat), radians(other.lat)
        h = (sin((phi_2 - phi_1) / 2)**2
             + cos(phi_1) * cos(phi_2) * sin((lam_2 - lam_1) / 2)**2)
        return 2 * r * asin(sqrt(h))
```

## More Flexible Data Class

```python
from dataclasses import dataclass
from typing import List

@dataclass
class PlayingCard:
    rank: str
    suit: str

@dataclass
class Deck:
    cards: List[PlayingCard]
```

### Advanced Default Values

```python
from dataclasses import dataclass
from typing import List

RANKS = '2 3 4 5 6 7 8 9 10 J Q K A'.split()
SUITS = '♣ ♢ ♡ ♠'.split()

@dataclass
class PlayingCard:
    rank: str
    suit: str
        
def make_french_deck():
    return [PlayingCard(r, s) for s in SUITS for r in RANKS]

@dataclass
class Deck:
    cards: List[PlayingCard] = field(default_factory=make_french_deck)
```

The `field()` specifier is used to customize each field of a data class individually. The parameters `field()` supports:

