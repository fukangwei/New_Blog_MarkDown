---
title: back_inserter函数
date: 2018-12-27 10:35:19
categories: C++语法详解
---
&emsp;&emsp;It is a function template, in the header file `<iterator>`.

``` cpp
template <class Container>
back_insert_iterator<Container> back_inserter ( Container &x );
```

Constructs a `back-insert` iterator that inserts new elements at the end of `x`.
&emsp;&emsp;A `back-insert` iterator is a special type of output iterator designed to allow algorithms that usually overwrite elements (such as copy) to instead insert new elements automatically at the end of the container.
&emsp;&emsp;The type of `x` needs to have a `push_back` member function (such as the standard containers vector, deque and list).
&emsp;&emsp;Using the assignment operator on the returned iterator (either dereferenced or not), causes the container to expand by one element, which is initialized to the value assigned.
&emsp;&emsp;The returned iterator supports all other typical operations of output iterators but have no effect: all values assigned are inserted at the end of the container.
&emsp;&emsp;Parameters: `x` is a container on which the iterator will insert new elements. Container should be a container class with member `push_back` defined.
&emsp;&emsp;Return value: A `back_insert_iterator` that inserts elements at the end of container `x`.

``` cpp
#include <iostream>
#include <iterator>
#include <vector>
#include <algorithm>
​
int main () {
    std::vector<int> foo, bar;
​
    for ( int i = 1; i <= 5; i++ ) {
        foo.push_back ( i );
        bar.push_back ( i * 10 );
    }
​
    std::copy ( bar.begin(), bar.end(), back_inserter ( foo ) );
    std::cout << "foo contains:";
​
    for ( std::vector<int>::iterator it = foo.begin(); it != foo.end(); ++it ) {
        std::cout << ' ' << *it;
    }
​
    std::cout << '\n';
    return 0;
}
```