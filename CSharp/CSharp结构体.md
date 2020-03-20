---
title: CSharp结构体
date: 2020-03-21 09:31:56
categories: C#
---
&emsp;&emsp;在`C#`中，结构体是值类型数据结构。它使得一个单一变量可以存储各种数据类型的相关数据。`struct`关键字用于创建结构体。<!--more-->
&emsp;&emsp;结构体是用来代表一个记录。假设你想跟踪图书馆中书的动态，可能需要跟踪每本书的以下属性：

- `Title`
- `Author`
- `Subject`
- `Book ID`

### 定义结构体

&emsp;&emsp;为了定义一个结构体，你必须使用`struct`语句。例如，你可以按照如下的方式声明`Book`结构：

``` cs
struct Books {
    public string title;
    public string author;
    public string subject;
    public int book_id;
};
```

下面的程序演示了结构的用法：

``` cs
using System;

struct Books {
    public string title;
    public string author;
    public string subject;
    public int book_id;
};

public class testStructure {
    public static void Main(string[] args) {
        Books Book1; /* 声明变量Book1，类型为Book */

        Book1.title = "C Programming";
        Book1.author = "Nuha Ali";
        Book1.subject = "C Programming Tutorial";
        Book1.book_id = 6495407;

        Console.WriteLine("Book 1 title : {0}", Book1.title);
        Console.WriteLine("Book 1 author : {0}", Book1.author);
        Console.WriteLine("Book 1 subject : {0}", Book1.subject);
        Console.WriteLine("Book 1 book_id :{0}", Book1.book_id);
    }
}
```

执行结果：

``` cs
Book 1 title : C Programming
Book 1 author : Nuha Ali
Book 1 subject : C Programming Tutorial
Book 1 book_id : 6495407
```

### C#结构的特点

&emsp;&emsp;`C#`中的结构`C/C++`中的结构不同，它有以下特点：

- 结构可带有`方法`、`字段`、`索引`、`属性`、`运算符方法`和`事件`。
- 结构可定义构造函数，但不能定义析构函数。但是，你不能为结构定义无参构造函数，它是自动定义的，且不能被改变。
- 与类不同，结构不能继承其他的结构或类。并且，结构不能作为其他结构或类的基础结构。
- 结构可实现一个或多个接口。
- 结构成员不能指定为`abstract`、`virtual`或`protected`。
- 当你使用`New`操作符创建一个结构对象时，会调用适当的构造函数来创建结构。与类不同，结构可以不使用`New`操作符即可被实例化。

### 类 vs 结构

&emsp;&emsp;类和结构有以下几个基本的不同点：

- 类是引用类型，结构是值类型。
- 结构不支持继承。
- 结构不能声明默认的构造函数。

``` cs
using System;
using System.Text;

struct Books {
    private string title;
    private string author;
    private string subject;
    private int book_id;

    public void getValues(string t, string a, string s, int id) {
        title = t;
        author = a;
        subject = s;
        book_id = id;
    }

    public void display() {
        Console.WriteLine("Title : {0}", title);
        Console.WriteLine("Author : {0}", author);
        Console.WriteLine("Subject : {0}", subject);
        Console.WriteLine("Book_id : {0}", book_id);
    }
};

public class testStructure {
    public static void Main(string[] args) {
        Books Book1 = new Books(); /* 声明Book1，类型为Book */
        Book1.getValues(
            "C Programming", "Nuha Ali",
            "C Programming Tutorial", 6495407);
        Book1.display();
    }
}
```

执行结果：

``` cs
Title : C Programming
Author : Nuha Ali
Subject : C Programming Tutorial
Book_id : 6495407
```