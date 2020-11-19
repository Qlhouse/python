# What does `@property` do

It is often considered best practice to create getters and setters for a class's *public properties*. In Python, it is done using `@property`.

The `@property` lets a method to be accessed as an attribute instead of as a method with a `()`.    

```python
class Person():
    def __init__(self, firstname, lastname):
        self.first = firstname
        self.last = lastname
        self.fullname = self.first + ' '+ self.last

    def email(self):
        return '{}.{}@email.com'.format(self.first, self.last)
```

# When to use `@property`

> *If you change the value of an attribute inside a class, the other attributes that are derived from from the attribute you just changed don't automatically update.* 

Methods support change value automatically.

```python
# Converting fullname to a method provides the right fullname
# But it breaks old code that used the fullname attribute without the `()`
class Person():

    def __init__(self, firstname, lastname):
        self.first = firstname
        self.last = lastname

    def fullname(self):
        return self.first + ' '+ self.last

    def email(self):
        return '{}.{}@email.com'.format(self.first, self.last)
```

However we are using `person.fullname()` method with a `()` instead of `person.fullname` as attribute, it will break whatever code that used the `self.fullname` attribute. If you are building a product/tool, the chances are, other developers and users of your module can't use it at some point and all their code will break as well.    

The `@property` lets a method to be accessed as an attribute instead of as a method with a `()`.    

```python
# Adding @property provides the right fullname and does not break code!
class Person():

    def __init__(self, firstname, lastname):
        self.first = firstname
        self.last = lastname

    @property    
    def fullname(self):
        return self.first + ' '+ self.last

    def email(self):
        return '{}.{}@email.com'.format(self.first, self.last)
```

# The setter method

The `setter` method will be called everytime a user sets a value to the property decorated by `@property`.

The conventions need to follow when defining a `setter` method:

1. The `setter` method should have  the same name as the equivalent method that `@property` decorator;
2. It accepts as argument the value that user set to the property;
3. You need to add a `@{methodname}.setter` decorator just before the method definition.

```python
class Person():

    def __init__(self, firstname, lastname):
        self.first = firstname
        self.last = lastname

    @property    
    def fullname(self):
        return self.first + ' '+ self.last

    @fullname.setter
    def fullname(self, name):
        firstname, lastname = name.split()
        self.first = firstname
        self.last = lastname

    def email(self):
        return '{}.{}@email.com'.format(self.first, self.last)
```

> ```python
> # Init a Person 
> person = Person('selva', 'prabhakaran')
> print(person.fullname)  #> selva prabhakaran
> print(person.first)  #> selva
> print(person.last)  #> prabhakaran
> 
> # Setting fullname calls the setter method and updates person.first and person.last
> person.fullname = 'velu pillai'
> 
> # Print the changed values of `first` and `last`
> print(person.fullname) #> velu pillai
> print(person.first)  #> pillai
> print(person.last)  #> pillai
> ```

# The `deleter` method

The `deleter` method defines what happens when a property is deleted.    

You can create the `deleter` method by defining a method of the same name and adding a `@{methodname}.deleter` decorator.    

```python
class Person():
    
    def __init__(self, firstname, lastname):
        self.first = firstname
        self.last = lastname
        
    @property    
    def fullname(self):
        return self.first + ' '+ self.last
    
    @fullname.setter
    def fullname(self, name):
        firstname, lastname = name.split()
        self.first = firstname
        self.last = lastname
        
    @fullname.deleter
    def fullname(self):
        self.first = None
        self.last = None        
        
    def email(self):
        return '{}.{}@email.com'.format(self.first, self.last)
```

> ```python
> # Init a Person 
> person = Person('selva', 'prabhakaran')
> print(person.fullname)  #> selva prabhakaran
> 
> # Deleting fullname calls the deleter method, which erases self.first and self.last
> del person.fullname 
> 
> # Print the changed values of `first` and `last`
> print(person.first)  #> None
> print(person.last)  #> None
> ```

# Advantages of using a getter/setter/deleter

+ **Validation**: Before setting the internal property, you can validate that the provided value meets some criteria, and have it throw an error if it doesn't    
+ **Lazy loading**: Resources can by *lazily loaded* to defer work until it is actually needed, saving time and resources
+ **Abstraction**: Getters and setters allow you to abstract out the internal representation of data.     
+ **Degugging**: Since mutator methods can encapsulate any code, it becomes a great place for interception when debugging (or logging) your code.