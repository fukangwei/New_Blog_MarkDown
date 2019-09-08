---
title: C程序设计语言代码
date: 2019-02-04 12:50:33
categories: C语言应用代码
---
### 字符出现频率的直方图

&emsp;&emsp;代码如下：

``` cpp
#include <stdio.h>
#include <ctype.h>
​
#define MAXHIST 15 /* max length of histogram */
#define MAXCHAR 128 /* max different characters */
​
/* Print horizontal histogram freq. of different characters */
int main ( void ) {
    int i, c;
    int len; /* length of each bar */
    int maxvalue; /* maximum value for cc[] */
    int cc[MAXCHAR]; /* character counters */
​
    for ( i = 0; i < MAXCHAR; i++ ) {
        cc[i] = 0;
    }
​
    while ( ( c = getchar() ) != EOF ) {
        if ( c < MAXCHAR ) {
            ++cc[c]; /* 对应字符的个数 */
        }
    }
​
    maxvalue = 0; /* find the maximum value in cc[MAXCHAR] */
​
    for ( i = 1; i < MAXCHAR; i++ ) { /* wl[0]未使用(单词的长度大于0) */
        if ( cc[i] > maxvalue ) {
            maxvalue = cc[i];
        }
    }
​
    for ( i = 1; i < MAXCHAR; ++i ) { /* 输出直方图(水平) */
        if ( isprint ( i ) ) {
            printf ( "%5d - %c - %2d : ", i, i, cc[i] );
        } else {
            printf ( "%5d -   - %2d : ", i, cc[i] );
        }
​
        if ( cc[i] > 0 ) {
            if ( ( len = cc[i] * MAXHIST / maxvalue ) <= 0 ) {
                len = 1;
            }
        } else {
            len = 0;
        }
​
        while ( len > 0 ) {
            putchar ( '*' );
            --len;
        }
​
        putchar ( '\n' );
    }
​
    return 0;
}
```

### 单词长度的垂直直方图

&emsp;&emsp;代码如下：

``` cpp
#include <stdio.h>
​
#define MAXHIST 20 /* max length of histogram */
#define MAXWORD 11 /* max length of a word */
#define IN  1 /* inside a word */
#define OUT 0 /* outside a word */
​
int main() {
    int i, j, c, state, nc;
    int maxvalue; /* maximum value for wl[] */
    int ovflow; /* number of overflow words */
    int wl[MAXWORD]; /* word length counters */
    state = OUT;
    nc = 0; /* number of chars in a word */
    ovflow = 0; /* number of words >= MAXWORD */
​
    for ( i = 0; i < MAXWORD; i++ ) {
        wl[i] = 0;
    }
​
    while ( ( c = getchar() ) != EOF ) {
        if ( c == ' ' || c == '\t' || c == '\n' ) {
            state = OUT; /* 在单词外，标志着一个单词的结束 */
​
            if ( nc > 0 ) {
                if ( nc < MAXWORD ) {
                    ++wl[nc];
                } else {
                    ++ovflow;
                }
​
                nc = 0; /* 准备计数下一个单词的字符数 */
            }
        } else if ( state == OUT ) { /* 出现某个单词的首字符 */
            state = IN;
            nc = 1; /* beginning of a new word */
        } else { /* 单词除过首字符的其他字符 */
            ++nc; /* inside a word */
        }
    }
​
    maxvalue = 0; /* find the maximum value in wl[MAXWORD] */
​
    for ( i = 1; i < MAXWORD; i++ ) { /* wl[0]未使用(单词的长度大于0) */
        if ( wl[i] > maxvalue ) {
            maxvalue = wl[i];
        }
    }
​
    for ( i = MAXHIST; i > 0; i-- ) { /* 输出直方图(垂直) */
        for ( j = 1; j < MAXWORD; j++ ) {
            if ( wl[j] * MAXHIST / maxvalue >= i ) {
                printf ( " * " );
            } else {
                printf ( "   " );
            }
        }
​
        putchar ( '\n' );
    }
​
    for ( i = 1; i < MAXWORD; i++ ) {
        printf ( "%2d ", i );
    }
​
    putchar ( '\n' );
​
    for ( i = 1; i < MAXWORD; i++ ) {
        printf ( "%2d ", wl[i] );
    }
​
    putchar ( '\n' );
​
    if ( ovflow > 0 ) { /* 是否有长度超出最大单词长度的单词 */
        printf ( "There are %d words >= %d/n", ovflow, MAXWORD );
    }
​
    return 0;
}
```

### 单词长度的水平直方图

&emsp;&emsp;空格、制表符或换行符标志着单词的结束。如果有一个单词(`nc > 0`)且它的长度小于允许的单词最大长度(`nc < MAXWORD`)，这个程序将对相应的单词长度计数器加`1`(`++wl[nc]`)。如果单词的长度超出了允许的单词最大长度(`nc >= MAXWORD`)，这个程序将对变量`ovflow`加`1`以记录长度大于或等于`MAXWORD`的单词的个数。

``` cpp
#include <stdio.h>
​
#define MAXHIST 15 /* max length of histogram */
#define MAXWORD 11 /* max length of a word */
#define IN 1 /* inside a word */
#define OUT 0 /* outside a word */
​
int main ( void ) {
    int i, c, state, nc;
    int len; /* length of each bar */
    int maxvalue; /* maximum value for wl[] */
    int ovflow; /* number of overflow words */
    int wl[MAXWORD]; /* word length counters */
    state = OUT;
    nc = 0; /* number of chars in a word */
    ovflow = 0; /* number of words >= MAXWORD */
​
    for ( i = 0; i < MAXWORD; i++ ) {
        wl[i] = 0;
    }
​
    while ( ( c = getchar() ) != EOF ) {
        if ( c == ' ' || c == '\t' || c == '\n' ) {
            state = OUT; /* 在单词外，标志着一个单词的结束 */
​
            if ( nc > 0 ) {
                if ( nc < MAXWORD ) {
                    ++wl[nc];
                } else {
                    ++ovflow;
                }
​
                nc = 0; /* 准备计数下一个单词的字符数 */
            }
        } else if ( state == OUT ) { /* 出现某个单词的首字符 */
            state = IN;
            nc = 1; /* beginning of a new word */
        } else { /* 单词除过首字符的其他字符 */
            ++nc; /* inside a word */
        }
    }
​
    maxvalue = 0; /* find the maximum value in wl[MAXWORD] */
​
    for ( i = 1; i < MAXWORD; i++ ) { /* wl[0]未使用(单词的长度大于0) */
        if ( wl[i] > maxvalue ) {
            maxvalue = wl[i];
        }
    }
​
    for ( i = 1; i < MAXWORD; i++ ) { /* 计算对应nc的直方图长度，并输出直方图 */
        printf ( "%5d - %5d : ", i, wl[i] );
​
        if ( wl[i] > 0 ) {
            /* 利用下面的公式计算长度，最小长度为1 */
            if ( ( len = wl[i] * MAXHIST / maxvalue ) <= 0 ) {
                len = 1;
            }
        } else {
            len = 0;
        }
​
        while ( len > 0 ) { /* 输出直方图 */
            putchar ( '*' );
            --len;
        }
​
        putchar ( '\n' );
    }
​
    if ( ovflow > 0 ) { /* 是否有长度超出最大单词长度的单词 */
        printf ( "There are %d words >= %d\n", ovflow, MAXWORD );
    }
​
    return 0;
}
```

### 替换制表符为空白符

&emsp;&emsp;代码如下：

``` cpp
#include <stdio.h>
​
#define TABINC 4 /* tab length */
​
int main ( void ) {
    int c, nb, pos;
    nb = 0; /* number of blanks necessary */
    pos = 1; /* position of character in line */
​
    while ( ( c = getchar() ) != EOF ) {
        if ( c == '\t' ) { /* tab character */
            nb = TABINC - ( pos - 1 ) % TABINC;
​
            while ( nb > 0 ) {
                putchar ( ' ' );
                ++pos;
                --nb;
            }
        } else if ( c == '\n' ) { /* new line character */
            putchar ( c );
            pos = 1;
        } else { /* other character */
            putchar ( c );
            ++pos;
        }
    }
​
    return 0;
}
```

### 打印最长的文本行

&emsp;&emsp;读入一组文本行，并把最长的文本行打印出来：

``` cpp
#include <stdio.h>
​
#define MAXLINE 1000 /* maximum input line length */
​
int mygetline ( char line[], int maxline );
void copy ( char to[], char from[] );
​
/* print the longest input line */
int main ( void ) {
    int len; /* current line length */
    int max; /* maximum length seen so far */
    char line[MAXLINE]; /* current input line */
    char longest[MAXLINE]; /* longest line saved here */
    max = 0;
​
    while ( ( len = mygetline ( line, MAXLINE ) ) > 0 )
        if ( len > max ) {
            max = len;
            copy ( longest, line );
        }
​
    if ( max > 0 ) { /* there was a line */
        printf ( "%s", longest );
    }
​
    return 0;
}
​
int mygetline ( char s[], int lim ) { /* read a line into s, return length */
    int c, i;
​
    for ( i = 0; i < lim - 1 && ( c = getchar() ) != EOF && c != '\n'; ++i ) {
        s[i] = c;
    }
​
    if ( c == '\n' ) {
        s[i] = c;
        ++i;
    }
​
    s[i] = '\0';
    return i;
}
​
void copy ( char to[], char from[] ) { /* 将from复制到to；这里假定to足够大 */
    int i;
    i = 0;
​
    while ( ( to[i] = from[i] ) != '\0' ) {
        ++i;
    }
}
```

### 简单计算器程序

&emsp;&emsp;该程序在每行中读取一个数(数的前面可能有正负号)，并对它们求和，在每次输入完成后把这些数的累积总和打印出来：

``` cpp
#include <ctype.h>
#include <stdio.h>
​
#define MAXLINE 100
​
double atof ( char s[] ) { /* 把字符串转换为相应的双精度浮点数 */
    double val, power;
    int i, sign;
​
    for ( i = 0; isspace ( s[i] ); i++ ) /* skip white space */
        ;
​
    sign = ( s[i] == '-' ) ? -1 : 1;
​
    if ( s[i] == '+' || s[i] == '-' ) {
        i++;
    }
​
    for ( val = 0.0; isdigit ( s[i] ); i++ ) {
        val = 10.0 * val + ( s[i] - '0' );
    }
​
    if ( s[i] == '.' ) {
        i++;
    }
​
    for ( power = 1.0; isdigit ( s[i] ); i++ ) {
        val = 10.0 * val + ( s[i] - '0' );
        power *= 10;
    }
​
    return sign * val / power;
}
​
int mygetline ( char s[], int lim ) { /* read a line into s, return length */
    int c, i;
​
    for ( i = 0; i < lim - 1 && ( c = getchar() ) != EOF && c != '\n'; ++i ) {
        s[i] = c;
    }
​
    if ( c == '\n' ) {
        s[i] = c;
        ++i;
    }
​
    s[i] = '\0';
    return i;
}
​
int main ( void ) {
    double sum;
    char line[MAXLINE];
    sum = 0;
​
    while ( mygetline ( line, MAXLINE ) > 0 ) {
        printf ( "The result is %g\n", sum += atof ( line ) );
    }
​
    return 0;
}
```

### 删除多余的空白

&emsp;&emsp;删除行末的空格及制表符，并删除全是空格的行：

``` cpp
#include <stdio.h>
​
#define MAXLINE 1000 /* maximum input line length */
​
int mygetline ( char line[], int maxline );
int myremove ( char s[] );
​
int main ( void ) {
    int len; /* current line length */
    int length; /* remove returns length */
    char line[MAXLINE]; /* current input line */
​
    while ( ( len = mygetline ( line, MAXLINE ) ) > 0 ) {
        if ( ( length = myremove ( line ) ) > 0 ) {
            printf ( "%s", line );
        }
    }
​
    return 0;
}
​
int mygetline ( char s[], int lim ) { /* read a line into s, return length */
    int c, i;
​
    for ( i = 0; i < lim - 1 && ( c = getchar() ) != EOF && c != '\n'; ++i ) {
        s[i] = c;
    }
​
    if ( c == '\n' ) {
        s[i] = c;
        ++i;
    }
​
    s[i] = '\0';
    return i;
}
​
int myremove ( char s[] ) { /* 从字符串s中去掉结尾的空格符及制表符，并返回处理后的字符串长度 */
    int i;
    i = 0;
​
    while ( s[i] != '\n' ) { /* 到达行末 */
        ++i;
    }
​
    --i; /* 回退一个字符，当前i指向“/n”的前一个字符 */
​
    while ( i >= 0 && ( s[i] == ' ' || s[i] == '\t' ) ) {
        --i;
    }
​
    if ( i >= 0 ) { /* 至少含有一个字符 */
        ++i;
        s[i] = '\n'; /* 重新组织为一个新行 */
        ++i;
        s[i] = '\0'; /* 结束存储的字符串 */
    }
​
    return i;
}
```

### 替换空格串

&emsp;&emsp;将空格串替换为最少数量的制表符和空格，单词间距不变：

``` cpp
#include <stdio.h>
​
#define TABINC 4 /* tab increment size */
​
/* replace strings of blanks with tabs and blanks */
int main ( void ) {
    int c, nb, nt, pos;
    nb = 0;  /* number of blanks */
    nt = 0;  /* number of tabs */
​
    for ( pos = 1; ( c = getchar() ) != EOF; pos++ ) {
        if ( c == ' ' ) {
            if ( pos % TABINC != 0 ) {
                nb++; /* increment of blanks */
            } else {
                nb = 0; /* reset of blanks */
                nt++; /* one more tab */
            }
        } else {
            for ( ; nt > 0; nt-- ) {
                putchar ( '\t' ); /* output tab(s) */
            }
​
            if ( c == '\t' ) { /* forget the blank(s) */
                nb = 0;
            } else { /* output blank(s) */
                for ( ; nb > 0; nb-- ) {
                    putchar ( ' ' );
                }
            }
​
            putchar ( c );
​
            if ( c == '\n' ) {
                pos = 0;
            } else if ( c == '\t' ) {
                pos = pos + ( TABINC - ( pos - 1 ) % TABINC ) - 1;
            }
        }
    }
​
    return 0;
}
```

程序将多于`4`个的空格用`Tab`代替。输入`hello[][][][][][][][][]world!`(`[]`代表一个空格)，输出`hello[    ][    ][]world!`(`[    ]`代表一个`Tab`)。