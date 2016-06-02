# namedtuple_decorator

A drop in replacement for the standard function `collections.namedtuple` that
can be used as a class and function decorator, while still supporting the
current standard usage:

```python
from namedtuple_decorator import namedtuple

# The basic function decorator uses the function argument names as field names
@namedtuple
def Point3d(x, y, z):
    """an element of some set called a space."""

# However you may want to generate the field names dynamically, in this case
# you can return field_names as expected by collections.namedtuple from the
# function.
@namedtuple
def Point3d():
    """an element of some set called a space."""
    return (chr(i) for i in range(120, 123))

# It is also possible to use as a class decorator, in which case your class
# should define a _fields member which gives field_names as expected by
# collections.namedtuple
@namedtuple
class Point3d:
    """an element of some set called a space."""
    _fields = 'x y z'

# The function can still be used like collections.namedtuple
Point3d = namedtuple('Point3d', 'x y z')
```

Using as a decorator avoids having to repeat the name. It is also possible to
set the docstring of the namedtuple using the decorator syntax.

The replace and verbose parameters are supported as parameters to both the
class and function decorator, for example:

```python
@namedtuple(replace=True)
def Point(x, y): pass
```

## Installation

Installing from PyPI using pip:

```bash
$ pip install table2dicts
```

Installing from source:

```bash
$ python setup.py install
```

## Motivation

The main motivation for this is to provide an improved syntax for defining a
named tuple, as well as offering the ability to set the docstring on the newly
created type.

## How it works

The function namedtuple selects an implementation based on the parameters that
are passed:

* when given a class we assume that a plain class decorator is intended
* when given a callable we assume that a plain function decorator is intended
    - the function should return either the field_names as expected in
      collections.namedtuple
    - or None in which case the function argument names are used as the
      field_names
* when fields_names is present in keyword arguments, or the second positional
  argument is iterable we assume the classic form of namedtuple is intended
* otherwise we assume a decorator factory is desired with the verbose and
  replace flags passed as arguments.