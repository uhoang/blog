---
title: "Modules in python"
date: 2017-02-09T15:06:09-05:00

---

# Modules

Writing a module to reuse a number of functions in other programs. The simplest way of writing a module is to create a file with a `.py` extension that contains functions and variables. Another method is to write the modules in the native language in which the Python interpreter was written, when compiled, they can be used from Python code when using the standard Python interpreter.

Example:

```
import sys

#To specify command line arguments to the program in IDE.
try: 
    __file__
except:
    sys.argv = [sys.argv, 'wordcount.py', '--count', 'small.txt']
print('The command line arguments are:')
for i in sys.argv:
    print(i)

The command line arguments are:
['/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages/ipykernel/__main__.py', '-f']
wordcount.py
--count
small.txt

```

# Byte-compiled .pyc files

Python creates byte-compiled files with the extension `.pyc` to make importing a module from another program faster since portion of the processing required in importing a module is already done. Also, these byte-compiled files are platform-independent.

# A module's `__name__`, `__version__`

Example: 
```
if __name__ == '__main__':
  print('This program is being run by itself')

else:
  print('I am being imported from another module')  

```

### How it works:

Every Python module has its `__name__` defined. If this is `__main__`, that implies that the module is being run standalone by the user. It's common practice for each module to declare it's version number using `__version__`

# The `dir` function

Example: 

```
$ python
>>> import sys

# get names of attributes in sys module
>>> dir(sys)
['__displayhook__', '__doc__',
'argv', 'builtin_module_names',
'version', 'version_info']
# only few entries shown here

# get names of attributes for current module
>>> dir()
['__annotations__', '__builtins__', '__doc__', '__loader__', 
'__name__', '__package__', '__spec__', 'sys']

# create a new variable 'a'
>>> a = 5

>>> dir()
['__builtins__', '__doc__', '__name__', '__package__', 'a']

# delete/remove a name
>>> del a

>>> dir()
['__builtins__', '__doc__', '__name__', '__package__']

```

### How it works:
The `dir` function returns a list of attributes that the specified module contains. By default, it returns the list of attributes for the current module without any arguments is passed. 

A note on `del` - this statement is used to delete a variable/name and after statement has run, in this case `del a`, completely remove `a` from the current Python environment.

# Data structures

Example: 

