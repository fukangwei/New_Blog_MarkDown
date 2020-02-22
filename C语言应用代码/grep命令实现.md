---
title: grep命令实现
categories: C语言应用代码
date: 2018-12-26 21:25:47
---
&emsp;&emsp;代码如下：<!--more-->

``` cpp
#include <stdio.h>

#define MAXLINE 1000 /* maximum input line length */

int mygetline ( char line[], int max );
int strindex ( char source[], char searchfor[] );
char pattern[] = "ould"; /* pattern to search for */

/* find all lines matching pattern */
int main() {
    char line[MAXLINE];
    int found = 0;

    while ( mygetline ( line, MAXLINE ) > 0 )
        if ( strindex ( line, pattern ) >= 0 ) {
            printf ( "%s", line );
            found++;
        }

    return found;
}

int mygetline ( char s[], int lim ) { /* get line into s, return length */
    int c, i;
    i = 0;

    while ( --lim > 0 && ( c = getchar() ) != EOF && c != '\n' ) {
        s[i++] = c;
    }

    if ( c == '\n' ) {
        s[i++] = c;
    }

    s[i] = '\0';
    return i;
}

int strindex ( char s[], char t[] ) { /* 返回t在s中的位置，若未找到则返回“-1” */
    int i, j, k;

    for ( i = 0; s[i] != '\0'; i++ ) {
        for ( j = i, k = 0; t[k] != '\0' && s[j] == t[k]; j++, k++ )
            ;

        if ( k > 0 && t[k] == '\0' ) {
            return i;
        }
    }

    return -1;
}
```