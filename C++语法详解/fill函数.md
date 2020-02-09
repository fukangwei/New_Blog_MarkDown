---
title: fill函数
categories: C++语法详解
abbrlink: c48ea9ee
date: 2018-12-27 10:16:53
---
&emsp;&emsp;This is a function template, int the head file `<algorithm>`:<!--more-->

``` cpp
template <class ForwardIterator, class T>
void fill ( ForwardIterator first, ForwardIterator last, const T &val );
```

Assigns val to all the elements in the range `[first, last)`. The behavior of this function template is equivalent to:

``` cpp
template <class ForwardIterator, class T>
void fill ( ForwardIterator first, ForwardIterator last, const T &val ) {
    while ( first != last ) {
        *first = val;
        ++first;
    }
}
```

- `first`, `last`: Forward iterators to the initial and final positions in a sequence of elements that support being assigned a value of type `T`. The range filled is `[first, last)`, which contains all the elements between `first` and `last`, including the element pointed by `first` but not the element pointed by `last`.
- `val`: Value to assign to the elements in the filled range.

&emsp;&emsp;Example:

``` cpp
#include <iostream> /* std::cout */
#include <algorithm> /* std::fill */
#include <vector> /* std::vector */
​
int main () {
    std::vector<int> myvector ( 8 ); /* myvector: 0 0 0 0 0 0 0 0 */
    std::fill ( myvector.begin(), myvector.begin() + 4, 5 ); /* myvector: 5 5 5 5 0 0 0 0 */
    std::fill ( myvector.begin() + 3, myvector.end() - 2, 8 ); /* myvector: 5 5 5 8 8 8 0 0 */
    std::cout << "myvector contains:";
​
    for ( std::vector<int>::iterator it = myvector.begin(); it != myvector.end(); ++it ) {
        std::cout << ' ' << *it;
    }
​
    std::cout << '\n';
    return 0;
}
```