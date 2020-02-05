---
title: mem_fun和mem_fun_ref函数
categories: C++语法详解
abbrlink: 8d3abb4c
date: 2019-02-05 19:53:25
---
&emsp;&emsp;怎么对容器中的所有对象都进行同一个操作？我们可能首先想到的是用循环来实现。比如有如下的一个类：<!--more-->

``` cpp
class ClxECS {
  public:
    int DoSomething() {
        cout << "Output from method DoSomething!" << endl;
        return 0;
    };
};
```

现在定义如下一个`vector`：

``` cpp
vector<ClxECS *> vECS;
​
for ( int i = 0; i < 13; i++ ) {
    ClxECS *pECS = new ClxECS;
    vECS.push_back ( pECS );
}
```

如果要对容器`vECS`中的所有对象都进行`DoSomething`的操作，那么下面的循环可能是首先想到的方案：

``` cpp
for ( int i = 0; i < vECS.size(); i++ ) {
    vECS.at ( i )->DoSomething();
}
```

当然我们也可以用`iterator`：

``` cpp
for ( vector<ClxECS *>::iterator it = vECS.begin(); it != vECS.end(); ++it ) {
    ( *it )->DoSomething();
}
```

但有很多`C++`高手都会给我们一个忠告，那就是`在处理STL里面的容器的时候，尽量不要自己写循环`，那么我们就只好用`STL`算法里面的`for_each`了。首先添加如下函数：

``` cpp
int DoSomething ( ClxECS *pECS ) {
    return pECS->DoSomething();
}
```

然后就可以用`for_each`来实现我们想要的功能：

``` cpp
for_each ( vECS.begin(), vECS.end(), &DoSomething );
```

在使用`for_each`时，如果不添加上面的那个函数，该怎么办呢？这个时候就该`mem_fun`和`mem_fun_ref`隆重登场了。使用如下这行代码就行了：

``` cpp
for_each ( vECS.begin(), vECS.end(), mem_fun ( &ClxECS::DoSomething ) );
```

实际上就是由迭代器去调用成员函数。代码示例如下：

``` cpp
list<Widget *> lpw;
for_each ( lpw.begin(), lpw.end(), mem_fun ( &Widget::test ) ); /* pw->test(); */
vector<Widget> vw;
for_each ( vw.begin(), vw.end(), mem_fun_ref ( &Widget::test ) ); /* w.test(); */
```

&emsp;&emsp;当成员函数有参数时，先将值传入，再`bind1st`为`this`：

``` cpp
#include <iostream>
#include <vector>
#include <functional>
#include <algorithm>
#include <string>
​
using namespace std;
​
class Book {
  public:
    string m_strName;
​
    Book ( string name ) : m_strName ( name ) {
    }
​
    void SayHello() {
        cout << "Hello " << m_strName << endl;
    }
};
​
class CBookEditDlg {
  public:
    vector<Book> m_vecBooks;
    void ForEachBookFunctor ( Book book );
    void PubBookInfo();
};
​
void CBookEditDlg::ForEachBookFunctor ( Book book ) {
    cout << book.m_strName << endl;
}
​
void CBookEditDlg::PubBookInfo() {
    /* 下面这两句起的作用是一样的，至于为什么一样，很值得仔细揣摩 */
    for_each ( m_vecBooks.begin(), m_vecBooks.end(), mem_fun_ref ( &Book::SayHello ) );
    for_each ( m_vecBooks.begin(), m_vecBooks.end(), std::bind1st ( mem_fun ( &CBookEditDlg::ForEachBookFunctor ), this ) );
}
​
int main() {
    Book book1 ( "Effective C++" );
    Book book2 ( "More Effective C++" );
    CBookEditDlg books;
    books.m_vecBooks.push_back ( book1 );
    books.m_vecBooks.push_back ( book2 );
    books.PubBookInfo();
    return 0;
}
```

&emsp;&emsp;`mem_fun_ref`的作用和用法跟`mem_fun`一样，唯一的不同就是：当容器中存放的是对象实体的时候用`mem_fun_ref`，当容器中存放的是对象的指针的时候用`mem_fun`。