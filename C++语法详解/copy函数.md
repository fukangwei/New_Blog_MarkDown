---
title: copy函数
categories: C++语法详解
abbrlink: 4f4f8c2d
date: 2019-02-05 18:23:20
---
&emsp;&emsp;It is a function template and it's included in `algorithm`:

### std::copy

``` cpp
template <class InputIterator, class OutputIterator>
OutputIterator copy ( InputIterator first, InputIterator last, OutputIterator result );
```

### Copy range of elements

&emsp;&emsp;Copies the elements in the range `[first, last)` into the range beginning at result.
&emsp;&emsp;The function returns an iterator to the end of the destination range (which points to the element following the last element copied).
&emsp;&emsp;The behavior of this function template is equivalent to:

``` cpp
template<class InputIterator, class OutputIterator>
OutputIterator copy ( InputIterator first, InputIterator last, OutputIterator result ) {
    while ( first != last ) {
        *result = *first;
        ++result;
        ++first;
    }
​
    return result;
}
```

- `first`, `last`: Input iterators to the initial and final positions in a sequence to be copied. The range used is `[first, last)`, which contains all the elements between `first` and `last`, including the element pointed by `first` but not the element pointed by `last`.
- `result`: Output iterator to the initial position in the destination sequence. This shall not point to any element in the range `[first, last)`.

### Example

&emsp;&emsp;The example is:

``` cpp
#include <iostream>  // std::cout
#include <algorithm> // std::copy
#include <vector>    // std::vector
​
int main () {
    int myints[] = {10, 20, 30, 40, 50, 60, 70};
    std::vector<int> myvector ( 7 );
    std::copy ( myints, myints + 7, myvector.begin() );
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

Output:

``` cpp
myvector contains: 10 20 30 40 50 60 70
```