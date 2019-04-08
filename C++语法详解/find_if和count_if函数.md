---
title: find_if和count_if函数
date: 2019-02-05 21:45:44
tags:
---
&emsp;&emsp;It is a function template and include in the header file `algorithm`.

### std::find_if

``` cpp
template <class InputIterator, class UnaryPredicate>
InputIterator find_if ( InputIterator first, InputIterator last, UnaryPredicate pred );
```

&emsp;&emsp;It can find element in range and return an iterator to the first element in the range `[first, last)` for which `pred` returns `true`. If no such element is found, the function returns last. The behavior of this function template is equivalent to:

``` cpp
template<class InputIterator, class UnaryPredicate>
InputIterator find_if ( InputIterator first, InputIterator last, UnaryPredicate pred ) {
    while ( first != last ) {
        if ( pred ( *first ) ) {
            return first;
        }
​
        ++first;
    }
​
    return last;
}
```

Example:

``` cpp
#include <iostream>
#include <algorithm>
#include <vector>
​
bool IsOdd ( int i ) {
    return ( ( i % 2 ) == 1 );
}
​
int main () {
    std::vector<int> myvector;
    myvector.push_back ( 10 );
    myvector.push_back ( 25 );
    myvector.push_back ( 40 );
    myvector.push_back ( 55 );
    std::vector<int>::iterator it = std::find_if ( myvector.begin(), myvector.end(), IsOdd );
    std::cout << "The first odd value is " << *it << '\n';
    return 0;
}
```

Output: `The first odd value is 25`.

---

### std::count_if

``` cpp
template <class InputIterator, class UnaryPredicate>
typename iterator_traits<InputIterator>::difference_type
count_if ( InputIterator first, InputIterator last, UnaryPredicate pred );
```

it returns the number of elements in the range `[first, last)` for which `pred` is `true`.
&emsp;&emsp;The behavior of this function template is equivalent to:

``` cpp
template <class InputIterator, class UnaryPredicate>
typename iterator_traits<InputIterator>::difference_type
count_if ( InputIterator first, InputIterator last, UnaryPredicate pred ) {
    typename iterator_traits<InputIterator>::difference_type ret = 0;
​
    while ( first != last ) {
        if ( pred ( *first ) ) {
            ++ret;
        }
​
        ++first;
    }
​
    return ret;
}
```

Example one:

``` cpp
#include <iostream>
#include <algorithm>
#include <vector>
​
bool IsOdd ( int i ) {
    return ( ( i % 2 ) == 1 );
}
​
int main () {
    std::vector<int> myvector;
​
    for ( int i = 1; i < 10; i++ ) {
        myvector.push_back ( i ); /* myvector: 1 2 3 4 5 6 7 8 9 */
    }
​
    int mycount = count_if ( myvector.begin(), myvector.end(), IsOdd );
    std::cout << "myvector contains " << mycount  << " odd values\n";
    return 0;
}
```

Output: `myvector contains 5 odd values`. Example two:

``` cpp
bool gt_2 ( const pair<const string,  int> &r ) {
    return r.second > 2;
}
​
void f ( map<string , int> &m ) { /* 找到第一个大于2的值 */
    typedef map<string, int>::const_iterator M1;
    M1 i = find_if ( m.begin(), m.end(), gt_2 );
    cout << ( *i ).first << " " << ( *i ).second << endl;
}
​
void g ( map<string , int> &m ) { /* 统计频率高于2的个数 */
    int c2 = count_if ( m.begin(), m.end(), gt_2 );
    cout << c2 << endl;
}
```