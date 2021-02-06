---
title: inspect模块
categories: Python语法
date: 2018-12-27 17:20:16
---
### inspect -- Inspect live objects

&emsp;&emsp;The `inspect` module provides several useful functions to help get information about live objects such as `modules`, `classes`, `methods`, `functions`, `tracebacks`, `frame objects` and `code objects`. For example, it can help you examine the contents of a `class`, retrieve the source code of a `method`, extract and format the argument list for a `function`, or get all the information you need to display a detailed `traceback`.<!--more-->
&emsp;&emsp;There are four main kinds of services provided by this module: type checking, getting source code, inspecting classes and functions, and examining the interpreter stack.

### Retrieving source code

&emsp;&emsp;`inspect.getdoc(object)`: Get the documentation string for an object, cleaned up with `cleandoc()`. If the documentation string for an object is not provided and the object is a `class`, a `method`, a `property` or a `descriptor`, retrieve the documentation string from the inheritance hierarchy.
&emsp;&emsp;Changed in `version 3.5`: Documentation strings are now inherited if not overridden.
&emsp;&emsp;`inspect.getcomments(object)`: Return in a single string any lines of comments immediately preceding the object's source code (for a `class`, `function`, or `method`), or at the top of the `Python` source file (if the object is a `module`). If the object's source code is unavailable, return `None`. This could happen if the object has been defined in `C` or the `interactive shell`.
&emsp;&emsp;`inspect.getfile(object)`: Return the name of the (`text` or `binary`) file in which an object was defined. This will fail with a `TypeError` if the object is a `built-in` `module`, `class`, or `function`.
&emsp;&emsp;`inspect.getmodule(object)`: Try to guess which module an object was defined in.
&emsp;&emsp;`inspect.getsourcefile(object)`: Return the name of the `Python` source file in which an object was defined. This will fail with a `TypeError` if the object is a `built-in` `module`, `class`, or `function`.
&emsp;&emsp;`inspect.getsourcelines(object)`: Return a list of source lines and starting line number for an object. The argument may be a `module`, `class`, `method`, `function`, `traceback`, `frame`, or `code object`. The source code is returned as a list of the lines corresponding to the object and the line number indicates where in the original source file the first line of code was found. An `OSError` is raised if the source code cannot be retrieved.
&emsp;&emsp;Changed in `version 3.3`: `OSError` is raised instead of `IOError`, now an alias of the former.
&emsp;&emsp;`inspect.getsource(object)`: Return the text of the source code for an object. The argument may be a `module`, `class`, `method`, `function`, `traceback`, `frame`, or `code object`. The source code is returned as a single string. An `OSError` is raised if the source code cannot be retrieved.
&emsp;&emsp;Changed in `version 3.3`: `OSError` is raised instead of `IOError`, now an alias of the former.
&emsp;&emsp;`inspect.cleandoc(doc)`: Clean up indentation from docstrings that are indented to line up with blocks of code.
&emsp;&emsp;All leading `whitespace` is removed from the first line. Any leading `whitespace` that can be uniformly removed from the second line onwards is removed. Empty lines at the `beginning` and `end` are subsequently removed. Also, all `tabs` are expanded to `spaces`.