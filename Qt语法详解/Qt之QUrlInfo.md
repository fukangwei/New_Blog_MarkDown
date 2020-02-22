---
title: Qt之QUrlInfo
categories: Qt语法详解
date: 2019-01-23 13:52:53
---
&emsp;&emsp;The `QUrlInfo` class stores information about `URLs`. The header file is `QUrlInfo`.<!--more-->

### Public Functions

Return         | Function
---------------|---------
               | `QUrlInfo()`
               | `QUrlInfo(const QUrlInfo & ui)`
               | `QUrlInfo(const QString & name, int permissions, const QString & owner, const QString & group, qint64 size, const QDateTime & lastModified, const QDateTime & lastRead, bool isDir, bool isFile, bool isSymLink, bool isWritable, bool isReadable, bool isExecutable)`
               | `QUrlInfo(const QUrl & url, int permissions, const QString & owner, const QString & group, qint64 size, const QDateTime & lastModified, const QDateTime & lastRead, bool isDir, bool isFile, bool isSymLink, bool isWritable, bool isReadable, bool isExecutable)`
`virtual`      | `~QUrlInfo()`
`QString`      | `group() const`
`bool`         | `isDir() const`
`bool`         | `isExecutable() const`
`bool`         | `isFile() const`
`bool`         | `isReadable() const`
`bool`         | `isSymLink() const`
`bool`         | `isValid() const`
`bool`         | `isWritable() const`
`QDateTime`    | `lastModified() const`
`QDateTime`    | `lastRead() const`
`QString`      | `name() const`
`QString`      | `owner() const`
`int`          | `permissions() const`
`virtual void` | `setDir(bool b)`
`virtual void` | `setFile(bool b)`
`virtual void` | `setGroup(const QString & s)`
`virtual void` | `setLastModified(const QDateTime & dt)`
`void`         | `setLastRead(const QDateTime & dt)`
`virtual void` | `setName(const QString & name)`
`virtual void` | `setOwner(const QString & s)`
`virtual void` | `setPermissions(int p)`
`virtual void` | `setReadable(bool b)`
`virtual void` | `setSize(qint64 size)`
`virtual void` | `setSymLink(bool b)`
`virtual void` | `setWritable(bool b)`
`qint64`       | `size() const`
`bool`         | `operator!=(const QUrlInfo & other) const`
`QUrlInfo &`   | `operator=(const QUrlInfo & ui)`
`bool`         | `operator==(const QUrlInfo & other) const`

### Static Public Members

- `bool equal(const QUrlInfo & i1, const QUrlInfo & i2, int sortBy)`
- `bool greaterThan(const QUrlInfo & i1, const QUrlInfo & i2, int sortBy)`
- `bool lessThan(const QUrlInfo & i1, const QUrlInfo & i2, int sortBy)`

### Detailed Description

&emsp;&emsp;The `QUrlInfo` class stores information about `URLs`.
&emsp;&emsp;The information about a `URL` that can be retrieved includes `name()`, `permissions()`, `owner()`, `group()`, `size()`, `lastModified()`, `lastRead()`, `isDir()`, `isFile()`, `isSymLink()`, `isWritable()`, `isReadable()` and `isExecutable()`.
&emsp;&emsp;You can create your own `QUrlInfo` objects passing in all the relevant information in the constructor, and you can modify a `QUrlInfo`; for each getter mentioned above there is an equivalent setter. Note that setting values does not affect the underlying resource that the `QUrlInfo` provides information about; for example if you call `setWritable(true)` on a `read-only` resource the only thing changed is the `QUrlInfo` object, not the resource.

### Member Type Documentation

- enum `QUrlInfo::PermissionSpec`: This enum is used by the `permissions()` function to report the permissions of a file.

Constant               | Value   | Description
-----------------------|---------|------------
`QUrlInfo::ReadOwner`  | `00400` | The file is readable by the owner of the file.
`QUrlInfo::WriteOwner` | `00200` | The file is writable by the owner of the file.
`QUrlInfo::ExeOwner`   | `00100` | The file is executable by the owner of the file.
`QUrlInfo::ReadGroup`  | `00040` | The file is readable by the group.
`QUrlInfo::WriteGroup` | `00020` | The file is writable by the group.
`QUrlInfo::ExeGroup`   | `00010` | The file is executable by the group.
`QUrlInfo::ReadOther`  | `00004` | The file is readable by anyone.
`QUrlInfo::WriteOther` | `00002` | The file is writable by anyone.
`QUrlInfo::ExeOther`   | `00001` | The file is executable by anyone.

### Member Function Documentation

- `QUrlInfo::QUrlInfo()`: Constructs an invalid `QUrlInfo` object with default values.
- `QUrlInfo::QUrlInfo(const QUrlInfo & ui)`: Copy constructor, copies ui to this `URL` info object.
- `QUrlInfo::QUrlInfo(const QString & name, int permissions, const QString & owner, const QString & group, qint64 size, const QDateTime & lastModified, const QDateTime & lastRead, bool isDir, bool isFile, bool isSymLink, bool isWritable, bool isReadable, bool isExecutable)`: Constructs a `QUrlInfo` object by specifying all the `URL's` information. The information that is passed is the `name`, file `permissions`, `owner` and `group` and the file's `size`. Also passed is the `lastModified date/time` and the `lastRead date/time`. Flags are also passed, specifically, `isDir`, `isFile`, `isSymLink`, `isWritable`, `isReadable` and `isExecutable`.
- `QUrlInfo::QUrlInfo(const QUrl & url, int permissions, const QString & owner, const QString & group, qint64 size, const QDateTime & lastModified, const QDateTime & lastRead, bool isDir, bool isFile, bool isSymLink, bool isWritable, bool isReadable, bool isExecutable)`: Constructs a `QUrlInfo` object by specifying all the `URL's` information. The information that is passed is the `url`, file `permissions`, `owner` and `group` and the file's `size`. Also passed is the `lastModified date/time` and the `lastRead date/time`. Flags are also passed, specifically, `isDir`, `isFile`, `isSymLink`, `isWritable`, `isReadable` and `isExecutable`.
- `QUrlInfo::~QUrlInfo() [virtual]`: Destroys the `URL` info object.
- `bool QUrlInfo::equal(const QUrlInfo & i1, const QUrlInfo & i2, int sortBy) [static]`: Returns `true` if `i1` equals to `i2`; otherwise returns `false`. The objects are compared by the value, which is specified by `sortBy`. This must be one of `QDir::Name`, `QDir::Time` or `QDir::Size`.
- `bool QUrlInfo::greaterThan(const QUrlInfo & i1, const QUrlInfo & i2, int sortBy) [static]`: Returns `true` if `i1` is greater than `i2`; otherwise returns `false`. The objects are compared by the value, which is specified by `sortBy`. This must be one of `QDir::Name`, `QDir::Time` or `QDir::Size`.
- `QString QUrlInfo::group() const`: Returns the group of the `URL`.
- `bool QUrlInfo::isDir() const`: Returns `true` if the `URL` is a directory; otherwise returns `false`.
- `bool QUrlInfo::isExecutable() const`: Returns `true` if the `URL` is executable; otherwise returns false.
- `bool QUrlInfo::isFile() const`: Returns `true` if the `URL` is a file; otherwise returns `false`.
- `bool QUrlInfo::isReadable() const`: Returns `true` if the `URL` is readable; otherwise returns `false`.
- `bool QUrlInfo::isSymLink() const`: Returns `true` if the `URL` is a symbolic link; otherwise returns `false`.
- `bool QUrlInfo::isValid() const`: Returns `true` if the `URL` info is valid; otherwise returns `false`. Valid means that the `QUrlInfo` contains real information. You should always check if the `URL` info is valid before relying on the values.
- `bool QUrlInfo::isWritable() const`: Returns `true` if the `URL` is writable; otherwise returns `false`.
- `QDateTime QUrlInfo::lastModified() const`: Returns the last modification date of the `URL`.
- `QDateTime QUrlInfo::lastRead() const`: Returns the date when the `URL` was last read.
- `bool QUrlInfo::lessThan(const QUrlInfo & i1, const QUrlInfo & i2, int sortBy) [static]`: Returns `true` if `i1` is less than `i2`; otherwise returns `false`. The objects are compared by the value, which is specified by `sortBy`. This must be one of `QDir::Name`, `QDir::Time` or `QDir::Size`.
- `QString QUrlInfo::name() const`: Returns the file name of the `URL`.
- `QString QUrlInfo::owner() const`: Returns the owner of the `URL`.
- `int QUrlInfo::permissions() const`: Returns the permissions of the `URL`. You can use the `PermissionSpec` flags to test for certain permissions.
- `void QUrlInfo::setDir(bool b) [virtual]`: If `b` is `true` then the `URL` is set to be a directory; if `b` is `false` then the `URL` is set not to be a directory (which normally means it is a file). (Note that a `URL` can refer to both a file and a directory even though most file systems do not support this.) If you call this function for an invalid `URL` info, this function turns it into a valid one.
- `void QUrlInfo::setFile(bool b) [virtual]`: If `b` is `true` then the `URL` is set to be a file; if is false then the `URL` is set not to be a file (which normally means it is a directory). (Note that a `URL` can refer to both a file and a directory even though most file systems do not support this.) If you call this function for an invalid `URL` info, this function turns it into a valid one.
- `void QUrlInfo::setGroup(const QString & s) [virtual]`: Specifies that the owning group of the `URL` is called `s`. If you call this function for an invalid `URL` info, this function turns it into a valid one.
- `void QUrlInfo::setLastModified(const QDateTime & dt) [virtual]`: Specifies that the object the `URL` refers to was last modified at `dt`. If you call this function for an invalid `URL` info, this function turns it into a valid one.
- `void QUrlInfo::setLastRead(const QDateTime & dt)`: Specifies that the object the `URL` refers to was last read at `dt`. If you call this function for an invalid `URL` info, this function turns it into a valid one.
- `void QUrlInfo::setName(const QString & name) [virtual]`: Sets the name of the `URL` to `name`. The `name` is the full text, for example, `http://qt.nokia.com/doc/qurlinfo.html`. If you call this function for an invalid `URL` info, this function turns it into a valid one.
- `void QUrlInfo::setOwner(const QString & s) [virtual]`: Specifies that the owner of the `URL` is called `s`. If you call this function for an invalid `URL` info, this function turns it into a valid one.
- `void QUrlInfo::setPermissions(int p) [virtual]`: Specifies that the `URL` has access permissions `p`. If you call this function for an invalid `URL` info, this function turns it into a valid one.
- `void QUrlInfo::setReadable(bool b) [virtual]`: Specifies that the `URL` is readable if `b` is `true` and not readable if `b` is `false`. If you call this function for an invalid `URL` info, this function turns it into a valid one.
- `void QUrlInfo::setSize(qint64 size) [virtual]`: Specifies the `size` of the `URL`. If you call this function for an invalid `URL` info, this function turns it into a valid one.
- `void QUrlInfo::setSymLink(bool b) [virtual]`: Specifies that the `URL` refers to a symbolic link if `b` is `true` and that it does not if `b` is `false`. If you call this function for an invalid `URL` info, this function turns it into a valid one.
- `void QUrlInfo::setWritable(bool b) [virtual]`: Specifies that the `URL` is writable if `b` is `true` and not writable if `b` is `false`. If you call this function for an invalid `URL` info, this function turns it into a valid one.
- `qint64 QUrlInfo::size() const`: Returns the `size` of the `URL`.
- `bool QUrlInfo::operator!=(const QUrlInfo & other) const`: Returns `true` if this `QUrlInfo` is not equal to `other`; otherwise returns `false`.
- `QUrlInfo & QUrlInfo::operator=(const QUrlInfo & ui)`: Assigns the values of `ui` to this `QUrlInfo` object.
- `bool QUrlInfo::operator==(const QUrlInfo & other) const`: Returns `true` if this `QUrlInfo` is equal to `other`; otherwise returns `false`.