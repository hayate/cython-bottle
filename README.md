bottle for cython
=================

this repo will try to hold the latest stable version of [bottle](https://github.com/defnull/bottle)
when required modifications will be made to allow cython compilation into byte code

## current version 0.12 (stable)


###to compile
1.  clone this repo: `git clone git@github.com:hayate/cython-bottle.git`
2.  install cython: `sudo pip install cython`
3.  cd into the cloned repo: `cd cython-bottle`
4.  compile bottle: `python setup.py build_ext --inplace`

##change log
### version 0.12 (stable) ###
added initialization of `__file__` from line 134
```python
# when bottle is byte compile with cython, __file__ is not available
import inspect
if not hasattr(sys.modules[__name__], '__file__'):
    __file__ = inspect.getfile(inspect.currentframe())
```

added workaround to [cython bug #600](http://trac.cython.org/ticket/600) at MultiDict initialization

from (around line 1817):
```python
    def __init__(self, *a, **k):
        self.dict = dict((k, [v]) for (k, v) in dict(*a, **k).items())
```

to:
```python
    def __init__(self, *a, **k):
        items = dict(*a, **k).items()
        self.dict = dict((k, [v]) for (k, v) in items)
```

----------
### version 0.10.9 (stable) ###
added initialization of `__file__` from around line 69
```python
# when bottle is byte compile with cython, __file__ is not available
import inspect
if not hasattr(sys.modules[__name__], '__file__'):
    __file__ = inspect.getfile(inspect.currentframe())
```


change below is not related with cython compilation, but if cStringIO is available
i prefer to import it rather then normal StringIO, also it fixes a small bug:
originally StringIO is imported only as BytesIO, but then in `class SimpleTALTemplate`
the variable `output` is initalize with the not imported StringIO, so the change below
tries to import cStringIO as BytesIO but also as StringIO.

from: (around line 118)
```python
from StringIO import StringIO as BytesIO, StringIO
```
to:
```python
    try:
        from cStringIO import StringIO as BytesIO, StringIO
    except ImportError:
        from StringIO import StringIO as BytesIO, StringIO
```


commented line 132 to avoid AttributeError:
```python
# below line commented to avoid:
"""
AttributeError: attribute '__doc__' of 'builtin_function_or_method' objects is not writable
"""
# tonat.__doc__ = """ Convert anything to native strings """
```
