---
title: GetMemory错误讲解
date: 2018-12-06 14:54:53
categories: C语言语法详解
---
&emsp;&emsp;该博客从网上整理了GetMemory错误有关的内容，感谢分享资料的人们！
&emsp;&emsp;错误程序如下所示：

``` c
void GetMemory ( char* p ) {
    p = ( char* ) malloc ( 100 );
}

void Test ( void ) {
    char* str = NULL;
    GetMemory ( str );
    strcpy ( str, "hello world" );
    printf ( "%s", str );
}
```

这是一个考验对指针理解的题目，上面的程序在运行之后：调用GetMemory(str)后，str并未产生变化，依然是NULL；`strcpy(str, "hello world");`程序运行到这里将产生错误；使用malloc可能会出错，应该在后面判断内存是否申请成功；动态创建的内存没释放。修改方法如下所示：

``` c
void GetMemory ( char** p ) {
    *p = ( char* ) malloc ( 100 );
}

void Test ( void ) {
    char* str = NULL;
    GetMemory = ( &str );
    strcpy ( str, "hello world" );
    printf ( str );
}
```

&emsp;&emsp;错误程序如下所示：

``` c
char* GetMemory ( void ) {
    char p[] = "hello world";
    return p;
}

void Test ( void ) {
    char* str = NULL;
    str = GetMemory();
    printf ( str );
}
```

上述代码中的p数组为函数的局部自动变量，在函数返回后，内存已经被释放。这是许多程序员常犯的错误，其根源在于不理解变量的生存期。