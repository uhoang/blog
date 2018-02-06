---
date: "2017-02-09T15:06:09-05:00"
title: "Modules in Python"
tags: ["coding"]

---

I recently started reading, A byte of python, and found it a very helpful guide to the language for a beginner. The book was well-written, and explained most of important concepts in simple terms with self evident examples to follow. Thus, it made learning Python simple and interesting.

As a R programmer, I found that those sections such as modules, data structures and OOP are important to understand the basic building blocks of Python and how to make connection with the language that I’m more familiar with.

In this blog, I’m going to focus on the concept of modules and packages in Python.


# Modules

The purpose of writing a module is to reuse a number of functions in other programs. There are several ways to create a module. The simplest way is to create a file with a `.py` extension that contains functions and variables. Another method is to write the modules in the native language in which the Python interpreter was written, when compiled, they can be used from Python code when using the standard Python interpreter.

Example (save as module_using_sys.py):


```
import sys

print('The command line arguments are:')
for i in sys.argv:
    print(i)

print('\n\nThe PYTHONPATH is', sys.path, '\n')

```
Output:

```
$ python module_using_sys.py we are arguments
The command line arguments are:
module_using_sys.py
we
are
arguments

The PYTHONPATH is ['/tmp/python', 
# many entries are not show here 
'/Library/Frameworks/Python.framework/Versions/3.6/lib/python3.6/site-packages']
```
To specify command line arguments to the program in IDE, you can use try ... except statement. For the previous example, you can execute the following lines before print command and the for loop.

```
try: 
    __file__
except:
    sys.argv = [sys.argv, 'we', 'are', 'arguments']
```
Note that the current directory is the directory from which the program is launched. Run `import os; print(os.getcwd())` to find out the current directory of your program.

# Byte-compiled .pyc files

Python creates byte-compiled files with the extension `.pyc` to make importing a module from another program faster since portion of the processing required in importing a module is already done. Also, these byte-compiled files are platform-independent.

NOTE: These `.pyc` files are usually created in the same directory as the corresponding `.pyc` files. If Python does not have permission to write to files in that directory, then the `.pyc` files will not be created.

# The from … import statement

To avoid typing the `module_name.function_name` or `moduel_name.variable_name`, you can use the from `moduel_name import function_name`.

WARNING: To avoid name classes, avoid using the `from ... import` statement

# A module's `__name__`, `__version__`

Every Python module has its `__name__` defined. If it is `__main__`, that implies that the module is being run standalone by the user. It’s common practice for each module to declare its version number using `__version__`

Example (save as module_using_name.py):

```
if __name__ == '__main__':
    print('This program is being run by itself')

else:
    print('I am being imported from another module')    
```
Output:

```
$ python module_using_name.py
This program is being run by itself

$ python
>>> import module_using_name
I am being imported from another module
```
An example of making my own modules:

Example (myunit.py):

```
def scale(x):
  """To transform a list of number to 0-1 scale."""
    new_x = [(v - min(x))/(max(x) - min(x)) for v in x]
    return new_x
__version__ = '0.1'
```

Output:

```
import myunit

xs = list(range(1,6))
myunit.scale(xs)
[0.0, 0.25, 0.5, 0.75, 1.0]

print('Version', myunit.__version__)
Version 0.1

print('what does myunit.scale() does:', myunit.scale.__doc__)

what does myunit.scale() does: To transform a list of number to 0-1 scale.
```
In the above example, I also used docstring, a multi-line string to document the program and makes it easier to understand. You can access the docstring of the function by `__doc__`.

# The dir function

The dir function returns a list of attributes that the specified module contains. By default, it returns the list of attributes for the current module without any arguments is passed.

A note on `del` - this statement is used to delete a variable/name and after statement has run, in this case `del a`, completely remove a from the current Python environment.

Example:
```
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
# Packages

Packages are just folders of modules with a special `__init__.py` file that indicates to Python that this folder is special because it contains Python modules.

Example:

> Let’s say we want to create a package called ‘world’ with subpackages ‘asia’, ‘africa’, etc. and these subpackages in turn contain modules like ‘india’, ‘madagascar’, etc.

This is how the structure of the folders should look:

```
- <some folder present in the sys.path>/
    - world/
        - __init__.py
        - asia/
            - __init__.py
            - india/
                - __init__.py
                - foo.py
        - africa/
            - __init__.py
            - madagascar/
                - __init__.py
                - bar.py
```
