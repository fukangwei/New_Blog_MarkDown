---
title: String类
date: 2018-12-24 09:23:07
categories: Java
---
&emsp;&emsp;在`Java`中，字符串属于对象，`Java`提供了`String`类来创建和操作字符串。创建字符串最简单的方式如下：

``` java
String greeting = "菜鸟教程";
```

在代码中遇到字符串常量时，编译器会使用该值创建一个`String`对象。和其它对象一样，可以使用关键字和构造方法来创建`String`对象：

``` java
public class StringDemo {
    public static void main(String args[]) {
        char[] helloArray = {'r', 'u', 'n', 'o', 'o', 'b'};
        String helloString = new String(helloArray);
        System.out.println(helloString); /* 输出“runoob” */
    }
}
```

注意，`String`类是不可改变的，所以你一旦创建了`String`对象，那它的值就无法改变了。

### 字符串长度

&emsp;&emsp;用于获取有关对象的信息的方法称为访问器方法。`String`类的一个访问器方法是`length`方法，它返回字符串对象包含的字符数：

``` java
public class StringDemo {
    public static void main(String args[]) {
        String site = "www.runoob.com";
        int len = site.length();
        /* 输出“菜鸟教程网址长度：14” */
        System.out.println("菜鸟教程网址长度：" + len);
    }
}
```

### 连接字符串

&emsp;&emsp;`String`类提供了连接两个字符串的方法：

``` java
string1.concat(string2);
```

该方法返回`string2`连接`string1`的新字符串。也可以对字符串常量使用`concat`方法：

``` java
"我的名字是 ".concat("Runoob");
```

更常用的是使用`+`操作符来连接字符串：

``` java
"Hello," + " runoob" + "!"
```

代码如下所示：

``` java
public class StringDemo {
    public static void main(String args[]) {
        String string1 = "菜鸟教程网址：";
        /* 输出“这是菜鸟教程网址：www.runoob.com” */
        System.out.println("这是" + string1 + "www.runoob.com");
    }
}
```

### 创建格式化字符串

&emsp;&emsp;我们知道输出格式化数字可以使用`printf`和`format`方法。`String`类使用静态方法`format`返回一个`String`对象，而不是`PrintStream`对象。`format`能用来创建可复用的格式化字符串，而不仅仅是用于一次打印输出：

``` java
System.out.printf("浮点型变量的值为 " + "%f, 整型变量的值为 " +
    " %d, 字符串变量的值为 " + " %s", floatVar, intVar, stringVar);

String fs;
fs = String.format("浮点型变量的值为 " +"%f, 整型变量的值为 " +
    " %d, 字符串变量的值为 " + " %s", floatVar, intVar, stringVar);
```

### String方法

&emsp;&emsp;下面是`String`类支持的方法：

- `char charAt(int index)`：返回指定索引处的`char`值。
- `int compareTo(Object o)`：把这个字符串和另一个对象比较。
- `int compareTo(String anotherString)`：按字典顺序比较两个字符串。
- `int compareToIgnoreCase(String str)`：按字典顺序比较两个字符串，不考虑大小写。
- `String concat(String str)`：将指定字符串连接到此字符串的结尾。
- `boolean contentEquals(StringBuffer sb)`：当且仅当字符串与指定的`StringBuffer`有相同顺序的字符时返回真。
- `static String copyValueOf(char[] data)`：返回指定数组中表示该字符序列的`String`。
- `static String copyValueOf(char[] data, int offset, int count)`：返回指定数组中表示该字符序列的`String`。
- `boolean endsWith(String suffix)`：测试此字符串是否以指定的后缀结束。
- `boolean equals(Object anObject)`：将此字符串与指定的对象比较。
- `boolean equalsIgnoreCase(String anotherString)`：将此`String`与另一个`String`比较，不考虑大小写。
- `byte[] getBytes()`：使用平台的默认字符集将此`String`编码为`byte`序列，并将结果存储到一个新的`byte`数组中。
- `byte[] getBytes(String charsetName)`：使用指定的字符集将此`String`编码为`byte`序列，并将结果存储到一个新的`byte`数组中。
- `void getChars(int srcBegin, int srcEnd, char[] dst, int dstBegin)`：将字符从此字符串复制到目标字符数组。
- `int hashCode()`：返回此字符串的哈希码。
- `int indexOf(int ch)`：返回指定字符在此字符串中第一次出现处的索引。
- `int indexOf(int ch, int fromIndex)`：返回在此字符串中第一次出现指定字符处的索引，从指定的索引开始搜索。
- `int indexOf(String str)`：返回指定子字符串在此字符串中第一次出现处的索引。
- `int indexOf(String str, int fromIndex)`：返回指定子字符串在此字符串中第一次出现处的索引，从指定的索引开始。
- `String intern()`：返回字符串对象的规范化表示形式。
- `int lastIndexOf(int ch)`：返回指定字符在此字符串中最后一次出现处的索引。
- `int lastIndexOf(int ch, int fromIndex)`：返回指定字符在此字符串中最后一次出现处的索引，从指定的索引处开始进行反向搜索。
- `int lastIndexOf(String str)`：返回指定子字符串在此字符串中最右边出现处的索引。
- `int lastIndexOf(String str, int fromIndex)`：返回指定子字符串在此字符串中最后一次出现处的索引，从指定的索引开始反向搜索。
- `int length()`：返回此字符串的长度。
- `boolean matches(String regex)`：告知此字符串是否匹配给定的正则表达式。
- `boolean regionMatches(boolean ignoreCase, int toffset, String other, int ooffset, int len)`：测试两个字符串区域是否相等。
- `boolean regionMatches(int toffset, String other, int ooffset, int len)`：测试两个字符串区域是否相等。
- `String replace(char oldChar, char newChar)`：返回一个新的字符串，它是通过用`newChar`替换此字符串中出现的所有`oldChar`得到的。
- `String replaceAll(String regex, String replacement)`：使用给定的`replacement`替换此字符串所有匹配给定的正则表达式的子字符串。
- `String replaceFirst(String regex, String replacement)`：使用给定的`replacement`替换此字符串匹配给定的正则表达式的第一个子字符串。
- `String[] split(String regex)`：根据给定正则表达式的匹配拆分此字符串。
- `String[] split(String regex, int limit)`：根据给定正则表达式的匹配拆分此字符串。
- `boolean startsWith(String prefix)`：测试此字符串是否以指定的前缀开始。
- `boolean startsWith(String prefix, int toffset)`：测试此字符串从指定索引开始的子字符串是否以指定前缀开始。
- `CharSequence subSequence(int beginIndex, int endIndex)`：返回一个新的字符序列，它是此序列的一个子序列。
- `String substring(int beginIndex)`：返回一个新的字符串，它是此字符串的一个子字符串。
- `String substring(int beginIndex, int endIndex)`：返回一个新字符串，它是此字符串的一个子字符串。
- `char[] toCharArray()`：将此字符串转换为一个新的字符数组。
- `String toLowerCase()`：使用默认语言环境的规则将此`String`中的所有字符都转换为小写。
- `String toLowerCase(Locale locale)`：使用给定`Locale`的规则将此`String`中的所有字符都转换为小写。
- `String toString()`：返回此对象本身(它已经是一个字符串)。
- `String toUpperCase()`：使用默认语言环境的规则将此`String`中的所有字符都转换为大写。
- `String toUpperCase(Locale locale)`：使用给定`Locale`的规则将此`String`中的所有字符都转换为大写。
- `String trim()`：返回字符串的副本，忽略前导空白和尾部空白。
- `static String valueOf(primitive data type x)`：返回给定`data type`类型`x`参数的字符串表示形式。

### 字符串与其它类型间的转换

&emsp;&emsp;其它类型向字符串的转换：

- 调用类的串转换方法：`X.toString();`
- 自动转换：`X + "";`
- 使用`String`的方法：`String.valueOf(X);`

&emsp;&emsp;字符串作为值，向其它类型的转换：
&emsp;&emsp;1. 先转换成相应的封装器实例，再调用对应的方法转换成其它类型。例如字符`32.1`转换`double`类型的值：

``` java
new Float("32.1").doubleValue(); /* 第一种方式 */
Double.valueOf("32.1").doubleValue(); /* 第二种方式 */
```

&emsp;&emsp;2. 静态`parseXXX`方法：

``` java
String s = "1";
byte b = Byte.parseByte(s);
short t = Short.parseShort(s);
int i = Integer.parseInt(s);
long l = Long.parseLong(s);
Float f = Float.parseFloat(s);
Double d = Double.parseDouble(s);
```

---

&emsp;&emsp;`String`类是不可改变的解析：

``` java
String s = "Google";
System.out.println("s = " + s);
s = "Runoob";
System.out.println("s = " + s);
```

执行结果：

``` bash
s = Google
s = Runoob
```

从结果上看是改变了，但为什么说`String`对象是不可变的呢？原因在于实例中的`s`只是一个`String`对象的引用，并不是对象本身。当执行`s = "Runoob";`时，创建了一个新的对象`Runoob`，而原来的`Google`还存在于内存中。

---

### java.lang.String.contains

&emsp;&emsp;函数原型如下：

``` java
public boolean contains(CharSequence s);
```

当字符串包含`CharSequence`字符序列时，返回`true`，否则返回`false`。如果`s`为`null`，则触发`NullPointerException`异常。

``` java
public class test {
    public static void main(String[] args) {
        String str1 = "tutorials point", str2 = "http://";
        CharSequence cs1 = "int";
        /* string contains the specified sequence of char values */
        boolean retval = str1.contains(cs1);
        System.out.println("Method returns : " + retval);
        /* string does not contain the specified sequence of char value */
        retval = str2.contains("_");
        System.out.println("Methods returns : " + retval);
    }
}
```

执行结果：

``` bash
Method returns : true
Methods returns : false
```