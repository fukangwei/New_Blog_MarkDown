---
title: 读写xml和yml文件
date: 2019-02-23 17:27:09
tags:
---
&emsp;&emsp;有时我们在处理完图像后，需要保存数据到文件上，以供下一步的处理。一个比较广泛的需求场景是：我们对一幅图像进行特征提取之后，需要把特征点信息保存到文件上，以供后面的机器学习分类操作。那么如果遇到这样的场景，我们有什么好方法？我想到的是把这些数据全写到文件上，如果下次需要这些数据时，就把它们从文件里读出来就好了。
&emsp;&emsp;其实更好的办法是使用`xml`和`yml`，因为它们更具有可读性，简直就是为保存数据结构而生。`OpenCV`提供了很好用的读写`xml`和`yml`的类，只要掌握其读写要领，很容易就可以实现这个小型数据库。
&emsp;&emsp;简单的数据写入示例：

``` cpp
#include <opencv2/highgui/highgui.hpp>

using namespace std;
using namespace cv;

typedef struct {
    int x;
    int y;
    string s;
} test_t;

int main ( int argc, char **argv ) {
    FileStorage fs ( "test.xml", FileStorage::WRITE );
    int a1 = 2;
    char a2 = -1;
    string str = "hello sysu!";
    int arr[10] = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };
    test_t t = { 3, 4, "hi sysu" };
    map<string, int> m;
    m["kobe"] = 100;
    m["james"] = 99;
    m["curry"] = 98;
    /* 写入文件操作，先写标注，再写数据 */
    fs << "int_data" << a1;
    fs << "char_data" << a2;
    fs << "string_data" << str;
    /* 写入数组 */
    fs << "array_data" << "["; /* 数组开始 */

    for ( int i = 0; i < 10; i++ ) {
        fs << arr[i];
    }

    fs << "]"; /* 数组结束 */
    /* 写入结构体 */
    fs << "struct_data" << "{"; /* 结构体开始 */
    fs << "x" << t.x;
    fs << "y" << t.y;
    fs << "s" << t.s;
    fs << "}"; /* 结构结束 */
    /* map的写入 */
    fs << "map_data" << "{"; /* map的开始写入 */
    map<string, int>::iterator it = m.begin();

    for ( ; it != m.end(); it++ ) {
        fs << it->first << it->second;
    }

    fs << "}"; /* map写入结束 */
    return 0;
}
```

打开`test.xml`文件，内容如下：

``` xml
<?xml version="1.0"?>
<opencv_storage>
<int_data>2</int_data>
<char_data>-1</char_data>
<string_data>"hello sysu!"</string_data>
<array_data>
  1 2 3 4 5 6 7 8 9 10</array_data>
<struct_data>
  <x>3</x>
  <y>4</y>
  <s>"hi sysu"</s></struct_data>
<map_data>
  <curry>98</curry>
  <james>99</james>
  <kobe>100</kobe></map_data>
</opencv_storage>
```

&emsp;&emsp;如果将文件存为`test.yml`：

``` cpp
FileStorage fs ( "test.yml", FileStorage::WRITE );
```

其内容如下：

``` yaml
%YAML:1.0
int_data: 2
char_data: -1
string_data: "hello sysu!"
array_data:
   - 1
   - 2
   - 3
   - 4
   - 5
   - 6
   - 7
   - 8
   - 9
   - 10
struct_data:
   x: 3
   y: 4
   s: hi sysu
map_data:
   curry: 98
   james: 99
   kobe: 100
```

其实还可以保存为`txt`或`doc`格式。显然`yml`文件的排版更加简洁明了，`xml`文件却有点冗余和杂乱了。

---

### XML/YAML file storages

&emsp;&emsp;You can store and then restore various `OpenCV` data structures to/from `XML` or `YAML` formats. Also, it is possible store and load arbitrarily complex data structures, which include `OpenCV` data structures, as well as primitive data types (integer and `floating-point` numbers and text strings) as their elements.
&emsp;&emsp;Use the following procedure to write something to `XML` or `YAML`:

1. Create new `FileStorage` and open it for writing. It can be done with a single call to `FileStorage::FileStorage()` constructor that takes a filename, or you can use the default constructor and then call `FileStorage::open()`. Format of the file (`XML` or `YAML`) is determined from the filename extension (`.xml` and `.yml`/`.yaml`, respectively)
2. Write all the data you want using the streaming operator `<<`, just like in the case of `STL` streams.
3. Close the file using `FileStorage::release()`. `FileStorage` destructor also closes the file.

&emsp;&emsp;Here is an example:

``` cpp
#include <opencv2/highgui/highgui.hpp>
#include <time.h>

using namespace cv;

int main ( int, char **argv ) {
    FileStorage fs ( "test.yml", FileStorage::WRITE );
    fs << "frameCount" << 5;
    time_t rawtime;
    time ( &rawtime );
    fs << "calibrationDate" << asctime ( localtime ( &rawtime ) );
    Mat cameraMatrix = ( Mat_<double> ( 3, 3 ) << 1000, 0, 320, 0, 1000, 240, 0, 0, 1 );
    Mat distCoeffs = ( Mat_<double> ( 5, 1 ) << 0.1, 0.01, -0.001, 0, 0 );
    fs << "cameraMatrix" << cameraMatrix << "distCoeffs" << distCoeffs;
    fs << "features" << "[";

    for ( int i = 0; i < 3; i++ ) {
        int x = rand() % 640;
        int y = rand() % 480;
        uchar lbp = rand() % 256;
        fs << "{:" << "x" << x << "y" << y << "lbp" << "[:";
        for ( int j = 0; j < 8; j++ ) {
            fs << ( ( lbp >> j ) & 1 );
        }
        fs << "]" << "}";
    }

    fs << "]";
    fs.release();
    return 0;
}
```

The sample above stores to `XML` and integer, text string (calibration date), `2` matrices, and a custom structure `feature`, which includes feature coordinates and `LBP` (`local binary pattern`) value. Here is output of the sample:

``` yaml
%YAML:1.0
frameCount: 5
calibrationDate: "Sun Dec  3 14:59:13 2017\n"
cameraMatrix: !!opencv-matrix
   rows: 3
   cols: 3
   dt: d
   data: [ 1000., 0., 320., 0., 1000., 240., 0., 0., 1. ]
distCoeffs: !!opencv-matrix
   rows: 5
   cols: 1
   dt: d
   data: [ 1.0000000000000001e-01, 1.0000000000000000e-02,
       -1.0000000000000000e-03, 0., 0. ]
features:
   - { x:103, y:166, lbp:[ 1, 0, 0, 1, 0, 1, 1, 0 ] }
   - { x:115, y:113, lbp:[ 1, 1, 1, 1, 1, 1, 1, 1 ] }
   - { x:586, y:12, lbp:[ 1, 0, 0, 1, 0, 1, 0, 0 ] }
```

&emsp;&emsp;To read the previously written `XML` or `YAML` file, do the following:

1. Open the file storage using `FileStorage::FileStorage()` constructor or `FileStorage::open()` method. In the current implementation the whole file is parsed and the whole representation of file storage is built in memory as a hierarchy of file nodes.
2. Read the data you are interested in. Use `FileStorage::operator []()`, `FileNode::operator []()` and/or `FileNodeIterator`.
3. Close the storage using `FileStorage::release()`.

&emsp;&emsp;Here is how to read the file created by the code sample above:

``` cpp
#include <opencv2/highgui/highgui.hpp>
#include "iostream"
#include <time.h>

using namespace std;
using namespace cv;

int main ( int, char **argv ) {
    FileStorage fs2 ( "test.yml", FileStorage::READ );
    /* first method: use (type) operator on FileNode. */
    int frameCount = ( int ) fs2["frameCount"];
    std::string date;
    /* second method: use FileNode::operator >> */
    fs2["calibrationDate"] >> date;
    Mat cameraMatrix2, distCoeffs2;
    fs2["cameraMatrix"] >> cameraMatrix2;
    fs2["distCoeffs"] >> distCoeffs2;
    cout << "frameCount: " << frameCount << endl
         << "calibration date: " << date << endl
         << "camera matrix: " << cameraMatrix2 << endl
         << "distortion coeffs: " << distCoeffs2 << endl;
    FileNode features = fs2["features"];
    FileNodeIterator it = features.begin(), it_end = features.end();
    int idx = 0;
    std::vector<uchar> lbpval;

    /* iterate through a sequence using FileNodeIterator */
    for ( ; it != it_end; ++it, idx++ ) {
        cout << "feature #" << idx << ": ";
        cout << "x=" << ( int ) ( *it ) ["x"] << ", y=" << ( int ) ( *it ) ["y"] << ", lbp: (";
        /* you can also easily read numerical arrays using FileNode >> std::vector operator. */
        ( *it ) ["lbp"] >> lbpval;

        for ( int i = 0; i < ( int ) lbpval.size(); i++ ) {
            cout << " " << ( int ) lbpval[i];
        }

        cout << ")" << endl;
    }

    fs2.release();
    return 0;
}
```

The result is:

``` yaml
frameCount: 5
calibration date: Sun Dec  3 14:59:13 2017
camera matrix: [1000, 0, 320;
  0, 1000, 240;
  0, 0, 1]
distortion coeffs: [0.1;
  0.01;
  -0.001;
  0;
  0]
feature #0: x=103, y=166, lbp: ( 1 0 0 1 0 1 1 0)
feature #1: x=115, y=113, lbp: ( 1 1 1 1 1 1 1 1)
feature #2: x=586, y=12, lbp: ( 1 0 0 1 0 1 0 0)
```

### class FileStorage

&emsp;&emsp;`XML/YAML` file storage class that encapsulates all the information necessary for writing or reading data to/from a file.

#### FileStorage::FileStorage

&emsp;&emsp;The constructors.

``` cpp
FileStorage::FileStorage();
FileStorage::FileStorage ( const string &source, int flags, const string &encoding = string() );
```

- `source`: Name of the file to open or the text string to read the data from. Extension of the file (`.xml` or `.yml`/`.yaml`) determines its format (`XML` or `YAML` respectively). Also you can append `.gz` to work with compressed files, for example `myHugeMatrix.xml.gz`. If both `FileStorage::WRITE` and `FileStorage::MEMORY` flags are specified, source is used just to specify the output file format (e.g. `mydata.xml`, `.yml` etc.).
- `flags`: Mode of operation. Possible values are:

1. `FileStorage::READ` Open the file for reading.
2. `FileStorage::WRITE` Open the file for writing.
3. `FileStorage::APPEND` Open the file for appending.
4. `FileStorage::MEMORY` Read data from source or write data to the internal buffer (which is returned by `FileStorage::release`).

- `encoding`: Encoding of the file. Note that `UTF-16` `XML` encoding is not supported currently and you should use `8-bit` encoding instead of it.

&emsp;&emsp;The full constructor opens the file. Alternatively you can use the default constructor and then call `FileStorage::open()`.

#### FileStorage::open

&emsp;&emsp;Opens a file.

``` cpp
bool FileStorage::open ( const string &filename, int flags, const string &encoding = string() );
```

- `filename`: Name of the file to open or the text string to read the data from. Extension of the file (`.xml` or `.yml`/`.yaml`) determines its format (`XML` or `YAML` respectively). Also you can append `.gz` to work with compressed files, for example `myHugeMatrix.xml.gz`. If both `FileStorage::WRITE` and `FileStorage::MEMORY` flags are specified, source is used just to specify the output file format (e.g. `mydata.xml`, `.yml` etc.).
- `flags`: Mode of operation.
- `encoding`: Encoding of the file. Note that `UTF-16` `XML` encoding is not supported currently and you should use `8-bit` encoding instead of it.

#### FileStorage::isOpened

&emsp;&emsp;Checks whether the file is opened.

``` cpp
bool FileStorage::isOpened() const;
```

Return `true` if the object is associated with the current file and `false` otherwise.
&emsp;&emsp;It is a good practice to call this method after you tried to open a file.

#### FileStorage::release

&emsp;&emsp;Closes the file and releases all the memory buffers.

``` cpp
void FileStorage::release();
```

Call this method after all `I/O` operations with the storage are finished.

#### FileStorage::releaseAndGetString

&emsp;&emsp;Closes the file and releases all the memory buffers.

``` cpp
string FileStorage::releaseAndGetString();
```

Call this method after all `I/O` operations with the storage are finished. If the storage was opened for writing data and `FileStorage::WRITE` was specified

#### FileStorage::getFirstTopLevelNode

&emsp;&emsp;Returns the first element of the `top-level` mapping.

``` cpp
FileNode FileStorage::getFirstTopLevelNode() const;
```

Return The first element of the `top-level` mapping.

#### FileStorage::root

&emsp;&emsp;Return the `top-level` mapping.

``` cpp
FileNode FileStorage::root ( int streamidx = 0 ) const;
```

- `streamidx`: Zero-based index of the stream. In most cases there is only one stream in the file. However, YAML supports multiple streams and so there can be several.

#### FileStorage::operator[]

&emsp;&emsp;Returns the specified element of the `top-level` mapping.

``` cpp
FileNode FileStorage::operator[] ( const string &nodename ) const;
FileNode FileStorage::operator[] ( const char *nodename ) const;
```

- `nodename`: Name of the file node.

#### FileStorage::operator*

&emsp;&emsp;Returns the obsolete `C` `FileStorage` structure.

``` cpp
CvFileStorage *FileStorage::operator*();
const CvFileStorage *FileStorage::operator*() const;
```

#### FileStorage::writeRaw

&emsp;&emsp;Writes multiple numbers.

``` cpp
void FileStorage::writeRaw ( const string &fmt, const uchar *vec, size_t len );
```

- `fmt`: Specification of each array element that has the following format (`[count]{'u'|'c'|'w'|'s'|'i'|'f'|'d'}`) where the characters correspond to fundamental `C++` types:

1. `u`: `8-bit` unsigned number.
2. `c`: `8-bit` signed number.
3. `w`: `16-bit` unsigned number.
4. `s`: `16-bit` signed number.
5. `i`: `32-bit` signed number.
6. `f`: single precision `floating-point` number.
7. `d`: double precision `floating-point` number.

&emsp;&emsp;`count` is the optional counter of values of a given type. For example, `2if` means that each array element is a structure of `2` integers, followed by a `single-precision` `floating-point` number. The equivalent notations of the above specification are `(space)iif(space)`, `(space)2i1f(space)` and so forth. Other examples: `u` means that the array consists of bytes, and `2d` means the array consists of pairs of doubles.

- `vec`: Pointer to the written array.
- `len`: Number of the uchar elements to write.

&emsp;&emsp;Writes one or more numbers of the specified format to the currently written structure. Usually it is more convenient to use `operator <<()` instead of this method.

#### FileStorage::writeObj

&emsp;&emsp;Writes the registered `C` structure (`CvMat`, `CvMatND`, `CvSeq`).

``` cpp
void FileStorage::writeObj ( const string &name, const void *obj );
```

- `name`: Name of the written object.
- `obj`: Pointer to the object.

#### FileStorage::getDefaultObjectName

&emsp;&emsp;Returns the normalized object name for the specified name of a file.

``` cpp
static string FileStorage::getDefaultObjectName ( const string &filename );
```

- `filename`: Name of a file.

#### operator <<

&emsp;&emsp;Writes data to a file storage.

``` cpp
template<typename _Tp> FileStorage &operator<< ( FileStorage &fs, const _Tp &value );
template<typename _Tp> FileStorage &operator<< ( FileStorage &fs, const vector<_Tp> &vec );
```

- `fs`: Opened file storage to write data.
- `value`: Value to be written to the file storage.
- `vec`: Vector of values to be written to the file storage.

&emsp;&emsp;It is the main function to write data to a file storage.

#### operator >>

&emsp;&emsp;Reads data from a file storage.

``` cpp
template<typename _Tp> void operator>> ( const FileNode &n, _Tp &value );
template<typename _Tp> void operator>> ( const FileNode &n, vector<_Tp> &vec );
template<typename _Tp> FileNodeIterator &operator>> ( FileNodeIterator &it, _Tp &value );
template<typename _Tp> FileNodeIterator &operator>> ( FileNodeIterator &it, vector<_Tp> &vec );
```

- `n`: Node from which data will be read.
- `it`: Iterator from which data will be read.
- `value`: Value to be read from the file storage.
- `vec`: Vector of values to be read from the file storage.

&emsp;&emsp;It is the main function to read data from a file storage.

### class FileNode

&emsp;&emsp;`File Storage Node` class. The node is used to store each and every element of the file storage opened for reading. When `XML/YAML` file is read, it is first parsed and stored in the memory as a hierarchical collection of nodes. Each node can be a `leaf` that is contain a single number or a string, or be a collection of other nodes. There can be named collections (`mappings`) where each element has a name and it is accessed by a name, and ordered collections (`sequences`) where elements do not have names but rather accessed by index. Type of the file node can be determined using `FileNode::type()` method.
&emsp;&emsp;Note that file nodes are only used for navigating file storages opened for reading. When a file storage is opened for writing, no data is stored in memory after it is written.

#### FileNode::FileNode

&emsp;&emsp;The constructors.

``` cpp
FileNode::FileNode();
FileNode::FileNode ( const CvFileStorage *fs, const CvFileNode *node );
FileNode::FileNode ( const FileNode &node );
```

- `fs`: Pointer to the obsolete file storage structure.
- `node`: File node to be used as initialization for the created file node.

&emsp;&emsp;These constructors are used to create a default file node, construct it from obsolete structures or from the another file node.

#### FileNode::operator[]

&emsp;&emsp;Returns element of a mapping node or a sequence node.

``` cpp
FileNode FileNode::operator[] ( const string &nodename ) const;
FileNode FileNode::operator[] ( const char *nodename ) const;
FileNode FileNode::operator[] ( int i ) const;
```

- `nodename`: Name of an element in the mapping node.
- `i`: Index of an element in the sequence node.

#### FileNode::type

&emsp;&emsp;Returns type of the node.

``` cpp
int FileNode::type() const;
```

Possible values are:

- `FileNode::NONE`: Empty node.
- `FileNode::INT`: Integer.
- `FileNode::REAL`: `Floating-point` number.
- `FileNode::FLOAT`: `Synonym` or `REAL`.
- `FileNode::STR`: Text string in `UTF-8` encoding.
- `FileNode::STRING`: Synonym for `STR`.
- `FileNode::REF`: Integer of type `size_t`. Typically used for storing complex dynamic structures where some elements reference the others.
- `FileNode::SEQ`: Sequence.
- `FileNode::MAP`: Mapping.
- `FileNode::FLOW`: Compact representation of a sequence or mapping. Used only by the `YAML` writer.
- `FileNode::USER`: Registered object (e.g. a matrix).
- `FileNode::EMPTY`: Empty structure (sequence or mapping).
- `FileNode::NAMED`: The node has a name (i.e. it is an element of a mapping).

#### FileNode::empty

&emsp;&emsp;Checks whether the node is empty.

``` cpp
bool FileNode::empty() const;
```

Return `true` if the node is empty.

#### FileNode::isNone

&emsp;&emsp;Checks whether the node is a `none` object.

``` cpp
bool FileNode::isNone() const;
```

Return `true` if the node is a `none` object.

#### FileNode::isSeq

&emsp;&emsp;Checks whether the node is a sequence.

``` cpp
bool FileNode::isSeq() const;
```

Return `true` if the node is a sequence.

#### FileNode::isMap

&emsp;&emsp;Checks whether the node is a mapping.

``` cpp
bool FileNode::isMap() const;
```

Return `true` if the node is a mapping.

#### FileNode::isInt

&emsp;&emsp;Checks whether the node is an integer.

``` cpp
bool FileNode::isInt() const;
```

Return `true` if the node is an integer.

#### FileNode::isReal

&emsp;&emsp;Checks whether the node is a `floating-point` number.

``` cpp
bool FileNode::isReal() const;
```

Return `true` if the node is a `floating-point` number.

#### FileNode::isString

&emsp;&emsp;Checks whether the node is a text string.

``` cpp
bool FileNode::isString() const;
```

Return `true` if the node is a text string.

#### FileNode::isNamed

&emsp;&emsp;Checks whether the node has a name.

``` cpp
bool FileNode::isNamed() const;
```

Return `true` if the node has a name.

#### FileNode::name

&emsp;&emsp;Returns the node name.

``` cpp
string FileNode::name() const;
```

Return the node name or an empty string if the node is nameless.

#### FileNode::size

&emsp;&emsp;Returns the number of elements in the node.

``` cpp
size_t FileNode::size() const;
```

Return the number of elements in the node, if it is a sequence or mapping, or `1` otherwise.

#### FileNode::operator int

&emsp;&emsp;Returns the node content as an integer.

``` cpp
FileNode::operator int() const;
```

Return the node content as an integer. If the node stores a `floating-point` number, it is rounded.

#### FileNode::operator float

&emsp;&emsp;Returns the node content as float.

``` cpp
FileNode::operator float() const;
```

#### FileNode::operator double

&emsp;&emsp;Returns the node content as double.

``` cpp
FileNode::operator double() const;
```

#### FileNode::operator string

&emsp;&emsp;Returns the node content as text string.

``` cpp
FileNode::operator string() const;
```

#### FileNode::operator*

&emsp;&emsp;Returns pointer to the underlying obsolete file node structure.

``` cpp
CvFileNode *FileNode::operator*();
```

#### FileNode::begin

&emsp;&emsp;Returns the iterator pointing to the first node element.

``` cpp
FileNodeIterator FileNode::begin() const;
```

#### FileNode::end

&emsp;&emsp;Returns the iterator pointing to the element following the last node element.

``` cpp
FileNodeIterator FileNode::end() const;
```

#### FileNode::readRaw

&emsp;&emsp;Reads node elements to the buffer with the specified format.

``` cpp
void FileNode::readRaw ( const string &fmt, uchar *vec, size_t len ) const;
```

- `fmt`: Specification of each array element. It has the same format as in `FileStorage::writeRaw()`.
- `vec`: Pointer to the destination array.
- `len`: Number of elements to read. If it is greater than number of remaining elements then all of them will be read.

Usually it is more convenient to use `operator >>()` instead of this method.

#### FileNode::readObj

&emsp;&emsp;Reads the registered object.

``` cpp
void *FileNode::readObj() const;
```

### class FileNodeIterator

&emsp;&emsp;The class `FileNodeIterator` is used to iterate through sequences and mappings. A standard `STL` notation, with `node.begin()`, `node.end()` denoting the beginning and the end of a sequence, stored in node.

#### FileNodeIterator::FileNodeIterator

&emsp;&emsp;The constructors.

``` cpp
FileNodeIterator::FileNodeIterator();
FileNodeIterator::FileNodeIterator ( const CvFileStorage *fs, const CvFileNode *node, size_t ofs = 0 );
FileNodeIterator::FileNodeIterator ( const FileNodeIterator &it );
```

- `fs`: File storage for the iterator.
- `node`: File node for the iterator.
- `ofs`: Index of the element in the node. The created iterator will point to this element.
- `it`: Iterator to be used as initialization for the created iterator.

These constructors are used to create a default iterator, set it to specific element in a file node or construct it from another iterator.

#### FileNodeIterator::operator*

&emsp;&emsp;Returns the currently observed element.

``` cpp
FileNode FileNodeIterator::operator*() const;
```

#### FileNodeIterator::operator->

&emsp;&emsp;Accesses methods of the currently observed element.

``` cpp
FileNode FileNodeIterator::operator->() const;
```

#### FileNodeIterator::operator ++

&emsp;&emsp;Moves iterator to the next node.

``` cpp
FileNodeIterator &FileNodeIterator::operator++();
FileNodeIterator FileNodeIterator::operator++ ( int None );
```

#### FileNodeIterator::operator --

&emsp;&emsp;Moves iterator to the previous node.

``` cpp
FileNodeIterator &FileNodeIterator::operator--();
FileNodeIterator FileNodeIterator::operator-- ( int None );
```

#### FileNodeIterator::operator +=

&emsp;&emsp;Moves iterator forward by the specified offset.

``` cpp
FileNodeIterator &FileNodeIterator::operator+= ( int ofs );
```

- `ofs`: Offset (possibly negative) to move the iterator.

#### FileNodeIterator::operator -=

&emsp;&emsp;Moves iterator backward by the specified offset (possibly negative).

``` cpp
FileNodeIterator &FileNodeIterator::operator-= ( int ofs );
```

- `ofs`: Offset (possibly negative) to move the iterator.

#### FileNodeIterator::readRaw

&emsp;&emsp;Reads node elements to the buffer with the specified format.

``` cpp
FileNodeIterator &FileNodeIterator::readRaw (
    const string &fmt, uchar *vec,
    size_t maxCount = ( size_t ) INT_MAX );
```

- `fmt`: Specification of each array element. It has the same format as in `FileStorage::writeRaw()`.
- `vec`: Pointer to the destination array.
- `maxCount`: Number of elements to read. If it is greater than number of remaining elements then all of them will be read.

Usually it is more convenient to use `operator >>()` instead of this method.