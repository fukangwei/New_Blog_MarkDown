---
title: Character类
categories: Java
abbrlink: 9dc74a77
date: 2018-12-24 11:48:17
---
&emsp;&emsp;`Character`类用于对单个字符进行操作，它在对象中包装一个基本类型`char`的值：

``` java
char ch = 'a';
char uniChar = '\u039A'; /* Unicode字符表示形式 */
char[] charArray = {'a', 'b', 'c', 'd', 'e'}; /* 字符数组 */
```

`Character`类提供了一系列方法来操纵字符，你可以使用`Character`的构造方法创建一个`Character`类对象：

``` java
Character ch = new Character('a');
```

在某些情况下，`Java`编译器会自动创建一个`Character`对象。例如将一个`char`类型的参数传递给需要一个`Character`类型参数的方法时，那么编译器会自动地将`char`类型参数转换为`Character`对象。这种特征称为`装箱`，反过来称为`拆箱`。

``` java
Character ch = 'a'; /* 原始字符“a”装箱到Character对象ch中 */
char c = test('x'); /* 原始字符“x”用test方法装箱，返回拆箱的值到c */
```

### 转义序列

&emsp;&emsp;前面有反斜杠(`\`)的字符代表转义字符，它对编译器来说是有特殊含义的。下面列表展示了`Java`的转义序列：

转义字符 | 说明
--------|-----
`\t`    | 在文中该处插入一个`tab`键
`\b`    | 在文中该处插入一个后退键
`\n`    | 在文中该处换行
`\r`    | 在文中该处插入回车
`\f`    | 在文中该处插入换页符
`\'`    | 在文中该处插入单引号
`\"`    | 在文中该处插入双引号
`\\`    | 在文中该处插入反斜杠

当打印语句遇到一个转义序列时，编译器可以正确地对其进行解释：

``` java
public class Test {
    public static void main(String args[]) {
        /* 输出“访问"菜鸟教程!"” */
        System.out.println("访问\"菜鸟教程!\"");
    }
}
```

### Character方法

&emsp;&emsp;下面是`Character`类的方法：

方法           | 说明
---------------|-----
`isLetter`     | 是否为一个字母
`isDigit`      | 是否为一个数字字符
`isWhitespace` | 是否为一个空格
`isUpperCase`  | 是否为大写字母
`isLowerCase`  | 是否为小写字母
`toUpperCase`  | 指定字母的大写形式
`toLowerCase`  | 指定字母的小写形式
`toString`     | 返回字符的字符串形式，字符串的长度仅为`1`