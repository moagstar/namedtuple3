***********
namedtuple3
***********

A drop in replacement for the standard function :code:`collections.namedtuple`
which can be used as a decorator so that the type name does not have to be
written twice:

.. code:: python

    from namedtuple3 import namedtuple

    @namedtuple
    def Point3(x, y, z) : 'an element of some set called a space'

vs:

.. code:: python

    from collections import namedtuple

    Point3 = namedtuple('Point3', 'x y z')

========
Features
========

- Can be used:

    - `Like the standard collections.namedtuple`_
    - `As a function decorator`_
    - `As a function decorator factory`_
    - `As a class decorator`_
    - `As a class decorator factory`_

- `Memoization`_ on the generated type.

- `Docstring`_ can be set on the generated type.

=====
Usage
=====

There are several usage patterns:

-----------------------
As a function decorator
-----------------------

When used as a function decorator you can think of it as defining the signature
of a top-level function which is the constructor for the type. The decorator
then replaces this with the actual generated namedtuple.

    >>> from namedtuple3 import namedtuple
    >>> @namedtuple
    ... def Point3(x, y, z):
    ...     """an element of some set called a space"""

-------------------------------
As a function decorator factory
-------------------------------

If the field names are dynamically generated, they can be passed to the
decorator factory:

    >>> @namedtuple(chr(x) for x in range(ord('a'), ord('d')))
    ... def Fields(*args): pass

The decorator factory can also be used to pass the verbose or rename parameters:

    >>> @namedtuple(range(3), rename=True)
    ... def Fields(*args): pass

--------------------
As a class decorator
--------------------

When used as a class decorator you can think of it as defining the name of the
type and the signature of the constructor. The decorator then replaces this
with the actual generated namedtuple.

    >>> @namedtuple
    ... class Point3:
    ...     """an element of some set called a space"""
    ...     def __init__(self, x, y, z):
    ...         pass

----------------------------
As a class decorator factory
----------------------------

If the field names are dynamically generated, they can be passed to the
decorator factory:

    >>> @namedtuple(chr(x) for x in range(120, 123))
    ... class Fields:
    ...     """an element of some set called a space"""

The decorator factory can also be used to pass the verbose or rename parameters:

    >>> @namedtuple(range(3), rename=True)
    ... class Fields: pass

----------------------------------------
Like the standard collections.namedtuple
----------------------------------------

It is also possible to use namedtuple3 like the standard
:code:`collections.namedtuple`

    >>> Point3 = namedtuple('Point3', 'x y z')

=========
Docstring
=========

It is also possible to customize the docstring of the generated type, either
by setting the docstring of the class or function being decorated:

    >>> @namedtuple
    ... def Point3(x, y, z):
    ...     """an element of some set called a space"""

    >>> @namedtuple
    ... class Point3:
    ...     """an element of some set called a space"""
    ...     def __init__(self, x, y, z):
    ...         pass

Or by passing the docstring parameter of the namedtuple function:

    >>> Point3 = namedtuple('Point3', 'x y z',
    ...                     docstring='an element of some set called a space')

===========
Memoization
===========

The generated classes are memoized which is particularly useful when generating
named tuples with dymamic field names to ensure that lots of classes are not
instantiated. See the examples/csv_named_tuple_reader.py for an demonstration
of how this might be useful.

==========
Motivation
==========

The main motivation for this is to provide an improved syntax for defining a
named tuple, as well as offering the ability to set the docstring on the newly
created type.

============
How it works
============

The function namedtuple selects an implementation based on the parameters that
are passed:

- when given a class we assume that a plain class decorator is intended

- when given a callable we assume that a plain function decorator is intended

    - the function should return either the field_names as expected in
      collections.namedtuple
    - or None in which case the function argument names are used as the
      field_names

- when fields_names is present in keyword arguments, or the second positional
  argument is iterable the assumption is that the standard form of namedtuple
  is intended, where the type name and an iterable is given

- otherwise we assume a decorator factory is desired with the verbose and
  replace flags passed as arguments.

====
TODO
====

- Better docstrings (by monkey patching _class_template)

- Signature in python3 instead of getargspec

- Sphinx, readthedocs

- performance tests

- test with tox

- travis, appveyor, circle

- setup.py pypi

- Don't lose additional methods in class decorator? Maybe create a class that is a child of the namedtuple

- default values, like https://github.com/gesellkammer/namedtuple2

    - when passing a callable the callable is invoked at construction to
      determine the default value

    - need to figure out how this will work when:

        - used like a decorator factory with dynamic field names
        - used like the std function

    - this is basic idea when used as a decorator:

.. code:: python

    import sys
    import socket
    import datetime
    import threading

    @namedtuple
    def LogMessage(
        message,
        message_type='info',
        server=socket.gethostname(),
        application=sys.executable,
        process=lambda: threading.current_thread().name,
        timestamp=lambda: datetime.datetime.now(),
    ) : 'message for the logging system'
