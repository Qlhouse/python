# Definition

A dictionary consists of a collection of key-value pairs. Each key-value maps the key to its associated value.    

+ You can define a dictionary by enclosing a comma-separated list of key-value pairs in curly braces ({}). A colon (:) separates each key from its associated value.    

```python
MLB_team = {
     'Colorado' : 'Rockies',
     'Boston'   : 'Red Sox',
     'Minnesota': 'Twins',
     'Milwaukee': 'Brewers',
     'Seattle'  : 'Mariners'
}
```

![dictionary](https://files.realpython.com/media/t.b3e3d8f2d100.png "Dictionary Mapping Location to MLB Team")



+ You can also construct a dictionary with the built-in `dict()` function. The argument to `dict()` should be a sequence of *key-value pairs*. A list of tuples works well.    

```python
MLB_team = dict([
     ('Colorado', 'Rockies'),
     ('Boston', 'Red Sox'),
     ('Minnesota', 'Twins'),
     ('Milwaukee', 'Brewers'),
     ('Seattle', 'Mariners')
])
```

If the key values are simple strings, they can be specified as *keyword arguments*.

```python
MLB_team = dict(
     Colorado='Rockies',
     Boston='Red Sox',
     Minnesota='Twins',
     Milwaukee='Brewers',
     Seattle='Mariners'
)
```

# Accessing Dictionary Values

