bottle for cython
=================

this repo will try to hold the latest stable version of [bottle](https://github.com/defnull/bottle)
when required modifications will be made to allow cython compilation into byte code

current version 0.10.9
----------------------

to compile
----------
1.  clone this repo `git clone git://github.com/hayate/bottle.git`
2.  install cython i.e `sudo pip install cython`
3.  cd into the cloned repo `cd bottle`
4.  compile bottle `python setup.py build_ext --inplace`

change log
----------
### of stable version 0.10.9 ###
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