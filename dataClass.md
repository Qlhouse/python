# What is Data Class

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

# Alternatives to Data Class

## namedtuple

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

## `attrs` project

# Basic Data Classes

```python
# Create a Position class that will represent geographic positions with a name as well as the latitude and longitude
from dataclasses import dataclass

@dataclass
class Position:
    name: str
    lon: float 
    lat: float
```

## Default Values

```python
@dataclass
class Position:
    name: str
    lon: float = 0.0
    lat: float = 0.0
```

## Type Hints

Adding some kind of type hint is mandatory when defining the field in your data class. Without a type hint, the field will not be a part of the data class. However, if you do not want to add explicit types to your data class, use `typing.Any`.

```python
from dataclasses import dataclass
from typing import Any

@dataclass
class WithoutExplicitTypes:
    name: Any
    value: Any = 42
```

## Adding Methods

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

# More Flexible Data Class

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

## Advanced Default Values

```python
from dataclasses import dataclass, field
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

The `field()` specifier is used to customize each field of a data class individually.     

`dataclasses.field(*, default=MISSING, default_factory=MISSING, repr=True, hash=None, init=True, compare=True, metadata=None)`    

The parameters `field()` supports:

+ `default`: Default value of the field
+ `default_factory`: Function that return the initial value of the field
+ `init`: Use field in `.__init__()` method? (Default is `True`)
+ `repr`: Use field in `repr` of the object? (Default is `True`)
+ `compare`: Include the field in comparisons? (Default is `True`)
+ `hash`: Include the field when calculating `hash()`? (Default is to use the same as for `compare`)
+ `metadata`: A mapping with information about the field

## Prettify Representation

A Python object has two different string representations:

+ `repr(obj)` is defined by `obj.__repr__()` and should return a developer-friendly representation of `obj`.  If possible, this should be code that can recreate `obj`.
+ `str(obj)` is defined by `obj.__str__()` and should return a user-friendly representation of `obj`.

## Customize `@dataclass`

`@dataclasses.dataclass(*, init=True, repr=True, eq=True, order=False, unsafe_hash=False, frozen=False)`



# Immutable Data Classes

```python
from dataclasses import dataclass

@dataclass(frozen=True)
class Position:
    pass
```





# Inheritance







