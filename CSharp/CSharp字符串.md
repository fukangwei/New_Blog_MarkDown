---
title: CSharp字符串
date: 2020-03-21 08:51:08
categories: C#
---
&emsp;&emsp;在`C#`中，你可以使用字符数组来表示字符串，但是，更常见的做法是使用`string`来声明一个字符串变量。`string`是`System.String`类的别名。<!--more-->

### 创建String对象

&emsp;&emsp;你可以使用以下方法之一来创建`string`对象：

- 使用`string`类型创建一个字符串。
- 使用`String`类构造函数。
- 使用字符串串联运算符，即`+`。
- 使用格式化方法来转换一个值或对象为它的字符串表示形式。

``` cs
using System;

namespace StringApplication {
    class Program {
        static void Main(string[] args) {
            string fname, lname;
            fname = "Rowan";
            lname = "Atkinson";

            string fullname = fname + lname; /* 字符串连接 */
            Console.WriteLine("Full Name: {0}", fullname);

            /* 使用string构造函数 */
            char[] letters = {'H', 'e', 'l', 'l', 'o'};
            string greetings = new string(letters);
            Console.WriteLine("Greetings: {0}", greetings);

            /* 返回字符串 */
            string[] sarray = {"Hello", "From", "Tutorials", "Point"};
            string message = String.Join(" ", sarray);
            Console.WriteLine("Message: {0}", message);

            /* 用于转化值的格式化方法 */
            DateTime waiting = new DateTime(2012, 10, 10, 17, 58, 1);
            string chat = String.Format("Message sent at {0:t} on {0:D}", waiting);
            Console.WriteLine("Message: {0}", chat);
        }
    }
}
```

执行结果：

``` cs
Full Name: RowanAtkinson
Greetings: Hello
Message: Hello From Tutorials Point
Message: Message sent at 17:58 on Wednesday, 10 October 2012
```

### String类的属性

&emsp;&emsp;`String`类有以下`2`个属性：

- `Chars`：在当前`String`对象中获取`Char`对象的指定位置。
- `Length`：在当前的`String`对象中获取字符数。

### String类的方法

&emsp;&emsp;`String`类有许多方法用于`string`对象的操作。下面是一些最常用的方法：

- `public static int Compare(string strA, string strB)`：比较两个指定的`string`对象，并返回一个表示它们在排列顺序中相对位置的整数。该方法区分大小写。
- `public static int Compare(string strA, string strB, bool ignoreCase)`：比较两个指定的`string`对象，并返回一个表示它们在排列顺序中相对位置的整数。但是，如果布尔参数为真时，该方法不区分大小写。
- `public static string Concat(string str0, string str1)`：连接两个`string`对象。
- `public static string Concat(string str0, string str1, string str2)`：连接三个`string`对象。
- `public static string Concat(string str0, string str1, string str2, string str3)`：连接四个`string`对象。
- `public bool Contains(string value)`：返回一个表示指定`string`对象是否出现在字符串中的值。
- `public static string Copy(string str)`：创建一个与指定字符串具有相同值的新的`String`对象。
- `public void CopyTo(int sourceIndex, char[] destination, int destinationIndex, int count)`：从`string`对象的指定位置开始复制指定数量的字符到`Unicode`字符数组中的指定位置。
- `public bool EndsWith(string value)`：判断`string`对象的结尾是否匹配指定的字符串。
- `public bool Equals(string value)`：判断当前的`string`对象是否与指定的`string`对象具有相同的值。
- `public static bool Equals(string a, string b)`：判断两个指定的`string`对象是否具有相同的值。
- `public static string Format(string format, Object arg0)`：把指定字符串中一个或多个格式项替换为指定对象的字符串表示形式。
- `public int IndexOf(char value)`：返回指定`Unicode`字符在当前字符串中第一次出现的索引，索引从`0`开始。
- `public int IndexOf(string value)`：返回指定字符串在该实例中第一次出现的索引，索引从`0`开始。
- `public int IndexOf(char value, int startIndex)`：返回指定`Unicode`字符从该字符串中指定字符位置开始搜索第一次出现的索引，索引从`0`开始。
- `public int IndexOf(string value, int startIndex)`：返回指定字符串从该实例中指定字符位置开始搜索第一次出现的索引，索引从`0`开始。
- `public int IndexOfAny(char[] anyOf)`：返回某一个指定的`Unicode`字符数组中任意字符在该实例中第一次出现的索引，索引从`0`开始。
- `public int IndexOfAny(char[] anyOf, int startIndex)`：返回某一个指定的`Unicode`字符数组中任意字符从该实例中指定字符位置开始搜索第一次出现的索引，索引从`0`开始。
- `public string Insert(int startIndex, string value)`：返回一个新的字符串，其中，指定的字符串被插入在当前`string`对象的指定索引位置。
- `public static bool IsNullOrEmpty(string value)`：指示指定的字符串是否为`null`或者是否为一个空的字符串。
- `public static string Join(string separator, string[] value)`：连接一个字符串数组中的所有元素，使用指定的分隔符分隔每个元素。
- `public static string Join(string separator, string[] value, int startIndex, int count)`：连接接一个字符串数组中的指定位置开始的指定元素，使用指定的分隔符分隔每个元素。
- `public int LastIndexOf(char value)`：返回指定`Unicode`字符在当前`string`对象中最后一次出现的索引位置，索引从`0`开始。
- `public int LastIndexOf(string value)`：返回指定字符串在当前`string`对象中最后一次出现的索引位置，索引从`0`开始。
- `public string Remove(int startIndex)`：移除当前实例中的所有字符，从指定位置开始，一直到最后一个位置为止，并返回字符串。
- `public string Remove(int startIndex, int count)`：从当前字符串的指定位置开始移除指定数量的字符，并返回字符串。
- `public string Replace(char oldChar, char newChar)`：把当前`string`对象中，所有指定的`Unicode`字符替换为另一个指定的`Unicode`字符，并返回新的字符串。
- `public string Replace(string oldValue, string newValue)`：把当前`string`对象中，所有指定的字符串替换为另一个指定的字符串，并返回新的字符串。
- `public string[] Split(params char[] separator)`：返回一个字符串数组，包含当前的`string`对象中的子字符串，子字符串是使用指定的`Unicode`字符数组中的元素进行分隔的。
- `public string[] Split(char[] separator, int count)`：返回一个字符串数组，包含当前的`string`对象中的子字符串，子字符串是使用指定的`Unicode`字符数组中的元素进行分隔的。`int`参数指定要返回的子字符串的最大数目。
- `public bool StartsWith(string value)`：判断字符串实例的开头是否匹配指定的字符串。
- `public char[] ToCharArray()`：返回一个带有当前`string`对象中所有字符的`Unicode`字符数组。
- `public char[] ToCharArray(int startIndex, int length)`：返回一个带有当前`string`对象中所有字符的`Unicode`字符数组，从指定的索引开始，直到指定的长度为止。
- `public string ToLower()`：把字符串转换为小写并返回。
- `public string ToUpper()`：把字符串转换为大写并返回。
- `public string Trim()`：移除当前`String`对象中的所有前导空白字符和后置空白字符。

&emsp;&emsp;比较字符串：

``` cs
using System;

namespace StringApplication {
    class StringProg {
        static void Main(string[] args) {
            string str1 = "This is test";
            string str2 = "This is text";

            if (String.Compare(str1, str2) == 0) {
                Console.WriteLine(str1 + " and " + str2 + " are equal.");
            } else {
                Console.WriteLine(str1 + " and " + str2 + " are not equal.");
            }
      }
   }
}
```

执行结果：

``` cs
This is test and This is text are not equal.
```

&emsp;&emsp;字符串包含字符串：

``` cs
using System;

namespace StringApplication {
    class StringProg {
        static void Main(string[] args) {
            string str = "This is test";

            if (str.Contains("test")) {
                Console.WriteLine("The sequence 'test' was found.");
            }
        }
    }
}
```

执行结果：

``` cs
The sequence 'test' was found.
```

&emsp;&emsp;获取子字符串：

``` cs
using System;

namespace StringApplication {
    class StringProg {
        static void Main(string[] args) {
            string str = "Last night I dreamt of San Pedro";
            Console.WriteLine(str);
            string substr = str.Substring(23);
            Console.WriteLine(substr);
        }
    }
}
```

执行结果：

``` cs
Last night I dreamt of San Pedro
San Pedro
```

&emsp;&emsp;连接字符串：

``` cs
using System;

namespace StringApplication {
    class StringProg {
        static void Main(string[] args) {
            string[] starray = new string[] {
                "Down the way nights are dark",
                "And the sun shines daily on the mountain top",
                "I took a trip on a sailing ship",
                "And when I reached Jamaica",
                "I made a stop"};

            string str = String.Join("\n", starray);
            Console.WriteLine(str);
        }
    }
}
```

执行结果：

``` cs
Down the way nights are dark
And the sun shines daily on the mountain top
I took a trip on a sailing ship
And when I reached Jamaica
I made a stop
```