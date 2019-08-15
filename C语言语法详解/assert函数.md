---
title: assert函数
date: 2018-12-10 19:23:24
categories: C语言语法详解
---
&emsp;&emsp;`assert`宏的原型如下：

``` c
#include <assert.h>
void assert ( int expression );
```

`assert`的作用是现计算表达式`expression`，如果其值为`假`(即为`0`)，那么它先向`stderr`打印一条出错信息，然后通过调用`abort`来终止程序运行。

``` cpp
#include <stdio.h>
#include <assert.h>
#include <stdlib.h>

int main ( void ) {
    FILE* fp;
    /* 以可写的方式打开一个文件，如果不存在就创建一个同名文件 */
    fp = fopen ( "test.txt", "w" );
    assert ( fp ); /* 所以这里不会出错 */
    fclose ( fp );
    /* 以只读的方式打开一个文件，如果不存在就打开文件失败 */
    fp = fopen ( "noexistfile.txt", "r" );
    assert ( fp ); /* 所以这里出错 */
    fclose ( fp ); /* 程序永远都执行不到这里来 */
    return 0;
}
```

&emsp;&emsp;频繁地调用`assert`会极大地影响程序的性能，增加额外的开销。在调试结束后，可以通过在包含`#include <assert.h>`的语句之前插入`#define NDEBUG`来禁用`assert`调用，示例代码如下：

``` cpp
#include <stdio.h>
#define NDEBUG
#include <assert.h>
```

&emsp;&emsp;用法总结与注意事项：

- 在函数开始处检验传入参数的合法性：

``` cpp
int resetBufferSize ( int nNewSize ) {
    assert ( nNewSize >= 0 );
    assert ( nNewSize <= MAX_BUFFER_SIZE );
    ...
}
```

- 每个`assert`只检验一个条件，因为当同时检验多个条件时，如果断言失败，无法直观的判断是哪个条件失败。不合适的用法：

``` cpp
assert ( nOffset >= 0 && nOffset + nSize <= m_nInfomationSize );
```

合适的用法：

``` cpp
assert ( nOffset >= 0 );
assert ( nOffset + nSize <= m_nInfomationSize );
```

- 不能使用改变环境的语句，因为`assert`只在`DEBUG`中生效。如果这么做，会使程序在真正运行时遇到问题：

``` cpp
assert ( i++ < 100 );
```

这是因为如果出错，比如在执行之前`i = 100`，那么这条语句就不会执行，`i++`这条命令就没有执行。正确的用法如下：

``` c
assert ( i < 100 );
i++;
```

- `assert`和后面的语句应空一行，以形成逻辑和视觉上的一致感。
- 在有些地方`assert`不能代替条件过滤。