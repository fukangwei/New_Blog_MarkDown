---
title: reverse函数
date: 2018-12-27 10:45:07
categories: C++语法详解
---
&emsp;&emsp;This is a function template, in the header file `algorithm`.

``` cpp
template <class BidirectionalIterator>
void reverse ( BidirectionalIterator first, BidirectionalIterator last );
```

Reverses the order of the elements in the range `[first, last)`.
&emsp;&emsp;The function calls `iter_swap` to swap the elements to their new locations. The behavior of this function template is equivalent to:

``` cpp
template <class BidirectionalIterator>
void reverse ( BidirectionalIterator first, BidirectionalIterator last ) {
    while ( ( first != last ) && ( first != --last ) ) {
        std::iter_swap ( first, last );
        ++first;
    }
}
```

&emsp;&emsp;Parameters: `first`, `last`: Bidirectional iterators to the initial and final positions of the sequence to be reversed. The range used is `[first, last)`, which contains all the elements between `first` and `last`, including the element pointed by `first` but not the element pointed by `last`.
&emsp;&emsp;`BidirectionalIterator` shall point to a type for which swap is properly defined.

``` cpp
#include <iostream> /* std::cout */
#include <algorithm> /* std::reverse */
#include <vector> /* std::vector */
#include <cstdlib>
​
int main() {
    std::vector<int> myvector;
​
    for (int i = 1; i < 10; ++i) { /* set some values */
        myvector.push_back(i); /* 1 2 3 4 5 6 7 8 9 */
    }
​
    std::reverse(myvector.begin(), myvector.end()); /* 9 8 7 6 5 4 3 2 1 */
    /* print out content */
    std::cout << "myvector contains:";
​
    for (std::vector<int>::iterator it = myvector.begin(); it != myvector.end(); ++it) {
        std::cout << ' ' << *it;
    }
​
    std::cout << '\n';
    system("pause");
    return 0;
}
```