---
title: C结构体
categories: C语言语法详解
abbrlink: 49426
date: 2019-10-02 11:52:53
---
&emsp;&emsp;结构体是`C`语言中另一种用户自定义的可用的数据类型，它允许你存储不同类型的数据项。<!--more-->
&emsp;&emsp;结构体用于表示一条记录，假设你想要跟踪图书馆中书本的动态，可能需要跟踪每本书的下列属性：

- `Title`
- `Author`
- `Subject`
- `Book ID`

### 定义结构体

&emsp;&emsp;为了定义结构体，你必须使用`struct`语句。`struct`语句定义了一个包含多个成员的新的数据类型：

``` cpp
struct tag {
    member-list
    member-list
    member-list
    ...
} variable-list;
```

`tag`是结构体标签。`member-list`是标准的变量定义，比如`int i;`或者`float f;`，或者其他有效的变量定义。
&emsp;&emsp;`variable-list`是结构体变量，定义在结构的末尾。在最后一个分号之前，你可以指定`一个`或`多个`结构体变量。下面是声明`Book`结构体的方式：

``` cpp
struct Books {
    char title[50];
    char author[50];
    char subject[100];
    int  book_id;
} book;
```

&emsp;&emsp;结构体的成员可以包含其他结构体，也可以包含指向自己结构体类型的指针，通常这种指针的应用是为了实现一些更高级的数据结构，例如链表和树：

``` cpp
struct SIMPLE {
    int a;
    char b;
    double c;
};

/* 此结构体包含了其他的结构体 */
struct COMPLEX {
    char string[100];
    struct SIMPLE a;
};

/* 此结构体包含了指向自己类型的指针 */
struct NODE {
    char string[100];
    struct NODE *next_node;
};
```

如果两个结构体互相包含，则需要对其中一个结构体进行不完整声明：

``` cpp
struct B; /* 对结构体B进行不完整声明 */

/* 结构体A中包含指向结构体B的指针 */
struct A {
    struct B *partner;
    // other members;
};

/* 结构体B中包含指向结构体A的指针，在A声明完后，B也随之进行声明 */
struct B {
    struct A *partner;
    // other members;
};
```

### 结构体变量的初始化

&emsp;&emsp;和其它类型变量一样，对结构体变量可以在定义时指定初始值：

``` cpp
#include <stdio.h>

struct Books {
    char title[50];
    char author[50];
    char subject[100];
    int  book_id;
} book = { "C语言", "RUNOOB", "编程语言", 123456 };
```

### 访问结构成员

&emsp;&emsp;为了访问结构体中的成员，可以使用成员访问运算符`.`：

``` cpp
#include <stdio.h>

struct Books {
    char title[50];
    char author[50];
    char subject[100];
    int  book_id;
} book = { "C语言", "RUNOOB", "编程语言", 123456 };

int main() {
    printf ( "title: %s\nauthor: %s\nsubject: %s\nbook_id: %d\n", \
             book.title, book.author, book.subject, book.book_id );
}
```

执行结果：

``` cpp
title: C语言
author: RUNOOB
subject: 编程语言
book_id: 123456
```

### 结构作为函数参数

&emsp;&emsp;可以把结构体作为函数参数，传参方式与其他类型的变量类似：

``` cpp
#include <stdio.h>
#include <string.h>

struct Books {
    char title[50];
    char author[50];
    char subject[100];
    int  book_id;
};

void printBook ( struct Books book );

int main() {
    struct Books Book1; /* 定义Book1，类型为Books */

    strcpy ( Book1.title, "C Programming" );
    strcpy ( Book1.author, "Nuha Ali" );
    strcpy ( Book1.subject, "C Programming Tutorial" );
    Book1.book_id = 6495407;

    printBook ( Book1 ); /* 输出Book1信息 */
    return 0;
}

void printBook ( struct Books book ) {
    printf ( "Book title : %s\n", book.title );
    printf ( "Book author : %s\n", book.author );
    printf ( "Book subject : %s\n", book.subject );
    printf ( "Book book_id : %d\n", book.book_id );
}
```

执行结果：

``` cpp
Book title : C Programming
Book author : Nuha Ali
Book subject : C Programming Tutorial
Book book_id : 6495407
```

### 指向结构的指针

&emsp;&emsp;你可以定义指向结构的指针，方式与定义指向其他类型变量的指针相似：

``` cpp
struct Books *struct_pointer;
```

现在就可以在上述定义的指针变量中存储结构体变量的地址。为了查找结构变量的地址，请把`&`运算符放在结构名称的前面：

``` cpp
struct_pointer = &Book1;
```

为了使用指向该结构的指针访问结构的成员，你必须使用`->`运算符：

``` cpp
struct_pointer->title;
```

使用结构指针来重写上面的实例：

``` cpp
#include <stdio.h>
#include <string.h>

struct Books {
    char title[50];
    char author[50];
    char subject[100];
    int  book_id;
};

void printBook ( struct Books* book );

int main() {
    struct Books Book1;

    strcpy ( Book1.title, "C Programming" );
    strcpy ( Book1.author, "Nuha Ali" );
    strcpy ( Book1.subject, "C Programming Tutorial" );
    Book1.book_id = 6495407;

    printBook ( &Book1 );
    return 0;
}

void printBook ( struct Books* book ) {
    printf ( "Book title : %s\n", book->title );
    printf ( "Book author : %s\n", book->author );
    printf ( "Book subject : %s\n", book->subject );
    printf ( "Book book_id : %d\n", book->book_id );
}
```

执行结果：

``` cpp
Book title : C Programming
Book author : Nuha Ali
Book subject : C Programming Tutorial
Book book_id : 6495407
```