---
title: CSharp文件操作
date: 2020-03-22 06:35:44
categories: C#
---
&emsp;&emsp;一个`文件`是一个存储在磁盘中带有指定名称和目录路径的数据集合。当打开文件进行读写时，它变成一个`流`。<!--more-->
&emsp;&emsp;从根本上说，流是通过通信路径传递的字节序列。有`2`个主要的流：

- 输入流用于从文件读取数据(读操作)。
- 输出流用于向文件写入数据(写操作)。

### I/O类

&emsp;&emsp;`System.IO`命名空间有各种不同的类，用于执行各种文件操作，如创建和删除文件、读取或写入文件，关闭文件等。下表列出了一些`System.IO`命名空间中常用的非抽象类：

I/O类            | 描述
-----------------|------
`BinaryReader`   | 从二进制流读取原始数据
`BinaryWriter`   | 以二进制格式写入原始数据
`BufferedStream` | 字节流的临时存储
`Directory`      | 有助于操作目录结构
`DirectoryInfo`  | 用于对目录执行操作
`DriveInfo`      | 提供驱动器的信息
`File`           | 有助于处理文件
`FileInfo`       | 用于对文件执行操作
`FileStream`     | 用于文件中任何位置的读写
`MemoryStream`   | 用于随机访问存储在内存中的数据流
`Path`           | 对路径信息执行操作
`StreamReader`   | 用于从字节流中读取字符
`StreamWriter`   | 用于向一个流中写入字符
`StringReader`   | 用于读取字符串缓冲区
`StringWriter`   | 用于写入字符串缓冲区

### FileStream类

&emsp;&emsp;`System.IO`命名空间中的`FileStream`类有助于文件的读写与关闭。该类派生自抽象类`Stream`。
&emsp;&emsp;你需要创建一个`FileStream`对象来创建一个新的文件，或打开一个已有的文件。创建`FileStream`对象的语法如下：

``` cs
FileStream <object_name> = new FileStream(
    <file_name>, <FileMode Enumerator>,
    <FileAccess Enumerator>, <FileShare Enumerator>
);
```

- `FileMode`枚举定义了各种打开文件的方法。`FileMode`枚举的成员有：

1. `Append`：打开一个已有的文件，并将光标放置在文件的末尾。如果文件不存在，则创建文件。
2. `Create`：创建一个新的文件。如果文件已存在，则删除旧文件，然后创建新文件。
3. `CreateNew`：指定操作系统应创建一个新的文件。如果文件已存在，则抛出异常。
4. `Open`：打开一个已有的文件。如果文件不存在，则抛出异常。
5. `OpenOrCreate`：指定操作系统应打开一个已有的文件。如果文件不存在，则用指定的名称创建一个新的文件打开。
6. `Truncate`：打开一个已有的文件，文件一旦打开，就将被截断为零字节大小。然后我们可以向文件写入全新的数据，但是保留文件的初始创建日期。如果文件不存在，则抛出异常。

- `FileAccess`枚举的成员有`Read`、`ReadWrite`和`Write`。
- `FileShare`枚举的成员有：

1. `Inheritable`：允许文件句柄可由子进程继承。`Win32`不直接支持此功能。
2. `None`：谢绝共享当前文件。文件关闭前，打开该文件的任何请求(由此进程或另一进程发出的请求)都将失败。
3. `Read`：允许随后打开文件读取。如果未指定此标志，则文件关闭前，任何打开该文件以进行读取的请求(由此进程或另一进程发出的请求)都将失败。但是，即使指定了此标志，仍可能需要附加权限才能够访问该文件。
4. `ReadWrite`：允许随后打开文件读取或写入。如果未指定此标志，则文件关闭前，任何打开该文件以进行读取或写入的请求(由此进程或另一进程发出)都将失败。但是，即使指定了此标志，仍可能需要附加权限才能够访问该文件。
5. `Write`：允许随后打开文件写入。如果未指定此标志，则文件关闭前，任何打开该文件以进行写入的请求(由此进程或另一进过程发出的请求)都将失败。但是，即使指定了此标志，仍可能需要附加权限才能够访问该文件。
6. `Delete`：允许随后删除文件。

&emsp;&emsp;例如，创建一个`FileStream`对象`F`来读取名为`sample.txt`的文件：

``` cs
FileStream F = new FileStream("sample.txt", FileMode.Open, FileAccess.Read, FileShare.Read);
```

&emsp;&emsp;下面的程序演示了`FileStream`类的用法：

``` cs
using System;
using System.IO;

namespace FileIOApplication {
    class Program {
        static void Main(string[] args) {
            FileStream F = new FileStream("test.dat", \
                FileMode.OpenOrCreate, FileAccess.ReadWrite);

            for (int i = 1; i <= 20; i++) {
                F.WriteByte((byte)i);
            }

            F.Position = 0;

            for (int i = 0; i <= 20; i++) {
                Console.Write(F.ReadByte() + " ");
            }

            F.Close();
        }
    }
}
```

执行结果：

``` cs
1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20 -1
```

### 文本文件的读写

&emsp;&emsp;`StreamReader`和`StreamWriter`类用于文本文件的数据读写。这些类从抽象基类`Stream`继承，`Stream`支持文件流的字节读写。

#### StreamReader类

&emsp;&emsp;`StreamReader`类继承自抽象基类`TextReader`，表示阅读器读取一系列字符。下面列出了`StreamReader`类中一些常用的方法：

- `public override void Close()`：关闭`StreamReader`对象和基础流，并释放任何与读者相关的系统资源。
- `public override int Peek()`：返回下一个可用的字符，但不使用它。
- `public override int Read()`：从输入流中读取下一个字符，并把字符位置往前移一个字符。

&emsp;&emsp;下面的实例演示了读取名为`Jamaica.txt`的文件。文件内容如下：

``` cs
Down the way where the nights are gay
And the sun shines daily on the mountain top
I took a trip on a sailing ship
And when I reached Jamaica
I made a stop
```

代码如下：

``` cs
using System;
using System.IO;

namespace FileApplication {
    class Program {
        static void Main(string[] args) {
            try {
                /* using语句也能关闭StreamReader */
                using (StreamReader sr = new StreamReader("./jamaica.txt")) {
                    string line;

                    /* 从文件读取并显示行，直到文件的末尾 */
                    while ((line = sr.ReadLine()) != null) {
                        Console.WriteLine(line);
                    }
                }
            } catch (Exception e) { /* 向用户显示出错消息 */
                Console.WriteLine("The file could not be read:");
                Console.WriteLine(e.Message);
            }
        }
    }
}
```

当你编译和执行上面的程序时，它会显示文件的内容。

#### StreamWriter类

&emsp;&emsp;`StreamWriter`类继承自抽象类`TextWriter`，表示编写器写入一系列字符。下面列出了`StreamWriter`类中一些常用的方法：

- `public override void Close()`：关闭当前的`StreamWriter`对象和基础流。
- `public override void Flush()`：清理当前编写器的所有缓冲区，使得所有缓冲数据写入基础流。
- `public virtual void Write(bool value)`：把一个布尔值的文本表示形式写入到文本字符串或流。
- `public override void Write(char value)`：把一个字符写入到流。
- `public virtual void Write(decimal value)`：把一个十进制值的文本表示形式写入到文本字符串或流。
- `public virtual void Write(double value)`：把一个`8`字节浮点值的文本表示形式写入到文本字符串或流。
- `public virtual void Write(int value)`：把一个`4`字节有符号整数的文本表示形式写入到文本字符串或流。
- `public override void Write(string value)`：把一个字符串写入到流。
- `public virtual void WriteLine()`：把行结束符写入到文本字符串或流。

``` cs
using System;
using System.IO;

namespace FileApplication {
    class Program {
        static void Main(string[] args) {
            string[] names = new string[] {"Zara Ali", "Nuha Ali"};

            using (StreamWriter sw = new StreamWriter("names.txt")) {
                foreach (string s in names) {
                    sw.WriteLine(s);
                }
            }

            /* 从文件中读取并显示每行 */
            string line = "";

            using (StreamReader sr = new StreamReader("names.txt")) {
                while ((line = sr.ReadLine()) != null) {
                    Console.WriteLine(line);
                }
            }
        }
    }
}
```

执行结果：

``` cs
Zara Ali
Nuha Ali
```

### 二进制文件的读写

&emsp;&emsp;`BinaryReader`和`BinaryWriter`类用于二进制文件的读写。

#### BinaryReader类

&emsp;&emsp;`BinaryReader`类用于从文件读取二进制数据。一个`BinaryReader`对象通过向它的构造函数传递`FileStream`对象而被创建。下面列出了`BinaryReader`类中一些常用的方法：

- `public override void Close()`：关闭`BinaryReader`对象和基础流。
- `public virtual int Read()`：从基础流中读取字符，并把流的当前位置往前移。
- `public virtual bool ReadBoolean()`：从当前流中读取一个布尔值，并把流的当前位置往前移一个字节。
- `public virtual byte ReadByte()`：从当前流中读取下一个字节，并把流的当前位置往前移一个字节。
- `public virtual byte[] ReadBytes(int count)`：从当前流中读取指定数目的字节到一个字节数组中，并把流的当前位置往前移指定数目的字节。
- `public virtual char ReadChar()`：从当前流中读取下一个字节，并把流的当前位置按照所使用的编码和从流中读取的指定的字符往前移。
- `public virtual char[] ReadChars(int count)`：从当前流中读取指定数目的字节，在一个字符数组中返回数组，并把流的当前位置按照所使用的编码和从流中读取的指定的字符往前移。
- `public virtual double ReadDouble()`：从当前流中读取一个`8`字节浮点值，并把流的当前位置往前移`8`个字节。
- `public virtual int ReadInt32()`：从当前流中读取一个`4`字节有符号整数，并把流的当前位置往前移`4`个字节。
- `public virtual string ReadString()`：从当前流中读取一个字符串。字符串以长度作为前缀，同时编码为一个`7`位的整数。

#### BinaryWriter类

&emsp;&emsp;`BinaryWriter`类用于向文件写入二进制数据。一个`BinaryWriter`对象通过向它的构造函数传递`FileStream`对象而被创建。下表列出了`BinaryWriter`类中一些常用的方法：

- `public override void Close()`：关闭`BinaryWriter`对象和基础流。
- `public virtual void Flush()`：清理当前编写器的所有缓冲区，使得所有缓冲数据写入基础设备。
- `public virtual long Seek(int offset, SeekOrigin origin)`：设置当前流内的位置。
- `public virtual void Write(bool value)`：把一个单字节的布尔值写入到当前流中，`0`表示`false`，`1`表示`true`。
- `public virtual void Write(byte value)`：把一个无符号字节写入到当前流中，并把流的位置往前移一个字节。
- `public virtual void Write(byte[] buffer)`：把一个字节数组写入到基础流中。
- `public virtual void Write(char ch)`：把一个`Unicode`字符写入到当前流中，并把流的当前位置按照所使用的编码和要写入到流中的指定的字符往前移。
- `public virtual void Write(char[] chars)`：把一个字符数组写入到当前流中，并把流的当前位置按照所使用的编码和要写入到流中的指定的字符往前移。
- `public virtual void Write(double value)`：把一个`8`字节浮点值写入到当前流中，并把流位置往前移`8`个字节。
- `public virtual void Write(int value)`：把一个`4`字节有符号整数写入到当前流中，并把流位置往前移`4`个字节。
- `public virtual void Write(string value)`：把一个以长度为前缀的字符串写入到`BinaryWriter`的当前编码的流中，并把流的当前位置按照所使用的编码和要写入到流中的指定的字符往前移。

``` cs
using System;
using System.IO;

namespace BinaryFileApplication {
    class Program {
        static void Main(string[] args) {
            BinaryWriter bw;
            BinaryReader br;
            int i = 25;
            double d = 3.14157;
            bool b = true;
            string s = "I am happy";

            try { /* 创建文件 */
                bw = new BinaryWriter(new FileStream("mydata",
                FileMode.Create));
            } catch (IOException e) {
                Console.WriteLine(e.Message + "\n Cannot create file.");
                return;
            }

            try { /* 写入文件 */
                bw.Write(i);
                bw.Write(d);
                bw.Write(b);
                bw.Write(s);
            } catch (IOException e) {
                Console.WriteLine(e.Message + "\n Cannot write to file.");
                return;
            }

            bw.Close();

            try { /* 读取文件 */
                br = new BinaryReader(new FileStream("mydata",
                FileMode.Open));
            } catch (IOException e) {
                Console.WriteLine(e.Message + "\n Cannot open file.");
                return;
            }

            try {
                i = br.ReadInt32();
                Console.WriteLine("Integer data: {0}", i);
                d = br.ReadDouble();
                Console.WriteLine("Double data: {0}", d);
                b = br.ReadBoolean();
                Console.WriteLine("Boolean data: {0}", b);
                s = br.ReadString();
                Console.WriteLine("String data: {0}", s);
            } catch (IOException e) {
                Console.WriteLine(e.Message + "\n Cannot read from file.");
                return;
            }

            br.Close();
        }
    }
}
```

执行结果：

``` cs
Integer data: 25
Double data: 3.14157
Boolean data: True
String data: I am happy
```

### Windows文件系统的操作

&emsp;&emsp;`C#`允许你使用各种目录和文件相关的类来操作目录和文件，比如`DirectoryInfo`类和`FileInfo`类。

#### DirectoryInfo类

&emsp;&emsp;`DirectoryInfo`类派生自`FileSystemInfo`类，它提供了各种用于创建、移动、浏览目录和子目录的方法。注意，该类不能被继承。下面列出了`DirectoryInfo`类中一些常用的属性：

- `Attributes`：获取当前文件或目录的属性。
- `CreationTime`：获取当前文件或目录的创建时间。
- `Exists`：获取一个表示目录是否存在的布尔值。
- `Extension`：获取表示文件存在的字符串。
- `FullName`：获取目录或文件的完整路径。
- `LastAccessTime`：获取当前文件或目录最后被访问的时间。
- `Name`：获取该`DirectoryInfo`实例的名称。

&emsp;&emsp;下面列出了`DirectoryInfo`类中一些常用的方法：

- `public void Create()`：创建一个目录。
- `public DirectoryInfo CreateSubdirectory(string path)`：在指定的路径上创建子目录。指定的路径可以是相对于`DirectoryInfo`类的实例的路径。
- `public override void Delete()`：如果为空的，则删除该`DirectoryInfo`。
- `public DirectoryInfo[] GetDirectories()`：返回当前目录的子目录。
- `public FileInfo[] GetFiles()`：从当前目录返回文件列表。

#### FileInfo类

&emsp;&emsp;`FileInfo`类派生自`FileSystemInfo`类。它提供了用于创建、复制、删除、移动、打开文件的属性和方法，且有助于`FileStream`对象的创建。注意，该类不能被继承。下面列出了`FileInfo`类中一些常用的属性：

- `Attributes`：获取当前文件的属性。
- `CreationTime`：获取当前文件的创建时间。
- `Directory`：获取文件所属目录的一个实例。
- `Exists`：获取一个表示文件是否存在的布尔值。
- `Extension`：获取表示文件存在的字符串。
- `FullName`：获取文件的完整路径。
- `LastAccessTime`：获取当前文件最后被访问的时间。
- `LastWriteTime`：获取文件最后被写入的时间。
- `Length`：获取当前文件的大小，以字节为单位。
- `Name`：获取文件的名称。

&emsp;&emsp;下面列出了`FileInfo`类中一些常用的方法：

- `public StreamWriter AppendText()`：创建一个`StreamWriter`，追加文本到由`FileInfo`的实例表示的文件中。
- `public FileStream Create()`：创建一个文件。
- `public override void Delete()`：永久删除一个文件。
- `public void MoveTo(string destFileName)`：移动一个指定的文件到一个新的位置，提供选项来指定新的文件名。
- `public FileStream Open(FileMode mode)`：以指定的模式打开一个文件。
- `public FileStream Open(FileMode mode, FileAccess access)`：以指定的模式，使用`read`、`write`或`read/write`访问，来打开一个文件。
- `public FileStream Open(FileMode mode, FileAccess access, FileShare share)`：以指定的模式，使用`read`、`write`或`read/write`访问，以及指定的分享选项，来打开一个文件。
- `public FileStream OpenRead()`：创建一个只读的`FileStream`。
- `public FileStream OpenWrite()`：创建一个只写的`FileStream`。

``` cs
using System;
using System.IO;

namespace WindowsFileApplication {
    class Program {
        static void Main(string[] args) {
            DirectoryInfo mydir = new DirectoryInfo(@"c:\Windows");

            /* 获取目录中的文件以及它们的名称和大小 */
            FileInfo[] f = mydir.GetFiles();
            foreach (FileInfo file in f) {
                Console.WriteLine("File Name: {0} Size: {1}", \
                    file.Name, file.Length);
            }
        }
    }
}
```

当你编译和执行上面的程序时，它会显示文件的名称及它们在`Windows`目录中的大小。