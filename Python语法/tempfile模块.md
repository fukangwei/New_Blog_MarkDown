---
title: tempfile模块
date: 2019-02-09 21:33:45
categories: Python语法
---
### tempfile: Generate temporary files and directories

&emsp;&emsp;This module creates temporary files and directories. It works on all supported platforms. `TemporaryFile`, `NamedTemporaryFile`, `TemporaryDirectory`, and `SpooledTemporaryFile` are `high-level` interfaces which provide automatic cleanup and can be used as context managers. `mkstemp()` and `mkdtemp()` are `lower-level` functions which require manual cleanup.
&emsp;&emsp;All the `user-callable` functions and constructors take additional arguments which allow direct control over the location and name of temporary files and directories. Files names used by this module include a string of random characters which allows those files to be securely created in shared temporary directories. To maintain backward compatibility, the argument order is somewhat odd; it is recommended to use keyword arguments for clarity.
&emsp;&emsp;The module defines the following `user-callable` items:
&emsp;&emsp;`tempfile.TemporaryFile(mode='w+b', buffering=None, encoding=None, newline=None, suffix=None, prefix=None, dir=None)`: Return a `file-like` object that can be used as a temporary storage area. The file is created securely, using the same rules as `mkstemp()`. It will be destroyed as soon as it is closed (including an implicit close when the object is garbage collected). Under `Unix`, the directory entry for the file is either not created at all or is removed immediately after the file is created. Other platforms do not support this; your code should not rely on a temporary file created using this function having or not having a visible name in the file system.
&emsp;&emsp;The resulting object can be used as a context manager. On completion of the context or destruction of the file object the temporary file will be removed from the filesystem.
&emsp;&emsp;The `mode` parameter defaults to `w+b` so that the file created can be read and written without being closed. Binary mode is used so that it behaves consistently on all platforms without regard for the data that is stored. `buffering`, `encoding` and `newline` are interpreted as for `open()`.
&emsp;&emsp;The `dir`, `prefix` and `suffix` parameters have the same meaning and defaults as with `mkstemp()`.
&emsp;&emsp;The returned object is a true file object on `POSIX` platforms. On other platforms, it is a `file-like` object whose file attribute is the underlying true file object.
&emsp;&emsp;`tempfile.NamedTemporaryFile(mode='w+b', buffering=None, encoding=None, newline=None, suffix=None, prefix=None, dir=None, delete=True)`: This function operates exactly as `TemporaryFile()` does, except that the file is guaranteed to have a visible name in the file system (on `Unix`, the directory entry is not unlinked). That name can be retrieved from the name attribute of the returned `file-like` object. Whether the name can be used to open the file a second time, while the named temporary file is still open, varies across platforms (it can be so used on `Unix`; it cannot on `Windows NT` or later). If delete is `true` (the default), the file is deleted as soon as it is closed. The returned object is always a `file-like` object whose file attribute is the underlying true file object. This `file-like` object can be used in a with statement, just like a normal file.
&emsp;&emsp;`tempfile.SpooledTemporaryFile(max_size=0, mode='w+b', buffering=None, encoding=None, newline=None, suffix=None, prefix=None, dir=None)`: This function operates exactly as `TemporaryFile()` does, except that data is spooled in memory until the file size exceeds `max_size`, or until the file's `fileno()` method is called, at which point the contents are written to disk and operation proceeds as with `TemporaryFile()`.
&emsp;&emsp;The resulting file has one additional method, `rollover()`, which causes the file to roll over to an `on-disk` file regardless of its size.
&emsp;&emsp;The returned object is a `file-like` object whose `_file` attribute is either an `io.BytesIO` or `io.StringIO` object (depending on whether `binary` or `text` mode was specified) or a true file object, depending on whether `rollover()` has been called. This `file-like` object can be used in a with statement, just like a normal file.
&emsp;&emsp;`tempfile.TemporaryDirectory(suffix=None, prefix=None, dir=None)`: This function securely creates a temporary directory using the same rules as `mkdtemp()`. The resulting object can be used as a context manager. On completion of the context or destruction of the temporary directory object the newly created temporary directory and all its contents are removed from the filesystem.
&emsp;&emsp;The directory name can be retrieved from the name attribute of the returned object. When the returned object is used as a context manager, the name will be assigned to the target of the as clause in the with statement, if there is one.
&emsp;&emsp;The directory can be explicitly cleaned up by calling the `cleanup()` method.
&emsp;&emsp;`tempfile.mkstemp(suffix=None, prefix=None, dir=None, text=False)`: Creates a temporary file in the most secure manner possible. There are no race conditions in the file's creation, assuming that the platform properly implements the `os.O_EXCL` flag for `os.open()`. The file is readable and writable only by the creating `user ID`. If the platform uses permission bits to indicate whether a file is executable, the file is executable by no one. The file descriptor is not inherited by child processes.
&emsp;&emsp;Unlike `TemporaryFile()`, the user of `mkstemp()` is responsible for deleting the temporary file when done with it.
&emsp;&emsp;If `suffix` is not `None`, the file name will end with that `suffix`, otherwise there will be no suffix. `mkstemp()` does not put a dot between the file name and the suffix; if you need one, put it at the beginning of `suffix`.
&emsp;&emsp;If `prefix` is not `None`, the file name will begin with that `prefix`; otherwise, a default prefix is used. The default is the return value of `gettempprefix()` or `gettempprefixb()`, as appropriate.
&emsp;&emsp;If `dir` is not `None`, the file will be created in that directory; otherwise, a default directory is used. The default directory is chosen from a `platform-dependent` list, but the user of the application can control the directory location by setting the `TMPDIR`, `TEMP` or `TMP` environment variables. There is thus no guarantee that the generated filename will have any nice properties, such as not requiring quoting when passed to external commands via `os.popen()`.
&emsp;&emsp;If any of `suffix`, `prefix`, and `dir` are not `None`, they must be the same type. If they are bytes, the returned name will be bytes instead of str. If you want to force a bytes return value with otherwise default behavior, pass `suffix=b''`.
&emsp;&emsp;If `text` is specified, it indicates whether to open the file in `binary` mode (the default) or `text` mode. On some platforms, this makes no difference.
&emsp;&emsp;`mkstemp()` returns a tuple containing an `OS-level` handle to an open file (as would be returned by `os.open()`) and the absolute pathname of that file, in that order.
&emsp;&emsp;`tempfile.mkdtemp(suffix=None, prefix=None, dir=None)`: Creates a temporary directory in the most secure manner possible. There are no race conditions in the directory's creation. The directory is readable, writable, and searchable only by the creating `user ID`.
&emsp;&emsp;The user of `mkdtemp()` is responsible for deleting the temporary directory and its contents when done with it.
&emsp;&emsp;The `prefix`, `suffix`, and dir arguments are the same as for `mkstemp()`.
&emsp;&emsp;`mkdtemp()` returns the absolute pathname of the new directory.
&emsp;&emsp;`tempfile.gettempdir()`: Return the name of the directory used for temporary files. This defines the default value for the dir argument to all functions in this module.
&emsp;&emsp;Python searches a standard list of directories to find one which the calling user can create files in. The list is:

- The directory named by the `TMPDIR` environment variable.
- The directory named by the `TEMP` environment variable.
- The directory named by the `TMP` environment variable.
- A `platform-specific` location:

1. On `Windows`, the directories `C:\TEMP`, `C:\TMP`, `\TEMP`, and `\TMP`, in that order.
2. On all other platforms, the directories `/tmp`, `/var/tmp`, and `/usr/tmp`, in that order.

- As a last resort, the current working directory.

&emsp;&emsp;`tempfile.gettempdirb()`: Same as `gettempdir()` but the return value is in bytes.
&emsp;&emsp;`tempfile.gettempprefix()`: Return the filename prefix used to create temporary files. This does not contain the directory component.
&emsp;&emsp;`tempfile.gettempprefixb()`: Same as `gettempprefix()` but the return value is in bytes.
&emsp;&emsp;The module uses a global variable to store the name of the directory used for temporary files returned by `gettempdir()`. It can be set directly to override the selection process, but this is discouraged. All functions in this module take a dir argument which can be used to specify the directory and this is the recommended approach.
&emsp;&emsp;`tempfile.tempdir`: When set to a value other than `None`, this variable defines the default value for the dir argument to the functions defined in this module.
&emsp;&emsp;If `tempdir` is unset or `None` at any call to any of the above functions except `gettempprefix()` it is initialized following the algorithm described in `gettempdir()`.

### Examples

&emsp;&emsp;Here are some examples of typical usage of the tempfile module:

``` python
>>> import tempfile
>>> # create a temporary file and write some data to it
>>> fp = tempfile.TemporaryFile()
>>> fp.write(b'Hello world!')
>>> fp.seek(0)
>>> fp.read()  # read data from file
b'Hello world!'
>>> fp.close()  # close the file, it will be removed
>>> # create a temporary file using a context manager
>>> with tempfile.TemporaryFile() as fp:
...     fp.write(b'Hello world!')
...     fp.seek(0)
...     fp.read()
b'Hello world!'
# file is now closed and removed
>>> # create a temporary directory using the context manager
>>> with tempfile.TemporaryDirectory() as tmpdirname:
...     print('created temporary directory', tmpdirname)
# directory and contents have been removed
```

### Deprecated functions and variables

&emsp;&emsp;A historical way to create temporary files was to first generate a file name with the `mktemp()` function and then create a file using this name. Unfortunately this is not secure, because a different process may create a file with this name in the time between the call to `mktemp()` and the subsequent attempt to create the file by the first process. The solution is to combine the two steps and create the file immediately. This approach is used by `mkstemp()` and the other functions described above.
&emsp;&emsp;`tempfile.mktemp(suffix='', prefix='tmp', dir=None)`: Deprecated since `version 2.3`: Use `mkstemp()` instead.
&emsp;&emsp;Return an absolute pathname of a file that did not exist at the time the call is made. The `prefix`, `suffix`, and `dir` arguments are similar to those of `mkstemp()`, except that bytes file names, `suffix=None` and `prefix=None` are not supported.
&emsp;&emsp;**Warning**: Use of this function may introduce a security hole in your program. By the time you get around to doing anything with the file name it returns, someone else may have beaten you to the punch. `mktemp()` usage can be replaced easily with `NamedTemporaryFile()`, passing it the `delete=False` parameter:

``` python
>>> f = NamedTemporaryFile(delete=False)
>>> f.name
'/tmp/tmptjujjt'
>>> f.write(b"Hello World!\n")
13
>>> f.close()
>>> os.unlink(f.name)
>>> os.path.exists(f.name)
False
```