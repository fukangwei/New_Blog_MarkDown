---
title: gzip模块
date: 2019-02-09 15:04:04
categories: Python语法
---
### gzip module

&emsp;&emsp;This module provides a simple interface to compress and decompress files just like the `GNU` programs `gzip` and `gunzip` would.
&emsp;&emsp;The data compression is provided by the `zlib` module.
&emsp;&emsp;The `gzip` module provides the `GzipFile` class, as well as the `open()`, `compress()` and `decompress()` convenience functions. The `GzipFile` class reads and writes `gzip-format` files, automatically compressing or decompressing the data so that it looks like an ordinary file object.
&emsp;&emsp;Note that additional file formats which can be decompressed by the `gzip` and `gunzip` programs, such as those produced by compress and pack, are not supported by this module.
&emsp;&emsp;The module defines the following items:
&emsp;&emsp;`gzip.open(filename, mode='rb', compresslevel=9, encoding=None, errors=None, newline=None)`: Open a `gzip-compressed` file in `binary` or `text` mode, returning a file object. The `filename` argument can be an actual filename (a str or bytes object), or an existing file object to read from or write to.
&emsp;&emsp;The `mode` argument can be any of `r`, `rb`, `a`, `ab`, `w`, `wb`, `x` or `xb` for `binary` mode, or `rt`, `at`, `wt`, or `xt` for `text` mode. The default is `rb`.
&emsp;&emsp;The `compresslevel` argument is an integer from `0` to `9`, as for the `GzipFile` constructor.
&emsp;&emsp;`class gzip.GzipFile(filename=None, mode=None, compresslevel=9, fileobj=None, mtime=None)`: Constructor for the `GzipFile` class, which simulates most of the methods of a file object, with the exception of the `truncate()` method. At least one of `fileobj` and `filename` must be given a `non-trivial` value.
&emsp;&emsp;The new class instance is based on `fileobj`, which can be a regular file, an `io.BytesIO` object, or any other object which simulates a file. It defaults to `None`, in which case `filename` is opened to provide a file object.
&emsp;&emsp;When `fileobj` is not `None`, the `filename` argument is only used to be included in the `gzip` file header, which may include the original filename of the uncompressed file. It defaults to the filename of `fileobj`, if discernible; otherwise, it defaults to the empty string, and in this case the original filename is not included in the header.
&emsp;&emsp;The `mode` argument can be any of `r`, `rb`, `a`, `ab`, `w`, `wb`, `x`, or `xb`, depending on whether the file will be read or written. The default is the mode of `fileobj` if discernible; otherwise, the default is `rb`.
&emsp;&emsp;Note that the file is always opened in `binary` mode. To open a compressed file in `text` mode, use `open()` (or wrap your `GzipFile` with an `io.TextIOWrapper`).
&emsp;&emsp;The `compresslevel` argument is an integer from `0` to `9` controlling the level of compression; `1` is fastest and produces the least compression, and `9` is slowest and produces the most compression. `0` is no compression.
&emsp;&emsp;The `mtime` argument is an optional numeric timestamp to be written to the last modification time field in the stream when compressing. It should only be provided in compression mode. If omitted or `None`, the current time is used.
&emsp;&emsp;Calling a `GzipFile` object's `close()` method does not close `fileobj`, since you might wish to append more material after the compressed data. This also allows you to pass an `io.BytesIO` object opened for writing as `fileobj`, and retrieve the resulting memory buffer using the `io.BytesIO` object’s `getvalue()` method.
&emsp;&emsp;`GzipFile` supports the `io.BufferedIOBase` interface, including iteration and the with statement. Only the `truncate()` method isn’t implemented.
&emsp;&emsp;`GzipFile` also provides the following method and attribute:
&emsp;&emsp;`peek(n)`: Read `n` uncompressed bytes without advancing the file position. At most one single read on the compressed stream is done to satisfy the call. The number of bytes returned may be more or less than requested.
&emsp;&emsp;Note While calling `peek()` does not change the file position of the `GzipFile`, it may change the position of the underlying file object (e.g. if the `GzipFile` was constructed with the `fileobj` parameter).
&emsp;&emsp;`mtime`: When decompressing, the value of the last modification time field in the most recently read header may be read from this attribute, as an integer. The initial value before reading any headers is `None`.
&emsp;&emsp;All `gzip` compressed streams are required to contain this timestamp field. Some programs, such as `gunzip`, make use of the timestamp. The format is the same as the return value of `time.time()` and the `st_mtime` attribute of the object returned by `os.stat()`.
&emsp;&emsp;`gzip.compress(data, compresslevel=9)`: Compress the `data`, returning a bytes object containing the compressed data. `compresslevel` has the same meaning as in the `GzipFile` constructor above.
&emsp;&emsp;`gzip.decompress(data)`: Decompress the `data`, returning a bytes object containing the uncompressed data.

### Examples of usage

&emsp;&emsp;Example of how to read a compressed file:

``` python
import gzip
​
with gzip.open('/home/joe/file.txt.gz', 'rb') as f:
    file_content = f.read()
```

&emsp;&emsp;Example of how to create a compressed `GZIP` file:

``` python
import gzip
​
content = b"Lots of content here"
with gzip.open('/home/joe/file.txt.gz', 'wb') as f:
    f.write(content)
```

&emsp;&emsp;Example of how to `GZIP` compress an existing file:

``` python
import gzip
import shutil
​
with open('/home/joe/file.txt', 'rb') as f_in:
    with gzip.open('/home/joe/file.txt.gz', 'wb') as f_out:
        shutil.copyfileobj(f_in, f_out)
```

&emsp;&emsp;Example of how to `GZIP` compress a binary string:

``` python
import gzip
​
s_in = b"Lots of content here"
s_out = gzip.compress(s_in)
```